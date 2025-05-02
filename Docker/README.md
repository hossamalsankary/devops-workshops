Docker Labs – Mentor Guide
A five-part lab series that walks learners from Docker basics to a small end‑to‑end project. Each lab builds on the previous one, so feel free to mix‑and‑match or skip ahead depending on your cohort’s experience.

🛠️ Prerequisites for All Labs
A laptop with Docker Desktop (Windows/macOS) or Docker CE on Linux.
A code editor (e.g. VS Code).
Internet access (to pull images from Docker Hub).
Basic command‑line skills.
For Linux:

# Ubuntu / Debian
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER   # log out / in after this step
For Windows/macOS, download Docker Desktop from https://www.docker.com/products/docker-desktop.

Lab 1 – Docker Basics (≈ 20 min)
Objectives
Install Docker and verify the engine is running.
Pull and run containers.
Explore containers and images with inspection commands.
Steps
Verify installation
docker version
docker info | head
Run the hello‑world image
docker run hello-world
Start an Nginx container (detached, port 8080 → 80):
docker run -d --name web -p 8080:80 nginx:latest
List and inspect
docker ps            # running containers
docker ps -a         # all containers
docker inspect web   # deep JSON metadata
docker logs web      # container logs
Clean up
docker stop web && docker rm web
docker image rm nginx:latest
Checkpoint: Opening http://localhost:8080 in a browser should show the Nginx welcome page.

Optional challenges

Map a different host port (e.g. 9090) without rebuilding.
Use docker stats to watch live CPU / memory usage.
Lab 2 – Building Images with Dockerfiles (≈ 30 min)
Objectives
Understand layers and the build cache.
Create a custom image that serves a static HTML landing page.
Steps
Project scaffold
lab2-static-site/
├── Dockerfile
└── index.html
Author index.html – keep it minimal (add student names for fun).
Write the Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
HEALTHCHECK CMD wget -qO- http://localhost || exit 1
Build & tag
docker build -t static-site:lab2 .
Run & test
docker run -d -p 8081:80 --name site static-site:lab2
curl http://localhost:8081
Iterate – edit index.html, rebuild, and observe layer caching (--no-cache to skip).
Talking points

Why small base images (Alpine) matter.
Difference between COPY and ADD.
Image tags and versioning strategy.
Lab 3 – Docker Compose: WordPress + MySQL (≈ 40 min)
Objectives
Use docker‑compose to define multi‑container applications.
Manage lifecycle with a single command.
Set environment variables and named volumes.
Steps
Compose file (docker-compose.yml)
version: "3.9"
services:
  db:
    image: mysql:8.0
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: wp_root_pw
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_user_pw
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:6.5-php8.2-apache
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_user_pw
    depends_on:
      - db
volumes:
  db_data:
Kick it off
docker compose up -d
Verify – browse to http://localhost:8082 and complete the WordPress setup wizard.
Troubleshoot
docker compose logs -f db
docker compose exec db mysql -u root -pwp_root_pw -e "SHOW DATABASES;"
Teardown
docker compose down -v   # also removes named volumes
Quick wins

Explain depends_on ordering vs. true health checks.
Show live file edits using docker compose restart wordpress.
Lab 4 – Networking & Volumes Deep‑Dive (≈ 30 min)
Objectives
Create user‑defined bridge networks for service‑to‑service discovery.
Persist data across container restarts with named volumes.
Part A: Custom Network
docker network create lab-net
docker run -dit --name box1 --network lab-net alpine sh
docker run -dit --name box2 --network lab-net alpine sh
docker exec box1 ping -c 3 box2
Discuss how Docker adds containers to an embedded DNS on that network.

Part B: Volume Persistence for MySQL
docker volume create mysql-persist
docker run -d --name mysqldb   -e MYSQL_ROOT_PASSWORD=lab123   -v mysql-persist:/var/lib/mysql   mysql:8
docker rm -f mysqldb
docker run -d --name mysqldb   -e MYSQL_ROOT_PASSWORD=lab123   -v mysql-persist:/var/lib/mysql   mysql:8
Discussion starters

Bind mounts vs. named volumes.
When to use overlay networks (Swarm/K8s) vs. bridge.
Lab 5 – End‑to‑End Project: Containerized Microservice + CI/CD (≈ 60 min)
Scenario
You have a simple Node.js REST API and a React front‑end in one Git repository.

Tasks
Create a multi‑stage Dockerfile for each service (back‑end & front‑end).
Use docker‑compose to run the full stack with an Nginx reverse proxy.
Push both images to Docker Hub or a private registry.
Configure a minimal GitHub Actions workflow that automatically builds & pushes on every main commit.
(Bonus) Run trivy image to scan for vulnerabilities and fail the pipeline on critical CVEs.
Suggested milestones

Milestone	Command / Check
Build images locally	docker build -t user/api:1.0 backend/
Local integration test	docker compose -f deploy/docker-compose.yml up
Push to registry	docker push user/api:1.0
CI pipeline green	Check GitHub Actions run page
Wrap‑up Discussion
Image provenance & SBOM basics.
Next steps: orchestration with Kubernetes or Docker Swarm.
General Teaching Tips
Demo first, then let them try – a 5‑minute live demo helps newcomers see the goal.
Encourage using --help flags to build muscle memory.
Sprinkle small “what if…?” questions to keep the pace.
Use docker system prune at the end of each lab to free disk space.
Happy containerizing!
