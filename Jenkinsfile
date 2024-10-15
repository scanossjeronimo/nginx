pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/scanossjeronimo/nginx.git'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${env.GITHUB_REPO}"
                sh 'ls -la ${WORKSPACE}'
            }
        }

        stage('Prepare Output Directories') {
            steps {
                sh '''
                mkdir -p ${WORKSPACE}/ort/analyzer
                mkdir -p ${WORKSPACE}/ort/scanner
                chmod -R 777 ${WORKSPACE}/ort
                ls -la ${WORKSPACE}/ort
                '''
            }
        }

        stage('ORT Analyze') {
            steps {
                sh '''
                docker run --rm \
                -v ${WORKSPACE}:/project \
                -v ${WORKSPACE}/ort/analyzer:/output \
                --user $(id -u):$(id -g) \
                ort \
                analyze -f JSON -i /project -o /output
                
                echo "Contents of analyzer output directory:"
                ls -la ${WORKSPACE}/ort/analyzer
                '''
            }
        }

        stage('ORT Scan') {
            steps {
                sh '''
                docker run --rm \
                -v ${WORKSPACE}:/project \
                -v ${WORKSPACE}/ort/scanner:/output \
                --user $(id -u):$(id -g) \
                ort \
                scan -f JSON -i /project -o /output
                
                echo "Contents of scanner output directory:"
                ls -la ${WORKSPACE}/ort/scanner
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'ort/**/*', fingerprint: true
        }
    }
}
