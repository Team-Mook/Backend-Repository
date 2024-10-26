pipeline {
    agent any
    tools {
        gradle 'gradle'
    }

    environment {
        imagename = "normaninha/mook"
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
                COPY build/libs/mook-0.0.1-SNAPSHOT.jar app.jar
                ENTRYPOINT ["java", "-jar", "app.jar"]
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
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
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
                    sh """
                                ssh -o StrictHostKeyChecking=no ubuntu@3.39.38.199 '
                                    docker pull normaninha/mook:latest || exit 1

                                    # 기존 컨테이너가 있으면 중지 및 삭제
                                    if docker ps -a --format "{{.Names}}" | grep -q "^mook-docker-container$"; then
                                        docker rm -f mook-docker-container
                                    fi

                                    # 새로운 컨테이너 실행
                                    docker run -d --name mook-docker-container -p 8080:8080 normaninha/mook:latest
                                '
                                """
                }
            }
        }
    }
}