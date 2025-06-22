
## Docker Lab Assignment

### 1. Create a Single-Stage Dockerfile

- Write a Dockerfile to build and run a Node.js application using the provided code base.
- The application should run on port 80.

---

### 2. Create a Multi-Stage Dockerfile

- Write a multi-stage Dockerfile for the same Node.js application.
- The final image should be as small as possible and only contain production dependencies.

---

### 3. Write a Bash Script

- Create a bash script that:
    - Builds the Docker image.
    - Runs the container on port 80.
    - Takes a screenshot of the running app (you can use any tool or method you prefer).
    - Shows where the screenshot is saved.

---

### 4. Bind Data with Docker Volumes

- Modify your script to:
    - Create Docker volumes or bind mounts to persist application data outside the container.

---

**Hint:**
You can use the following Dockerfile as a reference for your work:

```dockerfile
FROM node:18-slim

```

---

Good luck!

