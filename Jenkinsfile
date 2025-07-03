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
    }

    stages {
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
