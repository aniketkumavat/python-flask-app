
# Python Flask Application CI/CD Pipeline with Jenkins on AWS

This repository contains a simple Flask application and a Jenkins pipeline configuration for automating tests, Docker builds, and image deployment to DockerHub. The pipeline is managed using Jenkins, hosted on an AWS EC2 instance.

---

## Steps to Set Up the Project

### 1. GitHub Repository Setup
1. Create a new GitHub repository.
2. Add the following files in the repository:
   - `app.py`: The Flask application code
   - `test_app.py`: Unit tests for the application
   - `requirements.txt`: Dependencies for the Flask application
   - `Dockerfile`: Docker configuration for containerizing the app
   - `Jenkinsfile`: Pipeline configuration for Jenkins

3. Push the code to GitHub.

### 2. DockerHub Repository Setup
1. Create a new DockerHub repository to store your application images.
2. Note the repository name (e.g., `username/python-flask-app`) as it will be referenced in the Jenkins pipeline.

### 3. Jenkins Server Setup on AWS
1. Launch an EC2 instance (Ubuntu or Amazon Linux).
2. SSH into the instance and install:
   - **Java** (for Jenkins installation)
   - **Docker** (for building and pushing images)
   - **Python** (for running the application)
3. Grant Jenkins the necessary permissions to run Docker:
   ```bash
   sudo usermod -aG docker jenkins
   ```

### 4. Jenkins Configuration
1. Access Jenkins at `http://<your-ec2-public-ip>:8080`.
2. Create a new **Pipeline** job and configure it:
   - **Source Control**: Connect your GitHub repository.
   - **Build Trigger**: Set up a webhook in GitHub to trigger Jenkins on each commit.

3. Add your DockerHub credentials in Jenkins with ID `dockercredential`.

### 5. Jenkins Pipeline Stages

The pipeline defined in `Jenkinsfile` includes:
   - **Checkout**: Clones the latest code from GitHub.
   - **Run Tests**: Installs dependencies and runs unit tests.
   - **Build Docker Image**: Builds a Docker image tagged with the build number.
   - **Login to DockerHub**: Authenticates using DockerHub credentials.
   - **Push Docker Image**: Pushes the Docker image to your DockerHub repository.
   - **Run Docker Image**: Removes any existing container on the custom port and deploys the new container.

---

### Jenkinsfile Overview

The Jenkins pipeline (Jenkinsfile) configuration:

```groovy
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockercredential')
        DOCKER_IMAGE = "username/python-flask-app"
        DOCKER_TAG = "v${BUILD_NUMBER}"
        CUSTOM_PORT = '500'
    }
    stages {
        stage('Checkout') { ... }
        stage('Run Tests') { ... }
        stage('Build Docker Image') { ... }
        stage('Login to DockerHub') { ... }
        stage('Push Docker Image') { ... }
        stage('Run Docker Image') { ... }
    }
    post {
        always {
            sh 'docker logout'
            sh "docker image prune -f"
        }
    }
}
```

Replace `username/python-flask-app` with your DockerHub repository name.
All stages content inside Jenkinsfile.

---

### Dockerfile Overview

The Dockerfile sets up the Flask application environment:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 500
CMD ["python", "app.py"]
```

---

## Running and Testing

1. Make a code change, commit, and push to GitHub.
2. Jenkins automatically triggers the pipeline:
   - Tests the code
   - Builds and pushes a Docker image to DockerHub
   - Deploys the container on the custom port

---

## Notes
- Ensure Docker, Jenkins, and Python are correctly installed on the AWS server.
- Use the custom port as defined in `Jenkinsfile` (`CUSTOM_PORT` environment variable).
- Clean up Docker images regularly to free up disk space.

---

This setup enables automated testing, building, and deployment of the Flask application using Jenkins on AWS.
