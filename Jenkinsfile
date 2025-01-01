pipeline {
    agent any

    environment {
        PYTHON_PATH = '/usr/bin/python3'
        SONAR_SCANNER_PATH = '/opt/sonar-scanner/bin/sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                export PATH=$PYTHON_PATH:$PATH
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('Sonarqube-token')
            }
            steps {
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                command -v sonar-scanner || echo "SonarQube scanner not found. Please install it."
                . venv/bin/activate
                sonar-scanner \
                  -Dsonar.projectKey=sample \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://localhost:9000 \
                  -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
