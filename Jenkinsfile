pipeline {
    agent any

    parameters {
        string(name: 'GITHUB_REPO', defaultValue: 'https://github.com/scanossjeronimo/nginx', description: 'GitHub repository URL to analyze and scan')
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${params.GITHUB_REPO}"
            }
        }

        stage('ORT Analyze') {
            steps {
                sh '''
                docker run --rm \
                -v ${WORKSPACE}:/project \
                -v ${WORKSPACE}/ort/analyzer:/output \
                ort \
                analyze -f JSON -i /project -o /output
                '''
            }
        }

        stage('ORT Scan') {
            steps {
                sh '''
                docker run --rm \
                -v ${WORKSPACE}:/project \
                -v ${WORKSPACE}/ort/scanner:/output \
                ort \
                scan -f JSON -i /project -o /output
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
