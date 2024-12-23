def registry = 'https://index.docker.io/v1/'
def backendImage = 'srinualajangi/backend:v1'
def mysqlImage = 'srinualajangi/mysql:v1'
def frontendImage = 'srinualajangi/frontend:v1'

pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'uat', 'pre-prod', 'prod'], description: 'Select your environment')
        string(name: 'version', description: 'Enter your app version')
    }

    environment {
        appVersion = ''
        region = 'us-east-2'
        account_id = ''
        project = 'expense'
        environment = ''
        component = 'mysql'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Srinualajangi/Docker-Expense.git', branch: 'main', credentialsId: 'github-cred'
            }
        }
        stage('Setup Environment') {
            steps {
                script {
                    environment = params.ENVIRONMENT
                    appVersion = params.version
                    // account_id = pipelineGlobals.getAccountID(environment)
                }
            }
        }

        stage("Docker Build") {
            steps {
                script {
                    echo '<--------------- Docker Build Started --------------->'
                    sh """
                        cd backend
                    """
                    backendApp = docker.build(backendImage, ".")
                    sh """
                        cd mysql
                    """
                    mysqlApp = docker.build(mysqlImage, ".")
                    sh """
                        cd frontend
                    """
                    frontendApp = docker.build(frontendImage, ".")
                    echo '<--------------- Docker Build Ends --------------->'
                }
            }
        }

        stage("Docker Publish") {
            steps {
                script {
                    echo '<--------------- Docker Publish Started --------------->'
                    docker.withRegistry(registry, 'dockerhub-cred') {
                        backendApp.push()
                        mysqlApp.push()
                        frontendApp.push()
                    }
                    echo '<--------------- Docker Publish Ended --------------->'
                }
            }
        }

        stage('Integration tests') {
            when { 
                expression { params.ENVIRONMENT == "dev" }
            }
            steps {
                script {
                    sh """
                        echo "Here integration tests are going"
                    """    
                }
            }
        }

        stage('Deploy') {
            steps {
                echo '<--------------- Deploy Started --------------->'
                sh """
                    docker-compose up
                """
            }
        }
    }
}
