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
        SONAR_PROJECT_KEY = 'fintrack'
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN = credentials('sonar-token') // ✅ Đảm bảo đã tạo credentials ID là sonar-token
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

        stage('SonarQube Analysis') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    withSonarQubeEnv('MySonarQubeServer') { // ✅ Server name phải đúng với cấu hình Sonar trong Jenkins
                        bat """
                            mvn sonar:sonar ^
                            -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} ^
                            -Dsonar.host.url=${env.SONAR_HOST_URL} ^
                            -Dsonar.login=${env.SONAR_TOKEN}
                        """
                    }
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
