pipeline {
    agent any

    stages {
        stage('Clone code') {
            steps {
                git 'https://github.com/quanzzzzzz/fintrack-project1.git'
            }
        }

        stage('Build Backend JAR') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t fintrack-backend .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                dir('backend') {
                    sh '''
                        docker rm -f fintrack-backend || true
                        docker run -d -p 5000:5000 --name fintrack-backend fintrack-backend
                    '''
                }
            }
        }
    }
}
