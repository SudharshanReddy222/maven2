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
                    // ✅ Declare variable with `def`
                    def dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                    // ✅ Save to environment for next stage
                    env.IMAGE_TAG = "${registry}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy our image') {
            steps {
                script {
                    // ✅ Push using the same tag
                    docker.withRegistry("https://${registry}", registryCredential) {
                        docker.image(env.IMAGE_TAG).push()
                    }
                }
            }
        }
    }
}
