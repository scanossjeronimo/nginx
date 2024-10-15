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
                    def projectDir = sh(script: "basename ${env.WORKSPACE}", returnStdout: true).trim()
      
                    sh '''
                    docker run \
                    -v $(env.WORKSPACE)/:/project \
                    ort \
                    --info analyze \
                    -f JSON \
                    -i /project \
                    -o /project/output/analyzer
                    '''
                }
            }
        }

        stage ('Scanner') {
            steps{
                script{
                    def projectDir = sh(script: "basename ${env.WORKSPACE}", returnStdout: true).trim()
                    
                    sh '''
                    docker run \
                    -v $(env.WORKSPACE)/:/project \
                    ort \
                    --info analyze \
                    -S SCANOSS \
                    -f JSON \
                    -i /project \
                    -o /project/output/scanner
                    '''
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
