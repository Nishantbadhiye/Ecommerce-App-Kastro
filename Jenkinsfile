
pipeline {
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    environment {
        DOCKER_IMAGE = "nishantbadhiye07/ecommerce:latest"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git 'https://github.com/Nishantbadhiye/Ecommerce-App-Kastro.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Deploy to Container') {
            steps {
                script {
                    sh '''
                        docker rm -f ecommerce-container || true
                        docker run -d \
                            --name ecommerce-container \
                            -p 8083:8080 \
                            ${DOCKER_IMAGE}
                    '''
                }
            }
        }
    }
}
