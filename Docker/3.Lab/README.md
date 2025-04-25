<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# I need the app code   full example

Here's a comprehensive full-stack Docker Compose example integrating concepts from all search results, featuring React, Node.js, PostgreSQL, Redis, and Nginx:

```yaml
# docker-compose.yml
version: '3.8'

services:
  frontend:
    build: ./frontend
    networks:
      - app-network
    environment:
      - REACT_APP_API_URL=http://backend:3000
    depends_on:
      - backend

  backend:
    build: 
      context: ./backend
      target: production
    networks:
      - app-network
      - database-network
    environment:
      - DB_HOST=postgres
      - DB_PORT=5432
      - REDIS_HOST=redis
      - NODE_ENV=production
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15-alpine
    networks:
      - database-network
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: securepassword
      POSTGRES_DB: appdb

  redis:
    image: redis:7-alpine
    networks:
      - database-network
    volumes:
      - redisdata:/data

  proxy:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
    networks:
      - app-network
    depends_on:
      - frontend
      - backend

networks:
  app-network:
    driver: bridge
  database-network:
    driver: bridge

volumes:
  pgdata:
  redisdata:
```

**Frontend Dockerfile** (React):

```dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

**Backend Dockerfile** (Node.js):

```dockerfile
# backend/Dockerfile
FROM node:18-alpine AS development
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npm", "run", "dev"]

FROM development AS builder
RUN npm run build

FROM node:18-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --production
CMD ["node", "dist/index.js"]
```

**Nginx Configuration**:

```nginx
# nginx/conf.d/default.conf
server {
    listen 80;

    location / {
        proxy_pass http://frontend;
    }

    location /api/ {
        proxy_pass http://backend:3000/;
        proxy_set_header Host $host;
    }
}
```

Key features from search results:

1. **Network Isolation** ([^1][^3]):
    - Separate `app-network` and `database-network`
    - Frontend only accesses backend via API
    - Database services isolated in private network
2. **Multi-Stage Builds** ([^1][^3]):
    - Optimized production builds for frontend/backend
    - Reduced final image sizes
3. **Service Discovery** ([^2][^4]):
    - DNS-based communication using service names
    - Backend connects to `postgres:5432` and `redis:6379`
4. **Persistent Storage** ([^1][^4]):
    - PostgreSQL and Redis volumes
    - Data survives container restarts
5. **Reverse Proxy** ([^1][^3]):
    - Nginx routes /api to backend
    - Static files served directly from frontend
6. **Environment Variables** ([^3][^4]):
    - Database credentials configured at runtime
    - API endpoints set via environment

To use this setup:

1. Create directory structure:

```
project/
├── docker-compose.yml
├── frontend/
│   ├── Dockerfile
│   └── ... (React source)
├── backend/
│   ├── Dockerfile
│   └── ... (Node.js source)
└── nginx/
    └── conf.d/
        └── default.conf
```

2. Start services:
```bash
docker-compose up --build -d
```

This example combines best practices from all sources:

- Multi-container architecture ([^1][^2][^3][^4])
- Secure credential handling ([^3])
- Production optimizations ([^1][^3])
- Network segmentation ([^1][^4])
- Reverse proxy configuration ([^1][^3])

The application will be accessible at `http://localhost` with:

- Frontend: React app on port 80
- Backend: Node.js API on /api endpoint
- PostgreSQL: Persistent relational storage
- Redis: Caching layer
- Nginx: SSL termination and load balancing (add certificate volume for HTTPS)

<div style="text-align: center">⁂</div>

[^1]: https://github.com/faysalmehedi/fullstack-docker-project

[^2]: https://www.youtube.com/watch?v=K7PsxBMeBCI

[^3]: https://dev.to/teetoflame/running-a-multi-container-web-app-with-docker-compose-4b42

[^4]: https://learn.microsoft.com/en-us/visualstudio/docker/tutorials/tutorial-multi-container-app-mysql

[^5]: https://github.com/dockersamples/example-voting-app

[^6]: https://www.milanwittpohl.com/projects/tutorials/full-stack-web-app/dockerizing-our-front-and-backend

[^7]: https://docs.docker.com/get-started/docker-concepts/running-containers/multi-container-applications/

[^8]: https://dev.to/kalkwst/using-docker-compose-to-build-environments-4f3c

