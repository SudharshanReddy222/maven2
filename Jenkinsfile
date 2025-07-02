pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
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
        stage("scananalysis using snyk"){
            steps{
                withCredentials([string(credentialsId: 'SNYK-token' , variable: 'SNYK-token')])
                sh 'snyk test --file=pom.xml'
            }
        }
    }
}
