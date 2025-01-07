pipeline {
    agent any
    environment {
        // REMOTE_HOST = credentials('remote_host')
        // LINUX_SSH = credentials('agent_jenkins')
        // SSH_USER = credentials('ssh_user')
        SUDO_ASKPASS = '/var/lib/jenkins/.askpass.sh'
    }

    stages {
        stage('Install Apache2 on Remote VM') {
            steps {
                sshagent(credentials: ['agent_ssh_jenkins']) {
                    sh """
                    sudo -A apt update
                    sudo -A apt install -y apache2

                    # Start Apache2
                    sudo -A systemctl start apache2
                    sudo -A systemctl enable apache2
                    """
                }
            }
        }

        stage('Verify Apache2 Installation') {
            steps {
                sshagent (credentials: ['agent_ssh_jenkins']) {
                    sh """
                    # Check Apache2 status
                    sudo systemctl status apache2 || echo "Apache2 service is not running."
                    curl -I http://localhost || echo "Apache2 HTTP check failed."
                    """
                }
            }
        }

        stage('Check Apache2 Logs for Errors') {
            steps {
                sshagent (credentials: ['agent_ssh_jenkins']) {
                    sh """
                    # Check logs for 4xx and 5xx errors
                    cat /var/log/apache2/access.log | grep '" [45][0-9][0-9] '
                    """
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
