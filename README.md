# Deploy a Website with Kubernetes on AWS

This project automates the deployment of a small website using a Kubernetes (Kind) cluster on AWS, managed through GitHub Actions.

---

## Cluster Automation Workflow

### Install Kind Cluster on EC2

This GitHub Actions workflow sets up a Kind cluster on an EC2 instance for testing and development purposes.

### Inputs
- **`EC2_IP`**: The IP address of the target EC2 instance (required).

### Key Steps
1. **Checkout Code**: Fetches the repository to the runner.
2. **Install Docker**: Installs and configures Docker on the EC2 instance.
3. **Install Kind**: Downloads and installs Kind for Kubernetes management.
4. **Install kubectl**: Installs Kubernetes CLI (`kubectl`) for managing the cluster.
5. **Create Cluster**: Sets up a Kind cluster with port mappings for testing.
6. **Verify Setup**: Confirms that Docker, Kind, and the cluster are working correctly.

### Notes
- Requires SSH access configured with `SSH_KEY`.
- Designed for development and lightweight Kubernetes setups.

---

## CI Workflow: Build and Push Docker Image

This CI workflow builds and pushes a Docker image to Docker Hub.

### Environment Variables
- **`IMAGE_NAME`**: Name of the Docker image to be built (default: `pyui`).
- **`IMAGE_TAG`**: Tag for the Docker image (default: `v1`).
- **`DOCKER_ACCOUNT`**: Docker Hub username or account (default: `gabecasis`).

### Key Steps
1. **Checkout Code**: Retrieves the project repository.
2. **Install Docker**: Installs Docker and ensures it is ready for building images.
3. **Login to Docker Hub**: Authenticates with Docker Hub using stored secrets.
4. **Build Docker Image**: Builds the Docker image from the `./app` directory.
5. **Push Docker Image**: Pushes the built image to Docker Hub.

---

## Sample CI Configuration

```yaml
name: CI
run-name: CI

env:
  IMAGE_NAME: pyui
  IMAGE_TAG: v1
  DOCKER_ACCOUNT: gabecasis

on:
  workflow_dispatch:

jobs:
  Build-and-Upload:
    runs-on: self-hosted

    steps:
      - name: Checkout Repo
        uses: actions/checkout@v3

      - name: Install Docker
        run: |
          sudo yum install -y docker
          sudo systemctl start docker
          sudo systemctl enable docker
          sudo usermod -aG docker $USER
          newgrp docker
          sudo chmod 666 /var/run/docker.sock

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build Docker Image
        run: docker build -t ${{ env.DOCKER_ACCOUNT }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_TAG }} ./app

      - name: Push Docker Image
        run: docker push ${{ env.DOCKER_ACCOUNT }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_TAG }}
