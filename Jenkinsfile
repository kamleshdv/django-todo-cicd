pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME = "django-todo-app"
        CONTAINER_NAME = "django-todo-app"
        APP_PORT = "8000"
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo '📥 Code clone ho raha hai...'
                checkout scm
            }
        }

        stage('Check Docker') {
            steps {
                echo '🐳 Docker version check...'
                sh 'docker --version'
                sh 'docker compose version'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🔨 Docker image build ho rahi hai...'
                sh 'docker compose build'
            }
        }

        stage('Stop Old Container') {
            steps {
                echo '🛑 Purana container band kar rahe hain...'
                sh '''
                    if [ $(docker ps -q -f name=${CONTAINER_NAME}) ]; then
                        docker compose down
                        echo "Container band ho gaya."
                    else
                        echo "Koi purana container nahi mila, skip."
                    fi
                '''
            }
        }

        stage('Run Container') {
            steps {
                echo '🚀 Naya container start ho raha hai...'
                sh 'docker compose up -d'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo '✅ Deployment verify kar rahe hain...'
                sh '''
                    sleep 5
                    if [ $(docker ps -q -f name=${CONTAINER_NAME}) ]; then
                        echo "✅ Container successfully chal raha hai!"
                        docker ps | grep ${CONTAINER_NAME}
                    else
                        echo "❌ Container start nahi hua!"
                        docker logs ${CONTAINER_NAME}
                        exit 1
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline successful! App chal rahi hai port ${APP_PORT} par.'
        }
        failure {
            echo '💥 Pipeline fail ho gayi. Logs dekho upar.'
        }
        always {
            echo '🔍 Pipeline complete hua.'
        }
    }
}
