## Lab 2 – Building Images with Dockerfiles (≈ 30 min)

  

### 🎯 Objectives

| Concept | Details |

|---------|---------|

| Dockerfile syntax | `FROM`, `RUN`, `COPY`, `CMD`, `ENTRYPOINT`, `ENV` |

| Build cache | Layer re‑use speeds up rebuilds |

  

### 📝 Steps

```text

lab2-static-site/

├── Dockerfile

└── index.html

```

  

*`index.html`* – keep it minimal.

  

```Dockerfile


FROM nginx:alpine

  

# Copy static site

COPY index.html /usr/share/nginx/html/index.html

  

```

  

```bash

# Build & tag

docker build -t static-site:1.0 .

  

# Run

docker run -d --name site -p 8081:80 static-site:1.0

  

# Test

curl http://localhost:8081

```

```bash 
docker build --no-cache -t my-image:latest .
```

```bash 
RELEASE_VERSION=$(git log -1 --pretty=format:%H | head -c 9)

docker build -t static-site:$RELEASE_VERSION .


```
### ✅ Key takeaways

* **Layer ordering** matters – put rarely‑changing commands early.

* Prefer **multi‑stage builds** to drop compilers or Node tooling from the final image.

* Never rely on `latest` for CI/CD; tag with git SHA or semantic version.