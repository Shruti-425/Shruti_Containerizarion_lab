# Lab Experiment 7: CI/CD using Jenkins, GitHub and Docker Hub

---

##  Aim

To design and implement a complete CI/CD pipeline using Jenkins, integrating source code from GitHub, and building & pushing Docker images to Docker Hub.

---

##  Objectives

* Understand CI/CD workflow using Jenkins (GUI-based tool)
* Create a structured GitHub repository with application + Jenkinsfile
* Build Docker images from source code
* Securely store Docker Hub credentials in Jenkins
* Automate build & push process using webhook triggers
* Use same host (Docker) as Jenkins agent

---

##  Theory

### 1. What is Jenkins?

Jenkins is a web-based GUI automation server used to:

* Build applications
* Test code
* Deploy software

It provides:

* Dashboard (browser-based UI)
* Plugin ecosystem (GitHub, Docker, etc.)
* Pipeline as Code using Jenkinsfile

---

### 2. What is CI/CD?

* **Continuous Integration (CI):** Code is automatically built and tested after each commit
* **Continuous Deployment (CD):** Built artifacts (Docker images) are automatically delivered/deployed

---

### 3. Workflow Overview

```
Developer → GitHub → Webhook → Jenkins → Build → Docker Hub
```

---

### 4. Prerequisites

* Docker & Docker Compose installed
* GitHub account
* Docker Hub account
* Basic Linux command knowledge

---

##  Part A: GitHub Repository Setup

### 1. Create Repository

Create a repository on GitHub:

```
my-app
```

---

### 2. Project Structure

```
my-app/
│── app.py
│── requirements.txt
│── Dockerfile
│── Jenkinsfile
```

---

### 3. Application Code

#### `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from CI/CD Pipeline!"

app.run(host="0.0.0.0", port=80)
```

---

#### `requirements.txt`

```
flask
```

---

### 4. Dockerfile (Build Process)

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 80
CMD ["python", "app.py"]
```

---

### Build Process Explanation

* Source code pushed to GitHub
* Jenkins pulls code
* Dockerfile:

  * Creates environment
  * Installs dependencies
  * Packages app
* Output → Docker Image

---

### 5. Jenkinsfile (Pipeline Definition)

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/myapp"
    }

    stages {

        stage('Clone Source') {
            steps {
                git 'https://github.com/your-username/my-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh 'echo $DOCKER_TOKEN | docker login -u your-dockerhub-username --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}
```

---

##  Part B: Jenkins Setup using Docker

### 1. Create Docker Compose File

```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: always
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    user: root

volumes:
  jenkins_home:
```

---

### 2. Start Jenkins

```bash
docker-compose up -d
```

Access Jenkins:

```
http://localhost:8080/
```

---

### 3. Unlock Jenkins

```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

---

### 4. Initial Setup

* Install suggested plugins
* Create admin user

---

##  Part C: Jenkins Configuration

### 1. Add Docker Hub Credentials

Path:

```
Manage Jenkins → Credentials → Add Credentials
```

* Type: Secret Text
* ID: `dockerhub-token`
* Value: Docker Hub Access Token

---

### 2. Create Pipeline Job

* New Item → Pipeline
* Name: `ci-cd-pipeline`

Configure:

* Pipeline script from SCM
* SCM: Git
* Repository URL: your GitHub repo
* Script Path: `Jenkinsfile`

---

### 3. Run Pipeline

Trigger build and monitor stages in Jenkins dashboard.

---

### 4. Verify in Docker Hub

Check if image is successfully pushed.

---

##  Part D: GitHub Webhook Integration

### Configure Webhook

In GitHub:

```
Settings → Webhooks → Add Webhook
```

* Payload URL:

```
http://<your-server-ip>:8080/github-webhook/
```

* Events: Push events

---

##  Part E: Execution Flow

### Stage 1: Code Push

Developer updates code in GitHub

### Stage 2: Webhook Trigger

GitHub sends event to Jenkins

### Stage 3: Jenkins Pipeline Execution

* **Clone:** Pulls latest code
* **Build:** Docker builds image
* **Auth:** Jenkins logs into Docker Hub
* **Push:** Image pushed to Docker Hub

### Stage 4: Artifact Ready

* Docker image available globally

---

##  Role of Same Host Agent

* Jenkins runs inside Docker
* Docker socket mounted:

```
/var/run/docker.sock
```

### Effect:

* Jenkins controls host Docker directly
* Builds and pushes images without separate agent

---

##  Observations

* Jenkins GUI simplifies CI/CD management
* GitHub acts as source + pipeline definition
* Docker ensures consistent builds
* Webhook enables full automation

---

##  Result

Successfully implemented a complete CI/CD pipeline where:

* Source code and pipeline are maintained in GitHub
* Jenkins automatically detects changes
* Docker image is built on host agent
* Image is securely pushed to Docker Hub

---