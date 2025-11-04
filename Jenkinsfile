pipeline {
    agent any
    environment {
        IMAGE_NAME = "myapi"
        CONTAINER_NAME = "myapi-container"
        API_PORT = "8290"
        MANAGEMENT_PORT = "8253"
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
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                """
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running API container on jenkins-net network'
                sh """
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    --network ${NETWORK_NAME} \
                    -p ${API_PORT}:${API_PORT} \
                    -p ${MANAGEMENT_PORT}:${MANAGEMENT_PORT} \
                    -p ${INTERNAL_PORT}:${INTERNAL_PORT} \
                    ${IMAGE_NAME}:v1
                """
            }
        }

        stage('Verify Containers') {
            steps {
                echo 'Listing running Docker containers'
                sh "docker ps"
            }
        }

        stage('Test GET API') {
            steps {
                echo 'Testing GET endpoint from Jenkins container'
                sh """
                    echo 'Waiting 20 seconds for WSO2 MI to fully start...'
                    sleep 20
                    echo 'GET: http://${CONTAINER_NAME}:${API_PORT}/appointmentservices/getAppointment'
                    curl -I http://${CONTAINER_NAME}:${API_PORT}/appointmentservices/getAppointment || true
                """
            }
        }

        stage('Test SOAP POST') {
            steps {
                echo 'Sending SOAP POST request to WSO2 MI service'
                sh """
                    # Create SOAP request file
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

                    # Send the SOAP request
                    curl -X POST \
                      http://${CONTAINER_NAME}:${MANAGEMENT_PORT}/services/AppointmentServices \
                      -H "Content-Type: text/xml;charset=UTF-8" \
                      -d @setAppointment.xml
                """
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished!'
        }
    }
}
