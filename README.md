# Strapi + PostgreSQL + Nginx Dockerized Setup

## Overview

This project demonstrates a fully **Dockerized Strapi application** connected to **PostgreSQL** and served via **Nginx reverse proxy**. A user-defined Docker network is used to connect all services.  

The project was developed from scratch, Dockerized, and tested locally.

---

## Features

- Containerized **Strapi** application  
- Connected **PostgreSQL** database  
- Configured **Nginx** as a reverse proxy  
- User-defined Docker network for inter-service communication  
- Admin panel accessible via browser at `http://localhost:1337`  

---

## Project Structure

W2Task3/
│
├─ strapi-app/ # Strapi application folder
│ ├─ package.json
│ ├─ package-lock.json
│ ├─ node_modules/
│ ├─ build/
│ └─ ...other Strapi files
│
├─ nginx/
│ └─ default.conf # Nginx configuration
│
├─ Dockerfile # Strapi Dockerfile
├─ docker-compose.yml
└─ README.md


---

## Commands Used

# Navigate to project folder
cd W2Task3

# Clean old builds
powershell Remove-Item -Recurse -Force node_modules, build, .cache, dist

# Build Docker images
docker compose build

# Start containers in detached mode
docker compose up -d

# View running containers
docker ps

# Stop and remove containers
docker compose down

# Strapi admin panel
http://localhost:1337

# Nginx reverse proxy
http://localhost

Dockerfile-code

## Dockerfile (Strapi)/docker-compose.yml

```dockerfile
FROM node:20-bullseye-slim

WORKDIR /app

# Install dependencies needed for building packages
RUN apt-get update && apt-get install -y python3 build-essential g++ git

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy application code
COPY . .

# Build Strapi admin panel
RUN npm run build --if-present

# Expose Strapi port
EXPOSE 1337

# Start Strapi
CMD ["npm", "run", "start"]

**docker-compose.yml**

services:
  postgres:
    image: postgres:15
    container_name: postgres
    environment:
      POSTGRES_USER: strapi
      POSTGRES_PASSWORD: strapi123
      POSTGRES_DB: strapidb
    ports:
      - "5432:5432"
    networks:
      - strapi-net

  strapi:
    build: ./strapi-app
    container_name: strapi
    depends_on:
      - postgres
    environment:
      DATABASE_CLIENT: postgres
      DATABASE_HOST: postgres
      DATABASE_PORT: 5432
      DATABASE_NAME: strapidb
      DATABASE_USERNAME: strapi
      DATABASE_PASSWORD: strapi123
    ports:
      - "1337:1337"
    networks:
      - strapi-net

  nginx:
    image: nginx:latest
    container_name: nginx
    depends_on:
      - strapi
    ports:
      - "80:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    networks:
      - strapi-net

networks:
  strapi-net:
    driver: bridge

**Nginx default.conf**

server {
    listen 80;

    server_name localhost;

    location / {
        proxy_pass http://strapi:1337;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


Notes / Issues Faced

* npm install sometimes failed if run from the wrong folder. Always run inside strapi-app.

* Admin panel build can take several minutes.

* Nginx container failed if default.conf path or content was incorrect.

* PowerShell rm -rf doesn’t work; Remove-Item -Recurse -Force used instead.

* ENOENT errors for /app/node_modules/@strapi/admin/... were fixed by cleaning builds and rebuilding Docker image.

Conclusion

This project demonstrates a fully containerized development environment for Strapi with PostgreSQL and Nginx:

Easy deployment of services

Isolation of each service in its own container

User-defined Docker network for secure inter-service communication

Scalable setup for future enhancements

The environment ensures a consistent workflow across machines and can be extended to production-ready deployments.






