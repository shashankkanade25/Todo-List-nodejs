pipeline {
    agent any

    environment {
        IMAGE_NAME = "shashankkanade25/todo-list-nodejs"
        DOCKER_CREDS = credentials('dockerhub-creds')
    }

    stages {

        stage('Code') {
            steps {
                git branch: 'master', url: 'git@github.com:shashankkanade25/Todo-List-nodejs.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh """
                echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                docker compose down || true
                docker compose pull
                docker compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
