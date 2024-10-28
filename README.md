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
- [Updating Submodules](#updating-submodules)
- [Workflow](#workflow)

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

1. **Clone the repository with submodules:**
   ```bash
   git clone --recurse-submodules https://github.com/henrymanke/Conduit.git
   cd Conduit
   ```

2. **Initialize and update submodules if necessary:**
   ```bash
   git submodule update --init --recursive
   ```

3. **Set up environment variables:**
   - Create `.env` file from `sample.env`

    ```bash
    cp sample.env .env
    ```

4. **Build and run the services with development environment configuration:**
    - Build the images with the `NG_ENV` (default=production) variable set to development:
        ```bash
        docker compose build --build-arg NG_ENV=development
        ```
    - Start the services:
        ```bash
        docker compose up
        ```

> [!NOTE]  
> Setting NG_ENV to development or production will affect the URL that the frontend uses to access the backend. This URL can be configured in ./frontend/src/environment/environment.prod.ts for production builds, and other environment files as needed. Adjust the apiUrl in these files to point to the appropriate backend URL based on your deployment.


5. Access the services:
   - Frontend: [http://localhost:8282](http://localhost:8282)
   - Backend: [http://localhost:8000](http://localhost:8000)

## Usage

### Configuration

This project uses `docker-compose.yaml` to manage both frontend and backend services:

- **Frontend**:
  - Hosted as a submodule.
  - Exposes port `8287`.
  - Built using `node:20-alpine` with Nginx for production.

- **Backend**:
  - Hosted as a submodule.
  - Exposes port `8007`.
  - Built with Python 3.6, configured in a multi-stage Dockerfile for efficiency.

### Modifying Configuration

1. **Environment Variables**:
   - Update `.env` files in `backend` directories as needed.
   - update the frontend/src/environments/environment.prod.ts to your backend URL

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


Hier ist ein README-Abschnitt, der den Befehl `git submodule update --remote --merge` erklärt und beschreibt, wie er verwendet wird, um Submodule auf den neuesten Stand zu bringen:

---

### Updating Submodules

This project uses Git submodules for managing the frontend and backend repositories as separate components. To keep these submodules up to date with their latest versions, you can use the following command:

```bash
git submodule update --remote --merge
```

#### Explanation

- **Purpose**: This command updates each submodule to its latest commit on the tracked branch (usually `main` or `master`) and merges any new changes into the submodule's state within the main repository.
- **Usage**:
  1. Run the command from the root of the main repository to pull the latest commits from each submodule.
  2. After running the command, commit the changes in the main repository to save the updated submodule references.

#### Example Workflow

1. **Fetch latest submodule changes**:
   ```bash
   git submodule update --remote --merge
   ```

2. **Commit the submodule update in the main repository**:
   ```bash
   git add <submodule-path>
   git commit -m "Update submodules to latest commits"
   ```

3. **Push changes to the remote**:
   ```bash
   git push origin main
   ```

After following these steps, your main repository will include the latest versions of all submodules. This ensures that when others clone the repository, they receive the updated submodule content as well.

## Workflow

The automated deployment workflow builds, pushes, signs, and deploys Docker images to a server. It’s defined in `.github/workflows/deployment.md`.

Key Points:
- **Build Job**: Builds the Docker image and pushes it to the registry on updates to the `main` branch or version-tagged releases.
- **Deploy Job**: Connects to the server via SSH, pulls the latest image, and starts the services with Docker Compose.

For more details, refer to the [deployment.md](.github/workflows/deployment.md) file.