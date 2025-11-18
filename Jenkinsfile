/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent { label 'maven-agent' }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning branch: ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo "Running tests on ${env.BRANCH_NAME}"
                sh 'mvn clean test'
            }
        }

        stage('Code Analysis') {
            steps {
                echo "Running SonarQube scan on ${env.BRANCH_NAME}"
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
            when {
                anyOf {
                    branch 'main'
                    branch 'dev'
                }
            }
            steps {
                echo "Running security scan on ${env.BRANCH_NAME}"
                sh '''
                    wget https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl -O html.tpl
                    trivy fs . --format template --template "@html.tpl" -o trivy-report.html
                '''
            }
        }

        stage('Build (main only)') {
            when {
                branch 'main'
            }
            steps {
                echo "Building the application on MAIN"
                sh 'mvn clean package'
            }
        }

        stage('JFrog Upload (main only)') {
            when {
                branch 'main'
            }
            steps {
                echo "Uploading WAR to JFrog Artifactory"
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
            echo "Cleaning workspace for branch: ${env.BRANCH_NAME}"
            cleanWs()
        }
    }
}
