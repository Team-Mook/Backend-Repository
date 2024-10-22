pipeline {
    agent any
    tools {
        jdk 'jdk17'
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
                withEnv(["JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64", "PATH=$JAVA_HOME/bin:$PATH"]) {
                    sh './gradlew --version'
                    sh './gradlew properties'
                    sh './gradlew clean build -Dorg.gradle.java.home=/usr/lib/jvm/java-17-openjdk-amd64'
                }
            }
        }
    }
}