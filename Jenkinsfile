def registry = 'https://index.docker.io/v1/'
def images = [
    [name: 'backend', path: 'backend', image: 'srinualajangi/backend:v1'],
    [name: 'mysql', path: 'mysql', image: 'srinualajangi/mysql:v1'],
    [name: 'frontend', path: 'frontend', image: 'srinualajangi/frontend:v1']
]

pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    environment {
        appVersion = ''
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

        stage("Docker Build and Publish") {
            steps {
                script {
                    echo '<--------------- Docker Build and Publish Started --------------->'
                    images.each { img ->
                        dir(img.path) {
                            def app = docker.build(img.image, ".")
                            docker.withRegistry(registry, 'dockerhub-cred') {
                                app.push()
                            }
                        }
                    }
                    echo '<--------------- Docker Build and Publish Ended --------------->'
                }
            }
        }

        // stage('Integration tests') {
        //     when { 
        //         expression { params.ENVIRONMENT == "dev" }
        //     }
        //     steps {
        //         script {
        //             sh """
        //                 echo "Here integration tests are going"
        //             """    
        //         }
        //     }
        // }

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
