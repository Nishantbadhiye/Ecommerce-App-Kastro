
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

        stage('Deploy to Nexus') {
            steps {
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'EcommerceApp',
                        classifier: '',
                        file: 'target/EcommerceApp.war',
                        type: 'war'
                    ]],
                    credentialsId: 'nexus-cred',
                    groupId: 'com',
                    nexusUrl: 'localhost:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'maven-snapshots',
                    version: "0.0.${BUILD_NUMBER}-SNAPSHOT"
                )
            }
        }


        stage('Docker Build') {
    steps {
        sh '''
            docker build -t 172.17.112.25:8082/ecommerce:${BUILD_NUMBER} .
        '''
    }
}

stage('Push Docker Image to Nexus') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'nexus-cred',
            usernameVariable: 'NEXUS_USER',
            passwordVariable: 'NEXUS_PASSWORD'
        )]) {
            sh '''
                echo "$NEXUS_PASSWORD" | docker login 172.17.112.25:8082 \
                    -u "$NEXUS_USER" --password-stdin

                docker push 172.17.112.25:8082/ecommerce:${BUILD_NUMBER}

                docker logout 172.17.112.25:8082
            '''
        }
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
