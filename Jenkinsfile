pipeline {
    agent any
    options {
    	withAWS(profile:'default')
    }
    environment {
        PATH="/usr/local/bin:$PATH"
    }
    stages {
        stage('Build') {
            steps {
                echo "BUILD"
                // sh 'mvn package'
            }
        }
        stage('Test') {
            steps {
                echo "Test"
            }            
        }
        stage('Deliver') {
            steps {
                sh 'eb --version'
                // Create configuration template based on existing environment
                ebCreateConfigurationTemplate(
                    applicationName: "todo-rest-api",
                    templateName: "todo-rest-api-template",
                    environmentId: "e-isnpfppgpf",
                    description: "Configuration template for todo-rest-api"
                )
            }
        }
        stage('Complete') {
            steps {
                echo 'Job Complete!'
            }
        }
    }
}
