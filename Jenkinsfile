pipeline {
    agent any
    tools {
        gradle 'gradle'
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
        }
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }
        stage('deploy') {
            steps {
                sshagent(['deploy-ssh-key']) {
                    sh '''
                        echo "1"
                        ssh -o StrictHostKeyChecking=no ubuntu@3.39.38.199 uptime
                        scp ~/Desktop/study/mook/build/libs/*.jar ubuntu@3.39.38.199:/home/ubuntu/demo
                        ssh -t ubuntu@3.39.38.199 chmod +x ./deploy.sh
                        ssh -t ubuntu@3.39.38.199 ./deploy.sh
                    '''
                }
            }
        }
    }
}