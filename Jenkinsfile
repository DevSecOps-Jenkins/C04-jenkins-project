pipeline {
    agent any
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
        stage('Use Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'app-login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "Username is $USERNAME"
                        echo "Password is $PASSWORD"
                    '''
                }
            }
        }
    }
}