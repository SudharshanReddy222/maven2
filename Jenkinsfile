pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    environment {
        registry = '819590942191.dkr.ecr.us-east-1.amazonaws.com/devsecops9347'
        imageName = 'myappc'
        registryCredential = 'ecr-credentials'
    }

    stages {
        stage('Build our image') {
            steps {
                script {
                    def dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                    env.IMAGE_TAG = "${registry}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy our image') {
            steps {
                script {
                    docker.withRegistry("https://${env.registry}", env.registryCredential) {
                        docker.image(env.IMAGE_TAG).push()
                    }
                }
            }
        }
    }
}
