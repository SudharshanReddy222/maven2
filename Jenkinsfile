pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    environment {
        registry = '819590942191.dkr.ecr.us-east-1.amazonaws.com/devsecops9347'
        imageName = 'myapp'
        region = 'us-east-1'
        awsCredentialId = 'ecr-credentials'
        sonarTokenId = 'sonar-token'
        snykTokenId = 'SNYK-token'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: "${sonarTokenId}", variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=${imageName} \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Snyk Scan') {
            steps {
                withCredentials([string(credentialsId: "${snykTokenId}", variable: 'SNYK_TOKEN')]) {
                    sh '''
                        npm install snyk -g
                        snyk auth $SNYK_TOKEN
                        snyk test || true
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def tag = "${env.registry}:${BUILD_NUMBER}"
                    docker.build(tag)
                    env.IMAGE_TAG = tag
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry("https://${env.registry}", "ecr:${env.region}:${env.awsCredentialId}") {
                        docker.image(env.IMAGE_TAG).push()
                    }
                }
            }
        }
    }
}
