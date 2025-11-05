pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapi"
        CONTAINER_NAME = "myapi-container"
        NETWORK_NAME = "jenkins-net"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo "🔧 Building Docker image..."
                sh "docker build -t ${IMAGE_NAME}:v1 ."
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "🛑 Stopping old container (if exists)..."
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Run New Container') {
            steps {
                echo "🚀 Running new container..."
                sh """
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    --network ${NETWORK_NAME} \
                    -p 8290:8290 \
                    -p 8253:8253 \
                    myapi:v1
                """
            }
        }

        stage('Test API') {
            steps {
                echo "🧪 Testing endpoint..."
                sh """
                    sleep 20
                    curl -I http://${CONTAINER_NAME}:8290 || true
                """
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline finished!"
        }
    }
}
