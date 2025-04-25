FROM nginx:alpine

  
RUN ls -al
# Copy static site

COPY index.html /usr/share/nginx/html/index.html

  

# Health‑check (great for orchestration tools)

HEALTHCHECK CMD wget -qO- http://lohost || exit 1
