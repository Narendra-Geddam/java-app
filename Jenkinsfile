/* groovylint-disable-next-line CompileStatic */
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
        stage('Coade Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                        sh '''
                            mvn verify sonar:sonar \
                            -Dsonar.projectKey=java-app \
                            -Dsonar.projectName=java-app
                        '''
                }
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
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
    }
}
