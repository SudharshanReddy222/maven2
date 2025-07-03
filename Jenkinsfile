pipeline {
    agent any
    stages {
        stage('List Credentials') {
            steps {
                script {
                    def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                        com.cloudbees.plugins.credentials.common.StandardCredentials.class,
                        Jenkins.instance,
                        null,
                        null
                    )
                    for (c in creds) {
                        println("CRED ID: ${c.id}  - TYPE: ${c.getClass()}")
                    }
                }
            }
        }
    }
}
