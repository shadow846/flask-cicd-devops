# Flask CI/CD DevOps Project

This project demonstrates a full CI/CD pipeline for a Flask web application using:

- **Git & GitHub** for source control
- **Jenkins** for CI/CD automation
- **Docker** to build and push containerized applications
- **Kubernetes (Minikube)** to deploy and manage the application

---

## Project Structure

flask-cicd-devops/
├── app/
│ ├── app.py
│ ├── requirements.txt
│ └── Dockerfile
├── jenkins/
│ └── Jenkinsfile
├── k8s/
│ ├── deployment.yaml
│ └── service.yaml
└── README.md


---

## 1. Flask Application

A simple Flask app that returns "Hello, World!" from a single endpoint.

**app/app.py**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, World!"

Docker 

FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]

docker build -t goutham0842/flask-cicd-pipeline app/
docker run -p 5000:5000 goutham0842/flask-cicd-pipeline

3. Jenkins CI/CD Pipeline

Jenkinsfile

pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/shadow846/flask-cicd-devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh 'docker build -t goutham0842/flask-cicd-pipeline .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push goutham0842/flask-cicd-pipeline
                    '''
                }
            }
        }
    }
}


minikube start --driver=docker --memory=3000 --cpus=2
kubectl config use-context minikube


Deployment.yaml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-container
        image: goutham0842/flask-cicd-pipeline
        ports:
        - containerPort: 5000


Service.yaml

apiVersion: v1
kind: Service
metadata:
  name: flask-app-service
spec:
  type: NodePort
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
      nodePort: 30036


cd k8s
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

minikube service flask-app-service

to get it manually

minikube ip
kubectl get svc


This project demonstrates how to build a complete CI/CD pipeline using Git, Jenkins, Docker, and Kubernetes to automate the process of testing, building, containerizing, and deploying a Flask web application.


Author
Goutham – DevOps Trainee | GitHub: shadow846
