# 🔍 Thanos - CleanStart Container

## Overview

This project provides a CleanStart container image for **Thanos**, a highly available Prometheus setup with long-term storage capabilities. Thanos enables global querying, unlimited retention, and high availability for Prometheus metrics.

## Project Structure

The Thanos container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Thanos container in a Kubernetes environment

## Container Image

**Image:** `cleanstart/thanos:latest-dev`

The container image includes:
- Thanos binary located at `/usr/bin/thanos`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

## Kubernetes Deployment

The `kubernetes/` directory contains a complete deployment setup for testing the Thanos container functionality in Kubernetes clusters.

### Deployment Components

The Kubernetes deployment includes:

- **Namespace**: Isolated `thanos-test` namespace for testing
- **ServiceAccount**: Dedicated service account for the Thanos deployment
- **Deployment**: Single-replica deployment that keeps the container running for interactive testing
- **Service**: ClusterIP service exposing ports 10901 (gRPC) and 10902 (HTTP)

### Key Features

- **Interactive Testing**: Container stays alive for running Thanos commands via `kubectl exec`
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Health Checks**: Liveness and readiness probes using Thanos version command
- **Port Exposure**: Standard Thanos ports (10901 for gRPC, 10902 for HTTP) are exposed for future service deployment

### Current Configuration

The deployment is configured for **testing and validation purposes**. The container runs a keep-alive script that allows interactive testing of Thanos commands. This setup is ideal for:

- Verifying the Thanos binary functionality
- Testing Thanos component commands (query, store, sidecar, etc.)
- Validating the container image in a Kubernetes environment
- CI/CD pipeline testing

### Use Cases

This deployment is designed for:
- **Image Validation**: Testing the CleanStart Thanos container image
- **Development Testing**: Validating Thanos functionality in Kubernetes
- **Component Testing**: Testing individual Thanos components interactively
- **CI/CD Integration**: Automated testing of the container image

## Notes

The current Kubernetes deployment keeps the container running for interactive testing. For production use, you would typically configure the deployment to run a specific Thanos component (query, store, sidecar, compact, etc.) with appropriate configuration, storage backends, and service discovery.

## Related Documentation

For detailed deployment instructions, testing procedures refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

