pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-django-app'
        DOCKER_TAG = 'latest'
        DOCKER_REGISTRY = 'docker.io' // Docker Hub default registry
        DOCKER_USERNAME = 'adarshtdocker' // Replace with your Docker Hub username
        DOCKER_PASSWORD = 'Asdf@2425#' // Replace with your Docker Hub password
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
        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub
                    sh """
                    echo ${DOCKER_PASSWORD} | docker login ${DOCKER_REGISTRY} -u ${DOCKER_USERNAME} --password-stdin
                    """
                    
                    // Push the Docker image
                    sh """
                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker push ${DOCKER_USERNAME}/${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
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
