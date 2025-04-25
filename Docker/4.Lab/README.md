
# 🐳 Docker Hands‑On Labs  
**Topics:** Core Components • Networking • Storage • Images • Registries  

---

## 📑 Command Cheat‑Sheet

| Command | What it does |
|---------|--------------|
| `docker ps [-a]` | List running (**or all**) containers |
| `docker images` | List local images |
| `docker pull <image>` / `docker push <image>` | Download / upload image to a registry |
| `docker run -d --name <c>` | Start a container in detached mode |
| `docker exec -it <c> <cmd>` | Run interactive command inside a container |
| `docker build -t <img>:tag .` | Build image from `Dockerfile` |
| `docker network create <net>` | Create user‑defined network |
| `docker volume create <vol>` | Create named volume |
| `docker inspect <object>` | Low‑level details (JSON) |
| `docker system prune` | Remove unused data |

---

## Lab 1 – Docker Basics & Components

### 🎯 Goal
Understand the roles of the **Docker client**, **daemon**, **registry**, **image**, and **container**.

### 🛠️ Steps
1. Verify the daemon:  
   ```bash
   docker info
   ```
2. Pull and run the hello‑world image:  
   ```bash
   docker run --name hello hello-world
   ```
3. Check container metadata:  
   ```bash
   docker inspect hello
   ```
4. Examine the image layers:  
   ```bash
   docker history hello-world
   ```

<details>
<summary>✅ Solution & What to Observe</summary>

* `hello-world` image downloads from Docker Hub (the registry).  
* The CLI talks to the **dockerd** daemon via the Unix socket or TCP.  
* `docker inspect hello` shows the container’s config and the network settings.</details>

---

## Lab 2 – Build Your Own Image

### 🎯 Goal  
Create an Nginx image with a custom landing page using a **multi‑stage Dockerfile**.

### 🛠️ Steps
1. Create a directory `lab2` with:
   * `index.html`
   * `Dockerfile`
2. **Dockerfile**
   ```dockerfile
   FROM nginx:alpine AS runtime
   COPY index.html /usr/share/nginx/html/index.html
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```
3. Build & run:  
   ```bash
   docker build -t mynginx:dev .
   docker run -d --name web -p 8080:80 mynginx:dev
   ```
4. Browse to `http://localhost:8080`.

<details>
<summary>✅ Solution</summary>

* Use `docker build --progress=plain .` to watch layer caching.  
* `docker exec web nginx -v` proves Nginx is inside.</details>

---

## Lab 3 – Networking Deep‑Dive

### 🎯 Goal  
Compare the default `bridge`, **user‑defined bridge**, and **host** modes.

### 🛠️ Steps
1. Create a bridge network:
   ```bash
   docker network create app-net
   ```
2. Launch two Alpine containers:
   ```bash
   docker run -dit --name app1 --network app-net alpine sh
   docker run -dit --name app2 --network app-net alpine sh
   ```
3. Inside **app1**:
   ```bash
   ping -c3 app2        # DNS works
   ```
4. Test host networking:
   ```bash
   docker run --rm --network host alpine ip addr show
   ```

<details>
<summary>✅ Solution</summary>

* On `app-net`, each container resolves the other’s name via Docker’s embedded DNS.  
* In `host` mode the container shares the host’s network stack (no port mapping needed).</details>

---

## Lab 4 – Persistent Storage

### 🎯 Goal  
Use **bind mounts** and **named volumes** to keep data after a container is deleted.

### 🛠️ Steps
1. Named volume example (PostgreSQL):
   ```bash
   docker volume create pgdata
   docker run -d --name db \
     -e POSTGRES_PASSWORD=pass \
     -v pgdata:/var/lib/postgresql/data \
     postgres:16
   ```
2. Verify data persists:
   ```bash
   docker rm -f db
   docker run --name db2 -d \
     -e POSTGRES_PASSWORD=pass \
     -v pgdata:/var/lib/postgresql/data \
     postgres:16
   ```
3. Bind mount example:
   ```bash
   mkdir $(pwd)/html
   echo "Hi at $(date)" > html/index.html
   docker run -d --name web2 \
     -p 9090:80 \
     -v $(pwd)/html:/usr/share/nginx/html:ro \
     nginx:alpine
   ```

<details>
<summary>✅ Solution</summary>

* `docker volume inspect pgdata` shows where Docker stores the volume on the host.  
* Bind mounts mirror live changes; edit `html/index.html` and refresh the browser.</details>

---


## 🧹 Clean‑up

```bash
docker container prune -f   # remove stopped containers
docker image prune -f       # dangling images
docker volume prune -f
docker network prune -f
```

---
