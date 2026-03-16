pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Select deployment environment')
        string(name: 'APP_NAME', defaultValue: 'jenkins-demo', description: 'Application name')
    }

    environment {
        SERVER_IP = credentials('prod-server-ip')
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/DevSecOps-Jenkins/C04-jenkins-project.git', branch: 'main'
                sh "ls -ltr"
            }
        }

        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }

        stage('Test') {
            steps {
                sh "python3 -m pytest"
                sh "whoami"
            }
        }

        stage('Show Parameters') {
            steps {
                echo "Application Name: ${params.APP_NAME}"
                echo "Environment: ${params.ENV}"
            }
        }

        stage('Approval') {
            input {
                message "Deploy to production?"
                ok "Deploy"
            }
            steps {
                echo "Deployment approved!"
            }
        }

        stage('Use Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'app-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "Username is $USERNAME"
                    '''
                }
            }
        }

        stage('Package Code') {
            steps {
                sh "zip -r myapp.zip ./* -x '**.git**'"
                sh "ls -lart"
            }
        }

        stage('Deploy Application') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'USER')]) {
                    sh '''
                    scp -i $SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${USER}@${SERVER_IP}:/tmp/

                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${USER}@${SERVER_IP} << EOF
                    unzip -o /tmp/myapp.zip -d /var/www/html/python-app/
                    cd /var/www/html/python-app
                    source venv/bin/activate
                    pip install -r requirements.txt
                    sudo systemctl restart python-app.service
EOF
                    '''
                }
            }
        }

    }
}
