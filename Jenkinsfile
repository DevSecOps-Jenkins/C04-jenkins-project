pipeline {
    agent any
    stages {

    parameters {
        choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Select deployment environment')
        string(name: 'APP_NAME', defaultValue: 'jenkins-demo', description: 'Application name')
    }

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