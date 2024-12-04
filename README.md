Simple project for deploying a small website with k8s cluster on aws using GitHub actions.


# Cluster automations steps:

# Install Kind Cluster on EC2

This workflow sets up a Kind cluster on an EC2 instance.

## Inputs
- **`EC2_IP`**: IP address of the target EC2 instance (required).

## Key Steps
1. **Checkout Code**: Fetches the repository.
2. **Install Docker**: Installs and configures Docker.
3. **Install Kind**: Downloads and sets up Kind.
4. **Install kubectl**: Installs the latest Kubernetes CLI tool.
5. **Create Cluster**: Sets up a Kind cluster with port mappings.
6. **Verify Setup**: Ensures Docker, Kind, and the cluster are working.

## Notes
- Requires SSH access (`SSH_KEY`).
- Suitable for development and testing Kubernetes setups.
