pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    stages {

        stage('code quality with sonarcloud') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=devsecops9347 -Dsonar.organization=devsecops9347 -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=5bdc408ef29f8dd26fe44ebb9978e9195d78edb8'
            }
        }
    }
}


    
