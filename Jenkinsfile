pipeline {
    agent any

    environment {
        MONGO_URI = 'mongodb://host.docker.internal:27017/test_student_db'
        SECRET_KEY = 'ci-test-secret'
        PIP_DISABLE_PIP_VERSION_CHECK = '1'
    }

    options {
        timestamps()
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checkout completed.'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/python -m pip install --upgrade pip
                    .venv/bin/python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Syntax Check') {
            steps {
                sh '''
                    .venv/bin/python -m compileall -q app.py test_app.py
                    echo "Python syntax check passed."
                '''
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                    .venv/bin/bandit -r app.py -ll
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    .venv/bin/python -m pytest -v
                '''
            }
        }
    }

    post {
        success {
            echo 'CI pipeline completed successfully!'
        }
        failure {
            echo 'CI pipeline failed. Check the stage logs.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
