pipeline {
    agent any
    
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Prepare Output Directory') {
            steps {
                sh """
                mkdir -p ${env.WORKSPACE}/output
                chmod 777 ${env.WORKSPACE}/output
                """
            }
        }
        
        stage('Check ORT is running') {
            steps {
                script {
                    sh 'docker run --rm ort --info'
                }
            }
        }

        stage('Verify Analyzed Directory') {
            steps {
                sh """
                docker run --rm \
                -v "${env.WORKSPACE}:/project:ro" \
                -v "${env.WORKSPACE}/output:/output" \
                ubuntu \
                bash -c "echo 'Contents of /project:' && ls -R /project && echo 'Contents of /output:' && ls -R /output"
                """
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
