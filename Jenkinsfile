pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/otmanek030/tp_jenkins.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'python -m pip install --upgrade pip'
                bat 'python -m pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'python -m pytest'
            }
        }

        stage('SAST Scan') {
            steps {
                // Assurez-vous que SonarScanner est installé et accessible
                bat 'sonar-scanner'
            }
        }

        stage('SCA Scan') {
            steps {
                // Mettre le chemin complet vers dependency-check.bat sur Windows
                bat 'C:\\Users\\OTHMANE\\dependency-check\\bin\\dependency-check.bat --project "TP-Jenkins" --scan . --format HTML --out C:\\Users\\OTHMANE\\tp_jenkins\\tp_jenkins\\reports'
            }
        }

    }

    post {
        failure {
            echo 'Build failed due to errors or vulnerabilities'
        }
    }
}