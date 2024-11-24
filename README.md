# python-flask-app

Deploy an python application with CI/CD Pipeline

# CI/CD Pipeline Documentation: Python Application with GitHub, Jenkins, and Docker

## Table of Contents
1. [Project Overview]
2. [Prerequisites]
3. [Application Setup]
4. [GitHub Configuration]
5. [Jenkins Setup]
6. [Docker Configuration]
7. [Pipeline Configuration]
8. [Testing and Deployment]
9. [Troubleshooting]
10. [Best Practices]

## 1. Project Overview

### 1.1 Architecture
- Source Control: GitHub
- CI/CD Server: Jenkins
- Containerization: Docker
- Image Registry: DockerHub

### 1.2 Pipeline Flow

1. Code push to GitHub
2. Jenkins automatically triggers build
3. Run Python tests
4. Build Docker image
5. Push to DockerHub

## 2. Prerequisites

### 2.1 Software Requirements

- Git
- Jenkins 
- Python 3.x
- Docker
- DockerHub account

### 2.2 System Requirements

```bash
# Install Python
sudo apt update
sudo apt install -y python3 python3-pip

# Install Docker
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker

# Install Git
sudo apt install -y git
```

## 3. Application Setup

### 3.1 Python Application (app.py)

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, DevOps World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### 3.2 Unit Tests (test_app.py)
```python
import unittest
from app import app

class TestApp(unittest.TestCase):
    def setUp(self):
        self.app = app.test_client()
        
    def test_hello_world(self):
        response = self.app.get('/')
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.data.decode('utf-8'), 'Hello, DevOps World!')

if __name__ == '__main__':
    unittest.main()
```

### 3.3 Requirements (requirements.txt)

```text
flask==3.0.0
pytest==7.4.3
```

### 3.4 Dockerfile


## 4. GitHub Configuration

### 4.1 Repository Setup

```bash
# Initialize local repository
git init
git add .
git commit -m "Initial commit"
git branch -M main

# Add remote repository
git remote add origin <your-repo-url>
git push -u origin main
```

### 4.2 Webhook Configuration
1. Go to Repository Settings → Webhooks
2. Add webhook:
   - Payload URL: `http://your-jenkins-url/github-webhook/`
   - Content type: `application/json`
   - Events: Just the push event
   - Active: ✓

## 5. Jenkins Setup

### 5.1 Required Plugins

1. Go to "Manage Jenkins" → "Manage Plugins"
2. Install:
   - GitHub Integration
   - Docker Pipeline
   - Credentials Plugin

### 5.2 Credentials Setup
1. Go to "Manage Jenkins" → "Credentials"
2. Add DockerHub credentials:

   Kind: Username with password
   Scope: Global
   Username: <DockerHub username>
   Password: <DockerHub password>
   ID: dockerhubcredential

### 5.3 Pipeline Job Creation
1. Click "New Item"
2. Select "Pipeline"
3. Configure:
   - GitHub project URL
   - Build Triggers: GitHub hook trigger
   - Pipeline: Pipeline script from SCM
   - SCM: Git
   - Repository URL
   - Credentials
   - Branch: */main
   - Script Path: Jenkinsfile

## 6. Docker Configuration

### 6.1 Jenkins Docker Setup

# Add Jenkins user to docker group
sudo usermod -aG docker jenkins

# Restart Jenkins
sudo systemctl restart jenkins

### 6.2 DockerHub Repository
1. Create repository on DockerHub
2. Note repository name for Jenkinsfile

## 7. Pipeline Configuration

### 7.1 Jenkinsfile


## 8. Testing and Deployment

### 8.1 Testing Pipeline
1. Make code changes
2. Commit and push to GitHub
3. Monitor Jenkins pipeline execution
4. Verify DockerHub repository

### 8.2 Manual Testing

```bash
# Pull and run container
docker pull your-dockerhub-username/your-app-name:latest
docker run -p 5000:5000 your-dockerhub-username/your-app-name:latest

# Test application
curl http://localhost:5000
```

