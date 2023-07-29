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
                s3Upload(bucket:"popsy-bucket", path:"$BUILD_NUMBER/", includePathPattern:'**/*')
                s3Upload(file:'target/02-spring-boot-todo-rest-api-h2-continuous-deployment-pipeline-0.0.1-SNAPSHOT.jar', 
                    bucket:'popsy-bucket', 
                    path:"$BUILD_NUMBER/02-spring-boot-todo-rest-api-h2-continuous-deployment-pipeline-0.0.1-SNAPSHOT.jar")
            }            
        }


        stage('Deliver') {
            steps {
                sh 'eb --version'
                ebCreateApplicationVersion(
                    applicationName: "todo-rest-api",
                    versionLabel: "Todo-rest-api-docker-$BUILD_NUMBER",
                    s3Bucket: "popsy-bucket",
                    s3Key: "$BUILD_NUMBER",
                    description: "New version"
                )
                // Create configuration template based on existing environment
                ebCreateConfigurationTemplate(
                    applicationName: "todo-rest-api",
                    templateName: "todo-rest-api-template-$BUILD_NUMBER",
                    environmentId: "e-isnpfppgpf",
                    description: "Configuration template for todo-rest-api"
                )
                // Create environment from existing configuration template - New version
                ebCreateEnvironment(
                    applicationName: "todo-rest-api",
                    environmentName: "Todo-rest-api-staging-$BUILD_NUMBER-blue",
                    templateName: "todo-rest-api-template",
                    versionLabel: "Todo-rest-api-docker-$BUILD_NUMBER",
                    description: "Blue environment"
                )
                // Wait for environment health to be green for at least 1 minute
                ebWaitOnEnvironmentHealth(
                    applicationName: "todo-rest-api",
                    environmentName: "Todo-rest-api-staging-$BUILD_NUMBER-blue",
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
