pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    stages {

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}


    
