pipeline {
    agent any
    environment {
        REMOTE_HOST = credentials('remote_host')
        LINUX_SSH = credentials('agent_jenkins')
        SSH_USER = credentials('ssh_user')
    }

    stages {

        stage('Debug') {
            steps {
                script {
                    echo "REMOTE_HOST=${REMOTE_HOST}"
                    echo "LINUX_SSH=${LINUX_SSH}"
                    echo "SSH_USER=${SSH_USER}"
                }
            }
        }
        
        stage('Install Apache2 on Remote VM') {
            steps {
                sshagent(credentials: ['agent_ssh_jenkins']) {
                    sh """
                    sudo apt update
                    sudo apt install -y apache2

                    # Start Apache2
                    sudo systemctl start apache2
                    sudo systemctl enable apache2
                    """
                }
            }
        }

        stage('Verify Apache2 Installation') {
            steps {
                script {
                    ssh (credentials: ['agent_ssh_jenkins']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no -i ${LINUX_SSH} ${SSH_USER}@${REMOTE_HOST} <<EOF
                        # Check Apache2 status
                        sudo systemctl status apache2 || echo "Apache2 service is not running."
                        curl -I http://localhost || echo "Apache2 HTTP check failed."
                        EOF
                        """
                    }
                }
            }
        }

        stage('Check Apache2 Logs for Errors') {
            steps {
                script {
                    ssh (credentials: ['agent_ssh_jenkins']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no -i ${LINUX_SSH} ${SSH_USER}@${REMOTE_HOST} <<EOF
                        # Check logs for 4xx and 5xx errors
                        cat /var/log/apache2/access.log | grep '" [45][0-9][0-9] '
                        EOF
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
