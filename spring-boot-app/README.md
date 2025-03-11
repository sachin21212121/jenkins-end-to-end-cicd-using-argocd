# Spring Boot CICD with Jenkins, ArgoCD, Docker, and SonarQube

This project demonstrates a complete CI/CD pipeline using **Jenkins, ArgoCD, Docker, and SonarQube** to automate the deployment of a Spring Boot application.

##  Project Overview

- **Tech Stack:** Java 11, Spring Boot, Jenkins, ArgoCD, Docker, Kubernetes, SonarQube
- **Pipeline Stages:**
  1. **Checkout Code** - Clones the GitHub repository
  2. **Build & Test** - Uses Maven to build the project and create a JAR file
  3. **Static Code Analysis** - Runs SonarQube for code quality checks
  4. **Build & Push Docker Image** - Creates a Docker image and pushes it to Docker Hub
  5. **Update Kubernetes Deployment** - Modifies the Kubernetes YAML file and updates the GitHub repository
  6. **ArgoCD Sync** - Ensures deployment is updated via ArgoCD

## 📁 Project Structure
```
├── java-maven-sonar-argocd-helm-k8s
│   ├── spring-boot-app               # Spring Boot application
│   │   ├── src                        # Java source code
│   │   ├── target                     # Built JAR file
│   │   ├── Dockerfile                 # Docker build file
│   │   ├── pom.xml                    # Maven configuration
│   ├── spring-boot-app-manifests      # Kubernetes deployment files
│   │   ├── deployment.yml             # Deployment definition
│   │   ├── service.yml                # Service definition
├── Jenkinsfile                         # CI/CD pipeline definition
├── README.md                           # Project documentation
```

## 🔧 Prerequisites

Make sure you have the following installed:
- **JDK 11** ([Download](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html))
- **Docker** ([Install](https://docs.docker.com/get-docker/))
- **Maven** ([Install](https://maven.apache.org/install.html))
- **Jenkins** ([Setup](https://www.jenkins.io/doc/book/installing/))
- **SonarQube** ([Setup](https://docs.sonarqube.org/latest/setup/get-started-2-minutes/))
- **ArgoCD** ([Install](https://argo-cd.readthedocs.io/en/stable/getting_started/))

## 🔨 Setup Instructions

### 1️⃣ Clone the Repository
```sh
git clone https://github.com/sachin21212121/jenkins-end-to-end-cicd-using-argocd.git
cd jenkins-end-to-end-cicd-using-argocd
```

### 2️⃣ Build the Spring Boot Application
```sh
cd java-maven-sonar-argocd-helm-k8s/spring-boot-app
mvn clean package
```

### 3️⃣ Run SonarQube Analysis
```sh
mvn sonar:sonar -Dsonar.login=<YOUR_SONARQUBE_TOKEN> -Dsonar.host.url=http://34.201.116.83:9000
```

### 4️⃣ Build & Push Docker Image
```sh
docker build -t sachin2223/spring-boot-image:latest .
docker login
docker push sachin2223/spring-boot-image:latest
```

### 5️⃣ Deploy to Kubernetes
```sh
kubectl apply -f java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
kubectl apply -f java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/service.yml
```

### 6️⃣ Configure ArgoCD
```sh
argocd app create spring-boot-app --repo https://github.com/sachin21212121/jenkins-end-to-end-cicd-using-argocd.git \
  --path java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

### 7️⃣ Trigger Jenkins Pipeline
- Ensure Jenkins is running and configured with the following credentials:
  - **SonarQube Token** (`sonarqube`)
  - **DockerHub Credentials** (`docker-cred`)
  - **GitHub Token** (`github`)
- Manually trigger the Jenkins pipeline or set up webhooks for automation.

## 📜 CI/CD Pipeline Explained

### Jenkinsfile
This defines the Jenkins pipeline stages:
```groovy
pipeline {
  agent { docker { image 'sachin2223/spring-boot-image:latest' } }
  stages {
    stage('Checkout') { steps { git branch: 'main', url: 'https://github.com/sachin21212121/jenkins-end-to-end-cicd-using-argocd.git' } }
    stage('Build and Test') { steps { sh 'mvn clean package' } }
    stage('Static Code Analysis') { steps { sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN' } }
    stage('Build and Push Docker Image') { steps { sh 'docker build -t sachin2223/spring-boot-image:$BUILD_NUMBER . && docker push sachin2223/spring-boot-image:$BUILD_NUMBER' } }
    stage('Update Deployment File') { steps { sh 'sed -i "s/replaceImageTag/$BUILD_NUMBER/g" deployment.yml && git commit -am "Updated image tag" && git push' } }
  }
}
```

## 📢 Conclusion

This project provides a **fully automated DevOps pipeline** that leverages Jenkins, Docker, Kubernetes, and ArgoCD to ensure **seamless deployment** of a Spring Boot application.

🔹 **Got questions?** Feel free to open an issue or contribute to the repository! 🚀


