# Docker Lab: Images, Containers, and Port Mapping


## Lab Overview

**Objectives:**
- Build Docker images from a Dockerfile
- Run containers from images
- Map ports between host and container
- Use sample files in containers

---

## Lab Steps

### 1. Build a Simple Docker Image

**Task:**  
Create a Dockerfile that installs `curl` and runs a simple command.

**Instructions:**
1. Create a file named `Dockerfile` with the following content:
    ```dockerfile
    FROM alpine:latest
    RUN apk add --no-cache curl
    CMD ["curl", "google.com"]
    ```
2. Build the image:
    ```
    docker build -t curl-test .
    ```
3. Run the container:
    ```
    docker run --rm curl-test
    ```
4. Observe the output in your terminal (HTML from Google).

---

### 2. Serve a Static HTML Page with Port Mapping

**Task:**  
Create a Docker image that serves a static HTML page and map the container port to your host.

**Instructions:**
1. Create a file named `index.html`:
    ```html
    
    
    Hello Docker
    Hello, Docker!
    
    ```
2. Create a `Dockerfile`:
    ```dockerfile
    FROM nginx:latest
    COPY index.html /usr/share/nginx/html/index.html
    EXPOSE 8080
    ```
3. Build the image:
    ```
    docker build -t static-site .
    ```
4. Run the container with port mapping:
    ```
    docker run -p 8080:80 static-site
    ```
5. Open your browser and visit `http://localhost:8080` to see the page.
