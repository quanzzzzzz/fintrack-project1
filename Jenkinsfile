pipeline {
    agent any

    environment {
        BACKEND_DIR = 'be-fintrack-master'
        FRONTEND_DIR = 'fe-fintrack-master'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/quanzzzzzz/fintrack-project1.git'
            }
        }

        stage('Build Backend JAR') {
            steps {
                dir("${BACKEND_DIR}") {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir("${FRONTEND_DIR}") {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${BACKEND_DIR}") {
                    bat 'docker build -t fintrack-backend .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                    docker stop fintrack-backend || exit 0
                    docker rm fintrack-backend || exit 0
                    docker run -d -p 5000:5000 --name fintrack-backend fintrack-backend
                '''
            }
        }
    }
}
