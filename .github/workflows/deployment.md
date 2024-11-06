# Docker and Deploy Workflow

This document provides an overview of the automated deployment process using GitHub Actions to build, push, sign, and deploy Docker images to a server.

## Workflow Overview

This GitHub Actions workflow is triggered on:
- **Pushes** to the `main` branch
- **Tag** creations following semantic versioning (e.g., `v1.0.0`)
- **Pull Requests** targeting the `main` branch

## Environment Variables

| Variable        | Description                                               |
|-----------------|-----------------------------------------------------------|
| `REGISTRY`      | Container registry to push images to (default: `ghcr.io`) |
| `IMAGE_NAME`    | Name of the Docker image, derived from the GitHub repository |

## Workflow Jobs

### 1. Build

The `build` job is responsible for creating the Docker images and, if applicable, pushing them to the registry.

#### Steps:
1. **Checkout repository**: Fetches the code and submodules in recursive mode.
2. **Install cosign**: Installs cosign for Docker image signing (skipped for pull requests).
3. **Set up Docker Buildx**: Prepares Docker for multi-platform builds.
4. **Log into Registry**: Authenticates with the container registry using GitHub’s token or Personal Access Token (PAT).
5. **Extract Docker Metadata**: Generates metadata (tags, labels) to be applied to the Docker images.
6. **Build and Push Docker Images**: Builds Docker images for both the frontend and backend, then pushes them to the registry if the event is not a pull request.
7. **Sign the Images**: Signs the Docker images to verify integrity (skipped for pull requests).

### 2. Deploy

The `deploy` job handles deploying the images to the server. This job only runs after a successful build and is not triggered for pull requests.

#### Steps:
1. **SSH and Deploy**: Connects to the server over SSH and:
   - Sets the image tag dynamically for Docker Compose.
   - Navigates to the project directory on the server.
   - Pulls the latest image from the registry.
   - Starts or restarts the Docker container in detached mode using Docker Compose.

## Required Secrets

The workflow requires the following secrets to be defined in the GitHub repository settings:

| Secret            | Description                                     |
|-------------------|-------------------------------------------------|
| `SERVER_HOST`     | IP address or hostname of the target server     |
| `SERVER_USER`     | Username for SSH access                         |
| `SSH_PRIVATE_KEY` | SSH private key for secure server access        |
| `SSH_PORT`        | SSH port for the server (typically 22)          |
| `GITHUB_TOKEN`    | Default GitHub Actions token for authentication |
| `DEPLOY_PATH`     | Path to the deployment directory on the server  |

> **Note**: Ensure the GitHub token or PAT has the necessary `write:packages` permission to push images to GitHub Container Registry.

## Example Workflow

On a push to `main` or when a tag is created, the workflow will:
1. Build the Docker images for the frontend and backend.
2. Push the images to the registry.
3. Sign the images using cosign for added security.
4. Deploy the images to the server by pulling the latest image and restarting the containers in detached mode.

This automated workflow enables seamless, consistent, and secure deployment of Docker-based applications using GitHub Actions, simplifying the CI/CD pipeline. 

