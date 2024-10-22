pipeline {
    agent any
    tools {
        gradle 'gradle'
    }
    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Team-Mook/Backend-Repository.git'
            }
        }
        stage('Build') {
            steps {
                dir("./mook") {
                    sh "./gradlew clean build"
                }
            }
        }
    }
}