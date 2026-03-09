pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source venv/bin/activate
                python -m pytest test_app.py
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
                source venv/bin/activate
                python -m safety check --output html > safety-report.html || true
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