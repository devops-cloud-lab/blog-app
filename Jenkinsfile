pipeline {
    agent { label 'app-node' }   // Run on Application Node agent

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        FRONTEND_IMAGE = "281644/blog-frontend"
        BACKEND_IMAGE  = "281644/blog-backend"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devops-cloud-lab/blog-app.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $FRONTEND_IMAGE:$BUILD_NUMBER ./frontend'
                sh 'docker build -t $BACKEND_IMAGE:$BUILD_NUMBER ./backend'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $FRONTEND_IMAGE:$BUILD_NUMBER'
                sh 'docker push $BACKEND_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                    docker stop frontend || true &&
                    docker rm frontend || true &&
                    docker run -d --name frontend -p 80:80 $FRONTEND_IMAGE:$BUILD_NUMBER

                    docker stop backend || true &&
                    docker rm backend || true &&
                    docker run -d --name backend -p 3000:3000 $BACKEND_IMAGE:$BUILD_NUMBER
                '''
            }
        }
    }
}
