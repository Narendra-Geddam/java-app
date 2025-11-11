pipeline {
    agent { label 'maven-agent' }
    stages {
        stage('Checkout') {
            steps {
                echo 'cloning the repo'
                git branch: 'main', url: 'https://github.com/Narendra-Geddam/java-app.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the application'
                sh 'mvn clean test'
            }
        }
        stage('Build') {
            steps {
                echo 'Building the application'
                sh 'mvn clean package'
            }
        }
        stage('Archive') {
            steps {
                echo 'Archiving the build artifacts'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
