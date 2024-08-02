pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Clean workspace
                deleteDir()
                checkout scm
            }
        }
        stage('Create Virtual Environment') {
            steps {
                sh 'python3 -m venv myenv'
            }
        }
        stage('Upgrade Pip') {
            steps {
                sh '''
                source myenv/bin/activate
                pip install --upgrade pip
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                source myenv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }
        stage('Run Migrations') {
            steps {
                sh '''
                source myenv/bin/activate
                python3 manage.py migrate
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                source myenv/bin/activate
                python3 manage.py test
                '''
            }
        }
    }
}
