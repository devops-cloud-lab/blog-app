pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')   // Jenkins credential ID
        IMAGE_NAME = "your-dockerhub-username/blogging-platform"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devops-cloud-lab/blog-app.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $IMAGE_NAME-frontend:$BUILD_NUMBER ./frontend'
                sh 'docker build -t $IMAGE_NAME-backend:$BUILD_NUMBER ./backend'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $IMAGE_NAME-frontend:$BUILD_NUMBER'
                sh 'docker push $IMAGE_NAME-backend:$BUILD_NUMBER'
            }
        }

        stage('Deploy to App Node') {
            steps {
                sshagent(['app-node-ssh']) {
                    sh '''
                    ssh user@app-node "
                        docker pull $IMAGE_NAME-frontend:$BUILD_NUMBER &&
                        docker pull $IMAGE_NAME-backend:$BUILD_NUMBER &&
                        docker stop frontend || true &&
                        docker rm frontend || true &&
                        docker run -d --name frontend -p 80:80 $IMAGE_NAME-frontend:$BUILD_NUMBER &&
                        docker stop backend || true &&
                        docker rm backend || true &&
                        docker run -d --name backend -p 3000:3000 $IMAGE_NAME-backend:$BUILD_NUMBER
                    "
                    '''
                }
            }
        }
    }
}
