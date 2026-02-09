pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Code checkout completed"
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                python3 -m venv $VENV
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                source $VENV/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source $VENV/bin/activate
                pytest -v
                '''
            }
        }

        stage('Build / Run Application') {
            steps {
                sh '''
                source $VENV/bin/activate
                python app.py
                '''
            }
        }
    }

    post {
        success {
            echo "✅ PIPELINE EXECUTED SUCCESSFULLY"
        }
        failure {
            echo "❌ PIPELINE FAILED"
        }
    }
}
