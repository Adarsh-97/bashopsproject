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
                sh '''#!/bin/bash
                python3 -m venv myenv
                '''
            }
        }
        stage('Upgrade Pip') {
            steps {
                sh '''#!/bin/bash
                source myenv/bin/activate
                pip install --upgrade pip
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''#!/bin/bash
                source myenv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }
        stage('Run Migrations') {
            steps {
                sh '''#!/bin/bash
                source myenv/bin/activate
                python3 manage.py migrate
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''#!/bin/bash
                source myenv/bin/activate
                python3 manage.py test
                '''
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('my-sonarqube-server') { // Ensure this matches the SonarQube server configuration name
                        sh '''#!/bin/bash
                        ${scannerHome}/bin/sonar-scanner
                        '''
                    }
                }
            }
        }
    }
}

