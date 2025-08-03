pipeline {
    agent any

    tools {
        maven 'Maven_3.9.11'
    }

    environment {
        BACKEND_DIR = 'be-fintrack-master'
        FRONTEND_DIR = 'fe-fintrack-master'
        DOCKER_BUILDKIT = '1'
        PATH = "${tool 'Maven_3.9.11'}/bin:${env.PATH}"
        SONAR_PROJECT_KEY = 'fintrack'
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN = credentials('sonar-token') // Tên credentials bạn tạo trong Jenkins
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
                    withSonarQubeEnv('MySonarQubeServer') {
                        bat "mvn sonar:sonar -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} -Dsonar.host.url=${env.SONAR_HOST_URL} -Dsonar.login=${env.SONAR_TOKEN}"
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
