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
                        ssh -o StrictHostKeyChecking=no ubuntu@10.0.1.165 uptime
                        scp /var/jenkins_home/workspace/spring-pipeline/build/libs/*.jar ubuntu@10.0.1.165:/home/ubuntu/demo
                        ssh -t ubuntu@10.0.1.165 chmod +x ./deploy.sh
                        ssh -t ubuntu@10.0.1.165 ./deploy.sh
                    '''
                }
            }
        }
    }
}