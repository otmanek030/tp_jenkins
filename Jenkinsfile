pipeline {
    agent {
        docker {
            image 'python:3.13-slim'   // Python environment in Docker
            args '-u root:root'        // optional, ensures you can install packages
        }
    }

    environment {
        PATH = "$PATH:/root/.local/bin"
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                python -m pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python -m pytest test_app.py'
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
                sh 'python -m safety check --output html > safety-report.html || true'
            }
        }

        stage('Publish Reports') {
            steps {
                archiveArtifacts artifacts: '*.html', fingerprint: true
            }
        }
    }

    post {
        failure {
            echo 'Build failed due to errors or vulnerabilities'
        }
        success {
            echo 'Pipeline executed successfully'
        }
    }
}