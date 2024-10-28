# Docker and Deploy Workflow

This document describes the automated deployment process using GitHub Actions to build, push, sign, and deploy Docker images to a server.

## Workflow Overview

This workflow is triggered on:
- **Pushes** to the `main` branch
- **Tags** following semantic versioning (e.g., `v1.0.0`)
- **Pull Requests** targeting the `main` branch

### Environment Variables

| Variable        | Description                                             |
|-----------------|---------------------------------------------------------|
| `REGISTRY`      | Container registry to push images to (default: `ghcr.io`) |
| `IMAGE_NAME`    | Name of the image, derived from the GitHub repository    |

## Workflow Jobs

### 1. Build

The `build` job builds and optionally pushes the Docker image to the specified registry.

#### Steps:
1. **Checkout repository:** Checks out the code and submodules in recursive mode.
2. **Install cosign:** Installs cosign for image signing (skipped for pull requests).
3. **Set up Docker Buildx:** Prepares the Docker environment for multi-platform builds.
4. **Log into Registry:** Authenticates with the container registry using GitHub's token.
5. **Extract Docker Metadata:** Generates metadata (tags, labels) for the Docker image.
6. **Build and Push Docker Image:** Builds the Docker image and pushes it if it’s not a pull request.
7. **Sign the Image:** Signs the Docker image (skipped for pull requests).

### 2. Deploy

The `deploy` job is responsible for deploying the image to the server and is only triggered if the `build` job completes successfully.

#### Steps:
1. **SSH and Deploy:** Connects to the server over SSH and:
   - Sets the image tag for Docker Compose.
   - Navigates to the project directory on the server.
   - Pulls the latest image and starts it in detached mode using Docker Compose.

## Secrets Required

This workflow requires several secrets to be defined in the GitHub repository:
- **`SERVER_HOST`**: IP or hostname of the target server.
- **`SERVER_USER`**: Username for SSH access.
- **`SSH_PRIVATE_KEY`**: SSH private key for secure server access.
- **`SSH_PORT`**: SSH port for the server (default is typically 22).
- **`GITHUB_TOKEN`**: Default GitHub Actions token for authentication.
- **`DEPLOY_PATH`**: Path to the deployment directory on the server.

### Example Usage

On push to `main` or tag creation, GitHub Actions will:
1. Build and push the Docker image to the registry.
2. Sign the image using cosign.
3. Deploy the image on the server by pulling the updated image and restarting the container in detached mode.

This workflow helps automate the deployment of Docker-based applications using GitHub Actions, ensuring consistency and security throughout the deployment pipeline.