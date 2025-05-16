# Understanding GitLab CI/CD: A Workshop Guide

## Pipeline Overview

The pipeline in this workshop demonstrates a complete CI/CD workflow with the following stages:

1. Installing dependencies and building the application
2. Running tests
3. Checking for vulnerabilities
4. Building a Docker image
5. Deploying to staging
6. Deploying to production

Let's examine each component in detail.

## Variables Section

```yaml
variables:
  APP_NAME: "hossamalsankary/lap"
  APP_VERSION: "${CI_PIPELINE_IID}"
  DOCKER_REGISTRY_USER: "${DOCKER_REGISTRY_USER}"
  DOCKER_REGISTRY_PASSWORD: "${DOCKER_REGISTRY_PASSWORD}"
```

This section defines global variables used throughout the pipeline:

- **APP_NAME**: The name for your Docker image (repository/image-name format)
- **APP_VERSION**: Uses GitLab's built-in `CI_PIPELINE_IID` variable, which provides a unique, incrementing ID for each pipeline run[^17]
- **DOCKER_REGISTRY_USER** and **DOCKER_REGISTRY_PASSWORD**: Reference variables stored securely in GitLab's CI/CD settings[^3]

Variables help you avoid hard-coding values and make your pipeline more configurable and secure[^17][^3].

## Pipeline Stages

```yaml
stages:
  - install-build
  - test
  - vulnerabilities
  - build-docker-job
  - deploy
  - Staging-deploy
  - production
```

This section defines the order of execution for the pipeline stages[^2]. Jobs in each stage run only after jobs in the previous stage complete successfully. Within a single stage, jobs run in parallel[^16].

## Caching

```yaml
cache:
  key: "node-16-${CI_PROJECT_NAME}"
  paths:
    - node_modules/   
  policy: pull-push
```

Caching speeds up your pipeline by reusing data between runs:

- **key**: Creates a unique cache identifier based on Node.js version and project name
- **paths**: Specifies directories to cache (node_modules in this case)
- **policy**: "pull-push" means the runner will both download the existing cache and update it with any changes[^5]

Caching differs from artifacts in that cache is intended for dependencies, while artifacts pass intermediate build results between stages[^5].

## Job: build-job

```yaml
build-job:
  stage: install-build
  image: node:16-alpine
  script:
    - npm install
```

This job:

- Runs in the **install-build** stage
- Uses the lightweight **node:16-alpine** Docker image
- Executes **npm install** to download and install project dependencies
- Relies on the cache configuration to speed up installation[^13]


## Job: test-job

```yaml
test-job:
  stage: test
  image: node:16-alpine
  script:
    - npm run test:unit
  # artifacts:
  #   paths:
  #     - npm-audit.json
  #   expire_in: 1 week
```

This job:

- Runs in the **test** stage after the build-job completes
- Uses the same Node.js image for consistency
- Executes unit tests defined in your package.json
- Has commented out artifact configuration that could be used to preserve test results


## Job: vulnerabilities-job

```yaml
vulnerabilities-job:
  stage: vulnerabilities
  image: node:16-alpine
  script:
    - npm audit || true  # Continue even if vulnerabilities are found
  allow_failure: true
  # artifacts:
  #   paths:
  #     - npm-audit.json
  #   expire_in: 1 week
```

This job:

- Checks for security vulnerabilities in your dependencies using **npm audit**
- Uses `|| true` to prevent the job from failing if vulnerabilities are found
- Has `allow_failure: true` which means the pipeline will continue even if this job fails[^9]
- Could be configured to save the audit results as artifacts for later review


## Job: build-docker-job

```yaml
build-docker-job:
  image: docker:24.0.2  
  stage: build-docker-job
  services:
    - docker:24.0.2-dind  
  before_script:
    - echo "$DOCKER_REGISTRY_PASSWORD" | docker login -u "$DOCKER_REGISTRY_USER" --password-stdin
  script:
    - echo "Building Docker image..."
    - docker info
    - docker build -t $APP_NAME:$APP_VERSION .
    - docker push $APP_NAME:$APP_VERSION
  after_script:
    - docker logout $DOCKER_REGISTRY
```

This job demonstrates a more complex configuration:

- Uses the official **docker:24.0.2** image for Docker commands
- Utilizes Docker-in-Docker (dind) service to enable Docker commands within the CI environment
- Has three script sections:
    - **before_script**: Logs in to the Docker registry using secure credentials
    - **script**: Builds and tags the Docker image using the defined variables, then pushes it to the registry
    - **after_script**: Logs out from the Docker registry for security reasons[^6]


## Job: Staging-deploy

```yaml
Staging-deploy:
  stage: Staging-deploy
  image: willhallonline/ansible:2.15-alpine-3.18
  variables:
    ANSIBLE_HOST_KEY_CHECKING: "False"
  script:
    - echo "Deploying version $APP_VERSION to staging..."
    - cd Ansbile/1-Lab/code
    - ansible-playbook -i inventory.ini web-server.yaml 
      -e "ansible_host=${STAGING_HOST}"
      -e "ansible_user=${STAGING_HOST_USER}"
      -e "ansible_password=${STAGING_HOST_PASSWORD}"
      -e "app_version=$APP_VERSION" 
      -e "docker_registry_user=$DOCKER_REGISTRY_USER" 
      -e "docker_registry_password=$DOCKER_REGISTRY_PASSWORD"
```

This job:

- Uses an Ansible image to handle deployment automation
- Disables host key checking for simplicity in a workshop environment
- Runs an Ansible playbook located in the repository
- Passes multiple environment variables to the playbook for configuration
- The playbook likely configures servers to pull and run the Docker image built in the previous step


## Job: production-deploy

```yaml
production-deploy:
  stage: production
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to production"
    - "for you"
  only:
    - main
  when: manual
```

### code
```yaml 
variables:
  APP_NAME: "hossamalsankary/lap"
  APP_VERSION: "${CI_PIPELINE_IID}"
  DOCKER_REGISTRY_USER: "${DOCKER_REGISTRY_USER}"
  DOCKER_REGISTRY_PASSWORD: "${DOCKER_REGISTRY_PASSWORD}"

stages:
  - install-build
  - test
  - vulnerabilities
  - build-docker-job
  - deploy
  - Staging-deploy
  - production

cache:
  key: "node-16-${CI_PROJECT_NAME}"
  paths:
    - node_modules/   
  policy: pull-push    

build-job:
  stage: install-build
  image: node:16-alpine
  script:
    - npm install


test-job:
  stage: test
  image: node:16-alpine
  script:
    - npm run  test:unit
  # artifacts:
  #   paths:
  #     - npm-audit.json
  #   expire_in: 1 week

vulnerabilities-job:
  stage: vulnerabilities
  image: node:16-alpine
  script:
    - npm audit || true  # Continue even if vulnerabilities are found
  allow_failure: true
  # artifacts:
  #   paths:
  #     - npm-audit.json
  #   expire_in: 1 week

build-docker-job:

  image: docker:24.0.2  
  stage: build-docker-job
  services:
    - docker:24.0.2-dind  
  before_script:
    - echo "$DOCKER_REGISTRY_PASSWORD" | docker login -u "$DOCKER_REGISTRY_USER" --password-stdin
  script:
    - echo "Building Docker image..."
    - docker info
    - docker build -t $APP_NAME:$APP_VERSION .
    - docker push $APP_NAME:$APP_VERSION
  after_script:
    - docker logout $DOCKER_REGISTRY




Staging-deploy:
  stage: Staging-deploy
  image: willhallonline/ansible:2.15-alpine-3.18

  variables:
    ANSIBLE_HOST_KEY_CHECKING: "False"
  script:
    - echo "Deploying version $APP_VERSION to staging..."
    - cd Ansbile/1-Lab/code
    - ansible-playbook -i inventory.ini web-server.yaml 
      -e "ansible_host=${STAGING_HOST}"
      -e "ansible_user=${STAGING_HOST_USER}"
      -e "ansible_password=${STAGING_HOST_PASSWORD}"
      -e "app_version=$APP_VERSION" 
      -e "docker_registry_user=$DOCKER_REGISTRY_USER" 
      -e "docker_registry_password=$DOCKER_REGISTRY_PASSWORD"


production-deploy:
  stage: production
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to production"
    - "for you"
  only:
    - main
  when: manual
```