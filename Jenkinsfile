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
        stage('Create virtual Environment') {
            steps {
                sh 'python3 -m venv myenv'
                sh '. myenv/bin/activate'
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Run migrations') {
            steps {
                sh '''#!/bin/bash
                   source ./myenv/bin/activate
                   python3 manage.py migrate
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''#!/bin/bash
                   source ./venv/bin/activate
                   python3 manage.py test
                '''
            }
        }
    }
}
