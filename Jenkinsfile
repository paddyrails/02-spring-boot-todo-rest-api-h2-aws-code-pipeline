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
                sh 'mvn package'
            }
        }
        stage('Test') {
            steps {
                echo "Test"
            }            
        }
        stage('Upload artifact to s3') {
            steps {
                echo "Upload artifact to s3"
                s3Upload(file:'target/02-spring-boot-todo-rest-api-h2-continuous-deployment-pipeline-0.0.1-SNAPSHOT.jar', 
                    bucket:'codepipeline-us-east-1-19767114376', 
                    path:'02-spring-boot-todo-rest-api-h2-continuous-deployment-pipeline-0.0.1-SNAPSHOT.jar')
            }            
        }


        stage('Deliver') {
            steps {
                sh 'eb --version'
                ebCreateApplicationVersion(
                    applicationName: "todo-rest-api",
                    versionLabel: "Todo-rest-api-docker-3",
                    s3Bucket: "codepipeline-us-east-1-19767114376",
                    s3Key: "02-spring-boot-todo-rest-api-h2-continuous-deployment-pipeline-0.0.1-SNAPSHOT.jar",
                    description: "New version"
                )
                // Create configuration template based on existing environment
                ebCreateConfigurationTemplate(
                    applicationName: "todo-rest-api",
                    templateName: "todo-rest-api-template",
                    environmentId: "e-isnpfppgpf",
                    description: "Configuration template for todo-rest-api"
                )
                // Create environment from existing configuration template - New version
                ebCreateEnvironment(
                    applicationName: "todo-rest-api",
                    environmentName: "Todo-rest-api-staging-blue",
                    templateName: "todo-rest-api-template",
                    versionLabel: "Todo-rest-api-docker-3",
                    description: "Blue environment"
                )
                // Wait for environment health to be green for at least 1 minute
                ebWaitOnEnvironmentHealth(
                    applicationName: "todo-rest-api",
                    environmentName: "Todo-rest-api-staging-blue",
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
