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
        stage('Trivy Scan') {
            steps {
                echo 'Performing security scan with Trivy'
                sh '''
                wget https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl -O html.tpl
                trivy fs . --format template --template "@html.tpl" -o trivy-report.html
                '''
            }
        }
        stage('Build') {
            steps {
                echo 'Building the application'
                sh 'mvn clean package'
            }
        }
        // stage('Archive') {
        //     steps {
        //         echo 'Archiving the build artifacts'
        //         archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        //     }
        // }
        stage('JFrog Upload') {
            steps {
                echo 'Uploading artifacts to JFrog Artifactory'
                rtUpload (
                    serverId: 'artifactory-server',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "target/*.war",
                                "target": "task-4/com/java-app/"
                            }
                        ]
                    }'''
                )
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
