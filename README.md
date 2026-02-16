# 🔍 Thanos - CleanStart Container

This project provides a CleanStart container image for **Thanos**, a highly available Prometheus setup with long-term storage capabilities. Thanos enables global querying, unlimited retention, and high availability for Prometheus metrics. The CleanStart Thanos image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `ghcr.io/cleanstart-containers/thanos`

**Registry:** CleanStart Registry

---

## Overview

Thanos is a highly available Prometheus setup with long-term storage capabilities. It operates as a set of components that work together to provide global querying, unlimited retention, and high availability for Prometheus metrics. This CleanStart Thanos container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Project Structure

The Thanos container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Thanos container in a Kubernetes environment

---

## Container Image

**Image:** `ghcr.io/cleanstart-containers/thanos:latest-dev`

The container image includes:

- Thanos binary located at `/usr/bin/thanos`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- **Interactive Testing**: Container stays alive for running Thanos commands via `kubectl exec`
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Health Checks**: Liveness and readiness probes using Thanos version command
- **Port Exposure**: Standard Thanos ports (10901 for gRPC, 10902 for HTTP) are exposed for future service deployment
- Global querying across multiple Prometheus instances
- Unlimited metric retention with object storage
- High availability for Prometheus metrics
- Multi-component architecture (query, store, sidecar, compact, etc.)

---

## Use Cases

Typical scenarios where this container excels:

- **Image Validation**: Testing the CleanStart Thanos container image
- **Development Testing**: Validating Thanos functionality in Kubernetes
- **Component Testing**: Testing individual Thanos components interactively
- **CI/CD Integration**: Automated testing of the container image
- Long-term metric storage and retention
- Multi-cluster Prometheus aggregation
- High availability metric querying
- Prometheus metrics backup and disaster recovery

---

## Quick Start

### Pull Commands
```bash
docker pull ghcr.io/cleanstart-containers/thanos:latest
docker pull ghcr.io/cleanstart-containers/thanos:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name thanos-test ghcr.io/cleanstart-containers/thanos:latest-dev
```

Production deployment:
```bash
docker run -d --name thanos-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  ghcr.io/cleanstart-containers/thanos:latest
```

---

## Kubernetes Deployment

The `kubernetes/` directory contains a complete deployment setup for testing the Thanos container functionality in Kubernetes clusters.

### Deployment Components

The Kubernetes deployment includes:

- **Namespace**: Isolated `thanos-test` namespace for testing
- **ServiceAccount**: Dedicated service account for the Thanos deployment
- **Deployment**: Single-replica deployment that keeps the container running for interactive testing
- **Service**: ClusterIP service exposing ports 10901 (gRPC) and 10902 (HTTP)

### Current Configuration

The deployment is configured for **testing and validation purposes**. The container runs a keep-alive script that allows interactive testing of Thanos commands. This setup is ideal for:

- Verifying the Thanos binary functionality
- Testing Thanos component commands (query, store, sidecar, etc.)
- Validating the container image in a Kubernetes environment
- CI/CD pipeline testing

For detailed deployment instructions, testing procedures refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 ghcr.io/cleanstart-containers/thanos:latest
docker pull --platform linux/arm64 ghcr.io/cleanstart-containers/thanos:latest
```

---

## Notes

The current Kubernetes deployment keeps the container running for interactive testing. For production use, you would typically configure the deployment to run a specific Thanos component (query, store, sidecar, compact, etc.) with appropriate configuration, storage backends, and service discovery.

Production considerations:
- Configure object storage backend (S3, GCS, Azure Blob, etc.)
- Set up Prometheus sidecar for metric shipping
- Deploy query component for global metric querying
- Configure compactor for metric downsampling
- Implement proper monitoring and alerting
- Use appropriate resource limits based on workload

---

## Resources

- **Official Documentation:** https://thanos.io/tip/thanos/getting-started.md/
- **Thanos GitHub Repository:** https://github.com/thanos-io/thanos
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/thanos
- **Docker Hub:** https://hub.docker.com/r/cleanstart/thanos
- **CleanStart All Images:** https://images.cleanstart.com/images/thanos/details
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart
  
---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.

