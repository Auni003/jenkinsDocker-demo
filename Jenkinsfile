pipeline {
    agent any
<<<<<<< HEAD

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
=======
    environment {
        IMAGE_NAME = "myapi"
        CONTAINER_NAME = "myapi-container"
        API_PORT = "8290"           // API endpoint port
        MANAGEMENT_PORT = "8253"    // Management port
        INTERNAL_PORT = "9164"
        NETWORK_NAME = "jenkins-net"
    }
    stages {

        stage('Prepare') {
            steps {
                echo 'Workspace ready: Jenkins will clone repository automatically'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image ${IMAGE_NAME}:v1"
                sh "docker build -t ${IMAGE_NAME}:v1 -f api/Dockerfile.api ./api"
            }
        }

        stage('Stop & Remove Old Container') {
            steps {
                echo 'Stopping and removing old container if exists'
>>>>>>> f4a4f96c148f01d6d263e7603af5d826b66ea98a
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                """
            }
        }

<<<<<<< HEAD
        stage('Run New Container') {
            steps {
                echo "🚀 Running new container..."
=======
        stage('Run Docker Container') {
            steps {
                echo 'Running API container on jenkins-net network'
>>>>>>> f4a4f96c148f01d6d263e7603af5d826b66ea98a
                sh """
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    --network ${NETWORK_NAME} \
<<<<<<< HEAD
                    -p 8290:8290 \
                    -p 8253:8253 \
                    myapi:v1
=======
                    -p ${API_PORT}:${API_PORT} \
                    -p ${MANAGEMENT_PORT}:${MANAGEMENT_PORT} \
                    -p ${INTERNAL_PORT}:${INTERNAL_PORT} \
                    ${IMAGE_NAME}:v1
>>>>>>> f4a4f96c148f01d6d263e7603af5d826b66ea98a
                """
            }
        }

<<<<<<< HEAD
        stage('Test API') {
            steps {
                echo "🧪 Testing endpoint..."
                sh """
                    sleep 20
                    curl -I http://${CONTAINER_NAME}:8290 || true
=======
        stage('Verify Containers') {
            steps {
                echo 'Listing running Docker containers'
                sh "docker ps"
            }
        }

        stage('Test API Endpoints') {
            steps {
                echo 'Testing API endpoints dynamically (GET & SOAP POST)'
                sh """
                    # Wait for WSO2 MI to fully start
                    echo 'Waiting 40 seconds for WSO2 MI to deploy CAR files...'
                    sleep 40

                    # --- Test GET Endpoint ---
                    echo 'Testing GET endpoint: http://${CONTAINER_NAME}:${API_PORT}/appointmentservices/getAppointment'
                    curl -i http://${CONTAINER_NAME}:${API_PORT}/appointmentservices/getAppointment || true

                    # --- Test SOAP POST Endpoint ---
                    echo 'Testing SOAP POST endpoint: http://${CONTAINER_NAME}:${API_PORT}/services/AppointmentServices'

                    cat <<EOF > setAppointment.xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:app="http://services.appointment/">
   <soapenv:Header/>
   <soapenv:Body>
      <app:setAppointment>
         <appointmentId>123</appointmentId>
         <name>Auni Hazimah</name>
         <time>2025-11-05T10:00:00</time>
      </app:setAppointment>
   </soapenv:Body>
</soapenv:Envelope>
EOF

                    curl -v -X POST \
                      http://${CONTAINER_NAME}:${API_PORT}/services/AppointmentServices \
                      -H "Content-Type: text/xml;charset=UTF-8" \
                      --data-binary @setAppointment.xml
>>>>>>> f4a4f96c148f01d6d263e7603af5d826b66ea98a
                """
            }
        }
    }

    post {
        always {
<<<<<<< HEAD
            echo "✅ Pipeline finished!"
=======
            echo 'Pipeline finished!'
>>>>>>> f4a4f96c148f01d6d263e7603af5d826b66ea98a
        }
    }
}
