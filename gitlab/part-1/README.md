## Lab 1: Git Fundamentals with GitLab - Local and Remote Repository Workflow


### Objectives

In this lab, participants will learn:

- How to clone a GitLab repository
- How to work with Git branches locally
- How to make and commit changes to files
- How to push and pull changes to/from GitLab
- How to effectively manage the Git workflow


### Prerequisites

- Git installed on your local machine
- A GitLab account
- Basic command line knowledge
- SSH access to the internet


### Task A: Verify Git Installation

1. Open a terminal on your computer and run:

```bash
git version
```

2. If a version number appears, Git is installed. If not, install Git following the official documentation[^2].

### Task B: Generate SSH Keys for GitLab Authentication

1. Verify your OpenSSH client version:

```bash
ssh -V
```

2. Generate a public-private key pair:

```bash
ssh-keygen
```

3. At the first prompt, accept the default location (`~/.ssh/id_rsa`) by pressing Enter, or specify a custom path.
4. Set a secure password for your SSH key when prompted (recommended).
5. Find your public key:

```bash
cat ~/.ssh/id_rsa.pub
```

6. Copy the entire output from the command above[^2].

### Task C: Add SSH Key to GitLab

1. Log in to your GitLab account.
2. Navigate to User Settings > SSH Keys.
3. Paste your public key in the "Key" field.
4. Give your key a descriptive title.
5. Click "Add key" to save your SSH key.

### Task D: Clone a Repository

1. Navigate to a sample project from GitLab Learn Labs.
2. Click the "Clone" button and copy the SSH URL.
3. In your terminal, navigate to the directory where you want to clone the repository:

```bash
git clone git@gitlab.com:gitlab-learn-labs/sample-projects/basic-cicd-workshop/basic-cicd-workshop.git
```

4. Navigate into the cloned repository:

```bash
cd basic-cicd-workshop
```


### Task E: Create and Work with Branches

1. Create a new branch for your work:

```bash
git checkout -b feature/my-new-feature
```

2. Confirm you're on the new branch:

```bash
git branch
```

3. Make a simple change to the README.md file using your preferred text editor.
4. Check the status of your changes:

```bash
git status
```

5. Add your changes to the staging area:

```bash
git add README.md
```

6. Commit your changes with a descriptive message:

```bash
git commit -m "Update README with additional information"
```


### Task F: Push Changes to GitLab

1. Push your branch to the remote repository:

```bash
git push -u origin feature/my-new-feature
```

2. Navigate to the GitLab web interface to verify your branch was pushed successfully.
3. Create a Merge Request from your branch to the main branch.

### Task G: Pull Changes from GitLab

1. Switch back to the main branch:

```bash
git checkout main
```

2. Pull the latest changes from the remote repository:

```bash
git pull
```

3. Merge your feature branch into main:

```bash
git merge feature/my-new-feature
```

4. Resolve any merge conflicts if necessary.
5. Push the merged changes to GitLab:

```bash
git push
```


## Lab 2: Building CI/CD Pipelines with GitLab

Estimated time to complete: 60 minutes

### Objectives

In this lab, participants will learn:

- How to create and configure a `.gitlab-ci.yml` file
- How to define stages and jobs in a CI/CD pipeline
- How to run automated tests and builds
- How to use CI/CD variables
- How to work with artifacts and the GitLab Docker Registry


### Prerequisites

- GitLab account with a project created
- Basic understanding of YAML syntax
- Familiarity with Git commands


### Task A: Create a New GitLab Project with CI/CD Configuration

1. Sign in to GitLab and navigate to your groups.
2. Create a new project:
    - Click "New project" > "Create blank project"
    - Name it "CI-CD-Workshop"
    - Set visibility to "Private"
    - Check "Initialize repository with a README"
    - Click "Create project"[^3]
3. Create a `.gitlab-ci.yml` file:
    - Navigate to your project
    - Click the "+" button > "This directory" > "New file"
    - Name the file `.gitlab-ci.yml`[^3]
4. Start with a basic CI configuration:

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - echo "Building the application..."
    - echo "Build complete."

test-job:
  stage: test
  script:
    - echo "Running tests..."
    - echo "Test complete."

deploy-job:
  stage: deploy
  script:
    - echo "Deploying application..."
    - echo "Application successfully deployed."
```

5. Commit the file with a message like "Add initial CI/CD configuration".

### Task B: Examine and Run Your First Pipeline

1. Navigate to "CI/CD" > "Pipelines" in your project.
2. You should see your pipeline running automatically after your commit.
3. Click on the pipeline to view the jobs and their status.
4. Review the output of each job to see the executed commands.

### Task C: Enhance Your Pipeline with Variables

1. Edit your `.gitlab-ci.yml` file to include variables:

```yaml
variables:
  APP_NAME: "my-application"
  APP_VERSION: "1.0.0"

stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - echo "Building $APP_NAME version $APP_VERSION"
    - echo "Build complete."

test-job:
  stage: test
  script:
    - echo "Running tests for $APP_NAME"
    - echo "Test complete."

deploy-job:
  stage: deploy
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION"
    - echo "Application successfully deployed."
```

2. Commit your changes and observe the new pipeline run.

### Task D: Add Artifacts to Your Pipeline

1. Update your `.gitlab-ci.yml` file to include artifacts:

```yaml
variables:
  APP_NAME: "my-application"
  APP_VERSION: "1.0.0"

stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - echo "Building $APP_NAME version $APP_VERSION"
    - mkdir -p build/
    - echo "Build artifact" > build/build.txt
  artifacts:
    paths:
      - build/
    expire_in: 1 week

test-job:
  stage: test
  script:
    - echo "Running tests for $APP_NAME"
    - cat build/build.txt
    - echo "Test results" > test-results.txt
  artifacts:
    paths:
      - test-results.txt
    expire_in: 1 week

deploy-job:
  stage: deploy
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION"
    - cat build/build.txt
    - cat test-results.txt
    - echo "Deployment successful"
```

2. Commit your changes and observe how artifacts are passed between jobs.

### Task E: Add Conditional Jobs

1. Update your `.gitlab-ci.yml` file to include jobs that run only on specific conditions:

```yaml
variables:
  APP_NAME: "my-application"
  APP_VERSION: "1.0.0"

stages:
  - build
  - test
  - deploy
  - production

build-job:
  stage: build
  script:
    - echo "Building $APP_NAME version $APP_VERSION"
    - mkdir -p build/
    - echo "Build artifact" > build/build.txt
  artifacts:
    paths:
      - build/
    expire_in: 1 week

test-job:
  stage: test
  script:
    - echo "Running tests for $APP_NAME"
    - cat build/build.txt
    - echo "Test results" > test-results.txt
  artifacts:
    paths:
      - test-results.txt
    expire_in: 1 week

deploy-job:
  stage: deploy
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to staging"
    - cat build/build.txt
    - cat test-results.txt
    - echo "Staging deployment successful"

production-deploy:
  stage: production
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to production"
  only:
    - main
  when: manual
```

2. Commit your changes and observe the new pipeline.
3. Note that the production deployment job only appears for the main branch and requires manual approval.

### Task F: Add Basic Security Scanning

1. Update your `.gitlab-ci.yml` file to include a security scanning job:

```yaml
variables:
  APP_NAME: "my-application"
  APP_VERSION: "1.0.0"

stages:
  - build
  - test
  - security
  - deploy
  - production

build-job:
  stage: build
  script:
    - echo "Building $APP_NAME version $APP_VERSION"
    - mkdir -p build/
    - echo "Build artifact" > build/build.txt
  artifacts:
    paths:
      - build/
    expire_in: 1 week

test-job:
  stage: test
  script:
    - echo "Running tests for $APP_NAME"
    - cat build/build.txt
    - echo "Test results" > test-results.txt
  artifacts:
    paths:
      - test-results.txt
    expire_in: 1 week

security-scan:
  stage: security
  script:
    - echo "Running security scans..."
    - echo "No vulnerabilities found"
  allow_failure: true

deploy-job:
  stage: deploy
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to staging"
    - cat build/build.txt
    - cat test-results.txt
    - echo "Staging deployment successful"

production-deploy:
  stage: production
  script:
    - echo "Deploying $APP_NAME version $APP_VERSION to production"
  only:
    - main
  when: manual
```


### Task K: used custom image

1. Update your `.gitlab-ci.yml` file to include a security scanning job:

```yaml
stages:
  - build

variables:
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""  

build-image:
  image: docker:24.0.2  
  stage: build
  services:
    - docker:24.0.2-dind  
  script:
    - echo "Building Docker image..."
    - docker info
    - docker pull alpine:latest
```

ref: https://docs.gitlab.com/ci/services/
