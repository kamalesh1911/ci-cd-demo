pipeline {
    agent any

    parameters {
        booleanParam(
            name: 'ENABLE_DOCKER',
            defaultValue: false,
            description: 'Enable Docker deployment'
        )
    }

    stages {

        stage('CI - Run Tests') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                pytest -v
                '''
            }
        }

        stage('Deploy to STAGING') {
            when { branch 'main' }
            steps {
                sh '/var/lib/company-app/deploy-staging.sh'
            }
        }

        stage('Approval for Production') {
            when { branch 'main' }
            input {
                message "Approve Production Deployment?"
                ok "Deploy"
            }
        }

        stage('Deploy to PRODUCTION (No Docker)') {
            when {
                allOf {
                    branch 'main'
                    expression { !params.ENABLE_DOCKER }
                }
            }
            steps {
                sh '/var/lib/company-app/deploy-prod.sh'
            }
        }

        stage('Docker Deployment (Optional)') {
            when {
                allOf {
                    branch 'main'
                    expression { params.ENABLE_DOCKER }
                }
            }
            steps {
                sh '''
                docker build -t company-app:${BUILD_NUMBER} .
                docker stop company-app || true
                docker rm company-app || true
                docker run -d --name company-app company-app:${BUILD_NUMBER}
                '''
            }
        }
    }
}
