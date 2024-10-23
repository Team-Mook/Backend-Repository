pipeline {
    agent any
    tools {
        jdk 'jdk17'
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
    }
}