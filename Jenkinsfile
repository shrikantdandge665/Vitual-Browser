pipeline {
    agent any
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shrikantdandge665/Vitual-Browser.git'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                script {
                    def depCheck = dependencyCheck additionalArguments: '--scan ./ --disableNodeAudit', odcInstallation: 'dc'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Sonarqube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=VirtualBrowser -Dsonar.projectName=VirtualBrowser"
                }
            }
        }

        stage('Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t shrikantdandge7/vb:latest -f .docker/firefox/Dockerfile ."
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh "trivy image shrikantdandge7/vb:latest > trivy-report.txt"
            }
        }

        stage('Docker Image Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push shrikantdandge7/vb:latest"
                    }
                }
            }
        }

        stage('Docker deploy') {
            steps {
               
                 sh "docker-compse up -d"
        
                }
            }
        }
    }
}
