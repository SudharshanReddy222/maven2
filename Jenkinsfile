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
        nexusBaseUrl = 'http://3.82.192.72:8081'
        nexusJarRepo = 'maven-releases'
        nexusZapRepo = 'zap-reports'
    }

    stages {
        stage('Code Quality with SonarQube') {
            steps {
                withSonarQubeEnv('sonar-9347') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn clean install sonar:sonar -Dsonar.login=$SONAR_TOKEN'
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

        stage('Deploy to EKS') {
            steps {
                script {
                    sh '''
                        echo "Deploying app to EKS..."
                        aws eks update-kubeconfig --region $region --name eksdemo2
                        kubectl apply -f spring-boot-deployment.yaml
                    '''
                }
            }
        }

        stage('Run ZAP Scan') {
            steps {
                script {
                    def targetUrl = 'http://3.89.201.145:30038'

                    sh '''
                        echo "Sleeping 20 seconds to wait for app startup..."
                        sleep 20
                        echo "Running ZAP Baseline Scan on ${targetUrl}"

                        docker run --rm -v $(pwd):/zap/wrk/:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
                            -t http://3.89.201.145:30038 \
                            -r zap_report.html \
                            -n -I || true
                    '''
                }
                archiveArtifacts artifacts: 'zap_report.html', allowEmptyArchive: true
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    script {
                        // Upload JAR
                        def jarFile = sh(script: "ls target/*.jar | head -1", returnStdout: true).trim()
                        sh """
                            curl -v -u $NEXUS_USER:$NEXUS_PASS --upload-file ${jarFile} \
                            ${nexusBaseUrl}/repository/${nexusJarRepo}/myapp/${BUILD_NUMBER}/myapp-${BUILD_NUMBER}.jar
                        """

                        // Upload ZAP Report
                        sh """
                            curl -v -u $NEXUS_USER:$NEXUS_PASS --upload-file zap_report.html \
                            ${nexusBaseUrl}/repository/${nexusZapRepo}/reports/zap_report_${BUILD_NUMBER}.html
                        """
                    }
                }
            }
        }
    }
}
