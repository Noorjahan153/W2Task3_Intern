Project Overview

This project demonstrates a Strapi CMS setup running with PostgreSQL as the database and Nginx as a reverse proxy using Docker. The setup uses Docker Compose to manage all containers.

Services

PostgreSQL (postgres:15)
Database for Strapi
User: strapi

Password: strapi123
Database: strapidb
Strapi (Custom Docker image)

CMS application

Connects to PostgreSQL
Nginx (nginx:latest)
Reverse proxy for Strapi

Commands Used
Docker & Compose

# Navigate to project folder
cd W2Task3

# Build Docker image for Strapi
docker build -t w2task3-strapi ./strapi-app

# Start all services in detached mode
docker compose up -d

# Check running containers
docker ps

# Stop containers
docker compose down

Notes

* Strapi admin panel runs on http://localhost:1337/admin

* Nginx proxy runs on http://localhost

* PostgreSQL runs on port 5432

* Used Docker Compose to manage all services




