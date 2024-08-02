pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                // Clean workspace
                deleteDir()
            }
        }
        stage('Create virtual Environment') {
            steps {
                sh '''#!/bin/bash
                   python3 -m venv myenv
                   source ./myenv/bin/activate
                '''
            }
        }
        stage('Install dependencies') {
            steps {
                sh '''#!/bin/bash
                   source ./myenv/bin/activate
                   pip install -r requirements.txt
                '''
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
