pipeline {
    agent any
    
    stages {

        stage('Checkout SCM') {
            steps{
                checkout scm
            }
        }
        
        stage('Check ORT is running') {
            steps {
                script {
                    sh 'docker run ort --info'
                }
            }
        }

        stage ('Analyze') {
            steps {
                script{
                    sh 'docker run -v ./:/project ort --info analyze -i /project -o /project/ort -f JSON'
                }
            }
        }

        stage ('Scanner') {
            steps{
                script{
                    sh 'docker run -v ./:/project ort --info scan -i /project/ort/analyzer-result.json -o /project/ort -s SCANOSS -f JSON'
                }
            }
        }

    }
    
    post {
        always {
            // Clean up steps
            cleanWs()
        }
    }
}
