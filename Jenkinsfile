pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    stages {

        stage('code quality with sonarqube') {
            steps {
                script {
                    withSonarQubeEnv(installationName: 'sonar-9347' , credentialsId: 'sonar-token' )
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }
}


    
