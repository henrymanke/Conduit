### Updated `deployment.md`

# Docker and Deploy Workflow

This document provides an overview of the automated deployment process using GitHub Actions to build, push, sign, and deploy Docker images to a server.

## Workflow Overview

The GitHub Actions workflow is triggered on:
- **Pushes** to the `main` branch.
- **Tag creations** following semantic versioning (e.g., `v1.0.0`).
- **Pull Requests** targeting the `main` branch (build only).

## Environment Variables

| Variable        | Description                                               |
|-----------------|-----------------------------------------------------------|
| `REGISTRY`      | Container registry to push images to (default: `ghcr.io`) |
| `IMAGE_NAME`    | Base name of the Docker images (e.g., `conduit`).          |
| `IMAGE_TAG`     | Default tag for Docker images (default: `latest`).         |

## Workflow Jobs

### 1. Build

The `build` job is responsible for creating Docker images for the frontend and backend, pushing them to the registry, and signing them.

#### Steps:
1. **Checkout Repository**: Fetches the codebase, including all submodules, in recursive mode.
2. **Install Cosign**: Installs `cosign` for signing Docker images. (Skipped for pull requests.)
3. **Set Up Docker Buildx**: Prepares Docker for multi-platform builds.
4. **Log Into Registry**: Authenticates with the container registry using GitHub’s token or a Personal Access Token (PAT).
5. **Extract Docker Metadata**: Generates metadata such as tags and labels for Docker images.
6. **Build and Push Docker Images**:
   - Builds separate Docker images for the frontend and backend.
   - Pushes the images to the registry unless the event is a pull request.
7. **Sign Docker Images**: Signs the Docker images to ensure their integrity and authenticity. (Skipped for pull requests.)

### 2. Deploy

The `deploy` job handles deploying the images to the server. It runs only after a successful build and is skipped for pull requests.

#### Steps:
1. **SSH to Server**:
   - Logs into the server using provided SSH credentials.
   - Pulls the latest Docker images for the frontend and backend with retry logic for reliability.
   - Updates and restarts the Docker containers using `docker compose`.

2. **(Optional) Image Verification**:
   - Verifies the integrity of Docker images using Cosign.
   - Ensures TUF cache initialization if verification is enabled.

## Required Secrets

The following secrets must be defined in the GitHub repository settings:

| Secret            | Description                                             |
|-------------------|---------------------------------------------------------|
| `SERVER_HOST`     | IP address or hostname of the target server             |
| `SERVER_USER`     | Username for SSH access                                 |
| `SSH_PRIVATE_KEY` | SSH private key for secure server access                |
| `SSH_PORT`        | SSH port for the server (default: `22`)                 |
| `DEPLOY_PATH`     | Path to the deployment directory on the server          |
| `GITHUB_TOKEN`    | Default GitHub Actions token for accessing the registry |
| `GHCR_PAT`        | Personal Access Token with `write:packages` permission  |

## Example Workflow

### Triggered on:
- Pushes to `main`.
- Tag creations (e.g., `v1.0.0`).

### Sequence:
1. Build and push Docker images for the frontend and backend.
2. Optionally sign images for security.
3. Deploy images to the server:
   - Pull latest Docker images from the registry.
   - Restart services using `docker compose`.

This automated workflow ensures consistent, secure, and efficient deployment, reducing manual overhead in CI/CD pipelines.

---

Let me know if additional enhancements or adjustments are needed!