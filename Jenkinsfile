pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-java-app"
        IMAGE_TAG  = "v1.0"
        MAVEN_DOCKER_IMAGE = "maven:3.9.6-eclipse-temurin-17"
    }

    stages {
        stage('Checkout') {
            steps { 
                git branch: 'main', url: 'https://github.com/JeevanKumar100/java.git'
            }
        }

        stage('Build JAR (dockerized)') {
            steps {
                script {
                    // Use Maven image to build inside workspace and produce target/*.jar on host via workspace bind
                    docker.image("${MAVEN_DOCKER_IMAGE}").inside {
                        sh 'mvn -B clean package -DskipTests'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Deploy with Docker') {
            steps {
                script {
                    sh """
                    docker rm -f ${IMAGE_NAME} || true
                    docker run -d --name ${IMAGE_NAME} -p 8080:8080 ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
