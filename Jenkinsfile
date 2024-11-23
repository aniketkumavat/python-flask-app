pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockercredential')
        DOCKER_IMAGE = "aniket1709/python-flask-app"
        DOCKER_TAG = "v${BUILD_NUMBER}"
        CUSTOM_PORT = '500'  // Set your custom port here
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Run Tests') {
            steps {
                sh '''
                    python -m pip install -r requirements.txt --break-system-packages
                    python -m pytest test_app.py
                '''
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        stage('Login to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        
        stage('Push Docker Image') {
            steps {
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
       stage('Run Docker Image') {
          steps {
                  sh """
                    CONTAINERS=\$(docker ps -q --filter "expose=${CUSTOM_PORT}")
                    if [ -n "\$CONTAINERS" ]; then
                        docker rm -f \$CONTAINERS
                    fi
                """
                // Run the container with the custom port mapping
                sh "docker run -d -p ${CUSTOM_PORT}:${CUSTOM_PORT} ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

    }
    
    post {
        always {
            sh 'docker logout'
            // Clean up Docker images to save space
                sh "docker image prune -f"
        }
    }
}
