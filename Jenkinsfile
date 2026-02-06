pipeline {
    agent { label 'ec2-docker' }

    environment {
        DOCKER_IMAGE = "amartyalodh/html-nginx"
        CONTAINER_NAME = "web-app"
        DOCKER_CREDS = "docker-cred"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/amartyalodh2-spec/GITFLOW.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE:latest .
                '''
            }
        }

    stage('Build and tag Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker', url: 'https://index.docker.io/v1/') {
                       sh 'docker build -t amartyalodh/web-app:latest .'
                  }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker', url: 'https://index.docker.io/v1/') {
                       sh 'docker push amartyalodh/web-app:latest'
                  }
                }
            }
        }

       

        stage('Deploy on EC2') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true

                docker pull $DOCKER_IMAGE:latest

                docker run -d \
                --name $CONTAINER_NAME \
                -p 80:80 \
                --restart always \
                $DOCKER_IMAGE:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD done. App is LIVE on EC2!"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}
