pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    environment {
        registry = '819590942191.dkr.ecr.us-east-1.amazonaws.com/devsecops9347'
        imageName = 'my appc'
        registryCredential = 'jenkins-ecr'
    }

    stages {
        stage('Code Quality with SonarQube') {
            steps {
                withSonarQubeEnv('sonar-9347') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN'
                    }
                }
            }
        }

        stage("Scan Analysis using Snyk") {
            steps {
                withCredentials([string(credentialsId: 'SNYK-token', variable: 'SNYK_TOKEN')]) {
                    sh 'snyk test --file=pom.xml --auth=$SNYK_TOKEN || true'
                }
            }
        }

        stage('Building our image') {
            steps {
                script {
                   def dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Deploy our image') {
            steps {
                script {
                    docker.withRegistry("http://${registry}", registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
