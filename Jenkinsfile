pipeline {
    agent any

    tools {
        maven 'Maven_3.9.11' // Phải khớp tên đã cấu hình trong Jenkins -> Global Tool Configuration
    }

    environment {
        BACKEND_DIR = 'be-fintrack-master'
        FRONTEND_DIR = 'fe-fintrack-master'
        DOCKER_BUILDKIT = '1'
        PATH = "${tool 'Maven_3.9.11'}/bin:${env.PATH}" // Đảm bảo Jenkins biết vị trí Maven
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

        stage('Build Frontend') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${env.BACKEND_DIR}") {
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

    post {
        failure {
            echo "❌ Build failed!"
        }
        success {
            echo "✅ Build and deployment successful!"
        }
    }
}
