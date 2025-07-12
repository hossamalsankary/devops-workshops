

# GitLab CI/CD Pipeline Project - Docker Integration

## Project Overview

Build a complete CI/CD pipeline for the Docker Example Voting App with **Docker Hub integration**. This project focuses on automating the build, test, security scanning, and deployment process while pushing all images to Docker Hub.

## Application Components

The Example Voting App consists of multiple services that need to be containerized and pushed to Docker Hub:

- **Vote App**: Python Flask web application
- **Result App**: Node.js application displaying results
- **Worker App**: .NET application processing votes
- **Redis**: Message queue (use official Docker Hub image)
- **PostgreSQL**: Database (use official Docker Hub image)


## Required Pipeline Stages

### 1. **Install Stage**

- Install Docker and required tools
- Set up build environment
- Configure Docker Hub authentication


### 2. **Build Stage**

- Build Docker images for all custom applications (vote, result, worker)
- Tag images with proper naming convention
- Use format: `your-dockerhub-username/app-name:tag`


### 3. **Test Stage**

- Run unit tests for each application if it exist



### 4. **Security Scanning with Trivy**

- Install Trivy scanner in pipeline
- Scan all built Docker images for vulnerabilities
- Generate security reports
- Fail pipeline on critical vulnerabilities


### 5. **Push to Docker Hub**

- **Push all built images to Docker Hub**
- Use proper image tagging strategy
- Implement versioning (latest, commit SHA, etc.)


### 6. **Deploy to Test Environment**

- Deploy application using Docker Compose
- Use images from Docker Hub
- Configure environment variables
- replying with Docker composed the plus


### 7. **Deploy to Production**

- Deploy to production with manual approval
- Use Docker Hub images for deployment
- Implement proper deployment strategy
- replying with Docker composed the plus


## Docker Hub Configuration

### Setup Requirements:

1. **Create Docker Hub Account**
    - Sign up at hub.docker.com
    - Create access token for GitLab CI
2. **Configure GitLab Variables**
Go to Project Settings > CI/CD > Variables and add:
    - `CI_REGISTRY`: `docker.io`
    - `CI_REGISTRY_IMAGE`: `index.docker.io/your-username/voting-app`
    - `CI_REGISTRY_USER`: Your Docker Hub username
    - `CI_REGISTRY_PASSWORD`: Your Docker Hub access token
3. **Image Naming Convention**
    - Vote app: `your-username/voting-app-vote:latest`
    - Result app: `your-username/voting-app-result:latest`
    - Worker app: `your-username/voting-app-worker:latest`

### Pipeline Example for Docker Hub Push:


## Required Deliverables

### 1. **Complete .gitlab-ci.yml File**

- All 7 stages implemented
- Docker Hub integration for image pushing
- Proper error handling and dependencies


### 2. **Docker Hub Images**

- All custom application images pushed to Docker Hub
- Proper tagging and versioning
- Public or private repository setup


### 3. **Security Implementation**

- Trivy scanning configuration
- Vulnerability reports
- Security thresholds defined


### 4. **Documentation**

- **README.md** with setup instructions
- **Docker Hub setup guide**
- **Pipeline documentation**
- **Deployment procedures**


### 5. **Screenshots and Evidence**

- Pipeline execution results
- Docker Hub repository with pushed images
- Security scan reports
- Application running in both environments



## Key Instructions

1. **Fork** the repository: https://github.com/dockersamples/example-voting-app
2. **Create** GitLab project and import the code
3. **Set up** Docker Hub account and access tokens
4. **Configure** GitLab CI/CD variables for Docker Hub authentication
5. **Build** all application images and push to Docker Hub
6. **Implement** security scanning with Trivy
7. **Deploy** applications using images from Docker Hub
8. **Document** the complete process with screenshots
9. **Submit** working pipeline with all deliverables

## Success Criteria

- All custom Docker images successfully pushed to Docker Hub
- Pipeline executes all stages without errors
- Security scanning identifies and reports vulnerabilities
- Application deploys and runs in both test and production environments
- Complete documentation with visual evidence provided
