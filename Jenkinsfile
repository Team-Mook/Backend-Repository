pipeline {
    agent any
    tools {
        gradle 'gradle'
    }

    environment {
        imagename = "normaininha/mook"
        registryCredential = 'docker-hub'
        dockerImage = ''
    }

    stages {
        stage('Clean Before Build') {
            steps {
                deleteDir() // 빌드 전에 워크스페이스 정리
            }
        }

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Team-Mook/Backend-Repository.git'
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        stage('Create Dockerfile') {
            steps {
                writeFile file: 'Dockerfile', text: """
                FROM openjdk:17-jdk-slim
                WORKDIR /app
                COPY build/libs/*.jar /app/
                ENTRYPOINT ["sh", "-c", "java -jar /app/*.jar"]
                """
            }
        }

        stage('Build Docker') {
            steps {
                echo 'Build Docker Image'
                script {
                    dockerImage = docker.build imagename
                }
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        stage('Push Docker') {
            steps {
                echo 'Push Docker Image to Docker Hub'
                script {
                    docker.withRegistry('', 'docker-hub') {
                        dockerImage.push()
                    }
                }
            }
            post {
                failure {
                    error 'This pipeline stops here...'
                }
            }
        }

        stage('Docker Run') {
            steps {
                echo 'Pull Docker Image & Run Docker Container on EC2'
                sshagent (credentials: ['deploy-ssh-key']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.39.38.199 'docker pull normaininha/mook:latest'"
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.39.38.199 'docker ps -q --filter name=mook-docker-container | grep -q . && docker rm -f \$(docker ps -aq --filter name=mook-docker-container)'"
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.39.38.199 'docker run -d --name mook-docker-container -p 8080:8080 normaninha/mook:latest'"
                }
            }
        }
    }
}