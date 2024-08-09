pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-django-app'
        DOCKER_TAG = 'latest'
    }

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
                    def scannerHome = tool 'globalsonarqube'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    // Build Docker image
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Run the Docker container and execute commands inside it
                    docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").inside {
                        sh '''
                        # Run migrations inside Docker
                        python3 manage.py migrate
                        # Run tests inside Docker
                        python3 manage.py test
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed'
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
