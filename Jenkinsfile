pipeline {
    agent any
    
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Check ORT is running') {
            steps {
                script {
                    sh 'docker run --rm ort --info'
                }
            }
        }
        stage ('Analyze') {
            steps {
                script {
                    def projectDir = sh(script: "basename ${env.WORKSPACE}", returnStdout: true).trim()
      
                    sh """
                    docker run --rm \
                    -v "${env.WORKSPACE}:/project:ro" \
                    -v "${env.WORKSPACE}/output:/output" \
                    ort \
                    --info analyze \
                    -f JSON \
                    -i /project \
                    -o /output/analyzer
                    """
                }
            }
        }
        stage ('Scanner') {
            steps {
                script {
                    def projectDir = sh(script: "basename ${env.WORKSPACE}", returnStdout: true).trim()
                    
                    sh """
                    docker run --rm \
                    -v "${env.WORKSPACE}:/project:ro" \
                    -v "${env.WORKSPACE}/output:/output" \
                    ort \
                    --info scan \
                    -S SCANOSS \
                    -f JSON \
                    -i /project \
                    -o /output/scanner
                    """
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
