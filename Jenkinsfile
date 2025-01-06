pipeline {
    agent any
    environment {
      REMOTE_HOST = credentials('remote_host')
      LINUX_SSH = credentials('agent_jenkins')
      SSH_USER = credentials('ssh_user')
    }

    stages {
        stage('Install Apache2 on Remote VM') {
            steps {
                script {
                    sshagent (credentials: ['agent_ssh_jenkins']) {
                        sh """
                        ssh -i ${LINUX_SSH} ${SSH_USER}@${REMOTE_HOST} <<EOF
                        sudo apt update
                        sudo apt install -y apache2

                        # Запуск Apache2
                        sudo systemctl start apache2
                        sudo systemctl enable apache2
                        EOF
                        """
                    }
                }
            }
        }

        stage('Verify Apache2 Installation') {
            steps {
                script {
                    sshagent (credentials: ['agent_ssh_jenkins']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no -i ${LINUX_SSH} ${SSH_USER}@${REMOTE_HOST} <<EOF
                        # Проверка статуса Apache2
                        sudo systemctl status apache2 || echo "Apache2 service is not running."
                        curl -I http://localhost || echo "Apache2 HTTP check failed."
                        EOF
                        """
                    }
                }
            }
        }

      stage('Checking log file for 4xx and 5xx errors') {
            steps {
                script {
                    sshagent (credentials: ['agent_ssh_jenkins']) {
                        sh """
                        cat /var/log/apache2/access.log | grep '" [45][0-9][0-9] '
                        """
                    }
                }
            }
        }
    }

          stage('Verify Apache2 Installation') {
                    steps {
                        script {
                            sshagent (credentials: ['agent_ssh_jenkins']) {
                                sh """
                                ssh -o StrictHostKeyChecking=no -i ${LINUX_SSH} ${SSH_USER}@${REMOTE_HOST} <<EOF
                                # Проверка статуса Apache2
                                sudo systemctl status apache2 || echo "Apache2 service is not running."
                                curl -I http://localhost || echo "Apache2 HTTP check failed."
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

