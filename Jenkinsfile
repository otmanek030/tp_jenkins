pipeline {
    agent any

    stages {
        stage('Install Dependencies and Run Tests') {
            steps {
                sh '''
                # Run everything inside a Python Docker container
                docker run --rm -v $PWD:/app -w /app python:3.13-slim /bin/bash -c "
                    pip install --upgrade pip &&
                    pip install -r requirements.txt &&
                    python -m pytest test_app.py
                "
                '''
            }
        }

        stage('SAST Scan') {
            steps {
                script {
                    def scannerHome = tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('sonar-server') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('SCA Scan') {
            steps {
                sh '''
                docker run --rm -v $PWD:/app -w /app python:3.13-slim /bin/bash -c "
                    pip install --upgrade pip &&
                    pip install -r requirements.txt &&
                    python -m safety check --output html > safety-report.html || true
                "
                '''
            }
        }

        stage('Publish Reports') {
            steps {
                archiveArtifacts artifacts: '*.html', fingerprint: true
            }
        }
    }

    post {
        failure { echo 'Build failed due to errors or vulnerabilities' }
        success { echo 'Pipeline executed successfully' }
    }
}