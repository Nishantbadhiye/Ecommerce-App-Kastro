
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
                git branch: 'master',
                    url: 'https://github.com/Nishantbadhiye/Ecommerce-App-Kastro.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

         stage('Nothing Build') {
            steps {
             }
        }


        stage('Docker Build') {
    steps {
        sh '''
            docker build -t 172.17.112.25:8082/ecommerce:${BUILD_NUMBER} .
        '''
    }
}



        stage('Deploy to Container') {
            steps {
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
