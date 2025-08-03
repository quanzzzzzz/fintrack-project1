pipeline {
    agent any

    tools {
        maven 'Maven_3.9.11'
        nodejs 'Node_18' // ⚠️ Đảm bảo bạn đã cài tool Node.js trong Jenkins và đặt tên chính xác
    }

    environment {
        BACKEND_DIR = 'be-fintrack-master'
        FRONTEND_DIR = 'fe-fintrack-master'
        DOCKER_BUILDKIT = '1'
        PATH = "${tool 'Maven_3.9.11'}/bin:${tool 'Node_18'}/bin:${env.PATH}"
        // Đã bỏ biến SONAR_PROJECT_KEY, SONAR_HOST_URL, SONAR_TOKEN
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/quanzzzzzz/fintrack-project1.git'
            }
        }

        stage('Build Backend JAR') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        // ❌ Đã xoá stage 'SonarQube Analysis'

        stage('Build Frontend') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                bat 'docker-compose build'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        failure {
            echo "❌ Build failed!"
        }
        success {
            echo "✅ Build and deployment successful!"
        }
    }
}
