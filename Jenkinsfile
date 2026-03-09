pipeline {
    agent any

    environment {
        PATH = "$PATH:/var/jenkins_home/.local/bin"
    }

    tools {
        sonarQubeScanner 'SonarScanner'
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m pip install --upgrade pip --break-system-packages
                python3 -m pip install --break-system-packages -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python3 -m pytest test_app.py'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('SAST Scan') {
            steps {
                sh 'python3 -m bandit -r . -f html -o bandit-report.html || true'
            }
        }

        stage('SCA Scan') {
            steps {
                sh 'python3 -m safety check --output html > safety-report.html || true'
            }
        }

        stage('Publish Reports') {
            steps {
                archiveArtifacts artifacts: '*.html', fingerprint: true
            }
        }
    }
}