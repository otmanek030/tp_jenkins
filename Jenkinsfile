pipeline {
    agent any
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'pip3 install --break-system-packages -r requirements.txt'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py'
            }
        }
        stage('SAST Scan') {
            steps {
                echo 'Skipping SAST for now...'
                // Once SonarQube is ready, you'll use:
                // tool 'sonar-scanner'
            }
        }
        stage('SCA Scan') {
            steps {
                // Use the plugin command instead of 'sh'
                // 'odcInstallation' must match the name 'DP-Check' you gave in Tools
                dependencyCheck additionalArguments: '--scan . --format HTML --format XML --failOnCVSS 7', odcInstallation: 'DP-Check'
            }
        }
        stage('Publish Reports') {
            steps {
                // This makes the report visible on the Jenkins project page
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
    post {
        failure {
            echo 'Build failed due to errors or vulnerabilities'
        }
    }
}