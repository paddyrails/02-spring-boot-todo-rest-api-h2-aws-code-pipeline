pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "BUILD"
            }
        }
        stage('Test') {
            steps {
                echo "Test"
            }            
        }
        stage('Deliver') {
            steps {
                sh '/home/ec2-user/.local/bin/eb --version'
            }
        }
        stage('Complete') {
            steps {
                echo 'Job Complete!'
            }
        }
    }
}
