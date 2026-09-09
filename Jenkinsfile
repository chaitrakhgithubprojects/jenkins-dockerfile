pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-demo"
        CONTAINER_NAME = "nginx-demo-container"
        HOST_PORT = "8081"
    }

    stages {

        stage('Checkout') {
            steps {
                deleteDir()
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    test -f Dockerfile
                    test -f index.html
                    echo "Required files are present"
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} nginx -t
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      -p ${HOST_PORT}:80 \
                      ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:${HOST_PORT}
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }

        failure {
            echo "Pipeline failed. Check the logs."
        }

        always {
            echo "Jenkins Build: ${BUILD_NUMBER}"
        }
    }
}
