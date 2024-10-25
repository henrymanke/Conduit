# Conduit

## Table of Contents

- [Project Description](#project-description)
- [Quickstart](#quickstart)
- [Usage](#usage)
  - [Configuration](#configuration)
  - [Modifying Configuration](#modifying-configuration)
- [Dockerfiles](#dockerfiles)
- [docker-compose.yaml](#docker-composeyaml)
- [Volumes and .dockerignore](#volumes-and-dockerignore)

## Project Description

This repository provides a Docker setup for a full-stack web application with separate frontend and backend services, managed as Git submodules. It includes configuration files for Docker and Docker Compose, and environment-specific setup to streamline the deployment process. 

Key Components:

- **Git Submodules**: The frontend and backend are included as submodules to manage separate repositories.
- **Dockerfiles** for frontend and backend, optimized with multi-stage builds to reduce image sizes.
- **Environment variable configurations** for customizable setup, while keeping sensitive data secure.
- **Volumes** to persist data across container restarts.
- **Health checks** to ensure service availability.
- `docker-compose.yaml` to manage multi-service deployments in a single network.

## Quickstart

### Prerequisites

- Docker and Docker Compose installed.
- Clone the repository with submodules initialized.

### Quickstart Guide

1. Clone the repository with submodules:
   ```bash
   git clone --recurse-submodules https://github.com/henrymanke/Conduit.git
   cd Conduit
   ```

2. Initialize and update submodules if necessary:
   ```bash
   git submodule update --init --recursive
   ```

3. Set up environment variables:
   - Create `.env` file from `sample.env`

    ```bash
    cp sample.env .env
    ```

4. Start the services:
   ```bash
   docker-compose up --build -d
   ```

5. Access the services:
   - Frontend: [http://localhost:8282](http://localhost:8282)
   - Backend: [http://localhost:8000](http://localhost:8000)

## Usage

### Configuration

This project uses `docker-compose.yaml` to manage both frontend and backend services:

- **Frontend**:
  - Hosted as a submodule.
  - Exposes port `8282`.
  - Built using `node:20-alpine` with Nginx for production.

- **Backend**:
  - Hosted as a submodule.
  - Exposes port `8000`.
  - Built with Python 3.6, configured in a multi-stage Dockerfile for efficiency.

### Modifying Configuration

1. **Environment Variables**:
   - Update `.env` files in `frontend` and `backend` directories as needed.

2. **Port Configuration**:
   - Modify port mappings in `docker-compose.yaml` under `ports` if needed.

3. **Volume Configuration**:
   - Adjust Docker volume paths in `docker-compose.yaml` to suit specific file paths.

4. **Health Checks**:
   - Health check configurations for each service can be modified in `docker-compose.yaml` under `healthcheck`.

## Dockerfiles

### Frontend Dockerfile

- **Multi-Stage Build**: Uses `node:20-alpine` for the build, then Nginx for serving the app.
- **Environment Variables**: Managed through Dockerfile or `.env`.
- **Exposed Port**: Port `80` for public access.

### Backend Dockerfile

- **Multi-Stage Build**: Based on Python 3.6 with a three-stage setup to minimize final image size.
- **Environment Variables**: Configured through Dockerfile or `.env`.
- **Exposed Port**: Port `8000` for backend access.

## docker-compose.yaml

The `docker-compose.yaml` defines and configures both services:

- **Services**:
  - Each submodule (frontend and backend) is treated as a separate service.
- **Environment Configuration**:
  - Environment variables configured for each service through `.env` files.
- **Port Mappings**:
  - Exposes necessary ports to make services accessible publicly.
- **Volumes**:
  - Volume configurations ensure data persistence across container restarts.

## Volumes and .dockerignore

- **Persistent Volumes**:
  - Configured in `docker-compose.yaml` for consistent data retention.

- **.dockerignore**:
  - Excludes unnecessary files (e.g., `node_modules`, logs) from image builds for efficiency.
