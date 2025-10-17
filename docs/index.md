# Platform Python App

A simple Python Flask application that displays the current time and hostname, designed for demonstration and platform engineering purposes.

## How to access the app

```
python-app-2-dev.test.com/api/v1/details
```

## Overview

This is a lightweight Flask-based REST API application that provides system details and health check endpoints. The application is containerized and ready to be deployed on Kubernetes with full CI/CD automation using GitHub Actions and ArgoCD.

## Features

- **REST API Endpoints**:
  - `/api/v1/details` - Returns current time, hostname, and a friendly message
  - `/api/v1/healthz` - Health check endpoint for readiness and liveness probes

- **Cloud-Native Architecture**:
  - Dockerized application using Alpine Linux for minimal footprint
  - Kubernetes-ready with manifests for deployment, service, and ingress
  - Helm chart for flexible deployment configurations

- **CI/CD Pipeline**:
  - Automated build and push to Docker Hub
  - GitOps workflow with ArgoCD integration
  - Self-hosted GitHub Actions runners

## Technology Stack

- **Runtime**: Python 3.10
- **Framework**: Flask 3.0.3
- **Containerization**: Docker (Alpine-based)
- **Orchestration**: Kubernetes
- **Package Management**: Helm
- **CI/CD**: GitHub Actions + ArgoCD
- **Service Catalog**: Backstage

## Project Structure

```
.
├── src/
│   └── app.py              # Flask application with API endpoints
├── k8s/
│   ├── deploy.yml          # Kubernetes deployment manifest
│   ├── service.yml         # Kubernetes service manifest
│   └── ingress.yml         # Kubernetes ingress configuration
├── charts/
│   ├── python-app-2/         # Helm chart for the application
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   └── argocd/             # ArgoCD application definitions
├── .github/
│   └── workflows/
│       └── cicd.yml        # CI/CD pipeline configuration
├── Dockerfile              # Container image definition
├── requirements.txt        # Python dependencies
├── catalog-info.yaml       # Backstage service catalog metadata
└── github_runner.yml       # Self-hosted runner deployment
```

## Getting Started

### Prerequisites

- Python 3.10+
- Docker (for containerization)
- Kubernetes cluster (for deployment)
- Helm 3+ (for Helm-based deployment)

### Local Development

1. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Run the application**:
   ```bash
   python src/app.py
   ```

3. **Test the endpoints**:
   ```bash
   curl http://localhost:5000/api/v1/details
   curl http://localhost:5000/api/v1/healthz
   ```

### Docker Build

Build the Docker image:
```bash
docker build -t python-app-2:latest .
```

Run the container:
```bash
docker run -p 5000:5000 python-app-2:latest
```

## Deployment

### Kubernetes (Direct Manifests)

Deploy using kubectl:
```bash
kubectl apply -f k8s/deploy.yml
kubectl apply -f k8s/service.yml
kubectl apply -f k8s/ingress.yml
```

### Helm Chart

Deploy using Helm:
```bash
helm install python-app-2 charts/python-app-2
```

Customize deployment with values:
```bash
helm install python-app-2 charts/python-app-2 \
  --set image.tag=v2 \
  --set ingress.hosts[0].host=python-app-2.example.com
```

### Configuration

Key Helm values (see `charts/python-app-2/values.yaml`):

- `replicaCount`: Number of pod replicas (default: 1)
- `image.repository`: Docker image repository
- `image.tag`: Image tag/version
- `service.type`: Kubernetes service type (default: ClusterIP)
- `service.port`: Service port (default: 5000)
- `ingress.enabled`: Enable ingress (default: true)
- `ingress.hosts`: Ingress hostnames
- `autoscaling.enabled`: Enable HPA (default: false)

## CI/CD Pipeline

The project includes a fully automated CI/CD pipeline that triggers on pushes to the `main` branch affecting the `src/` directory.

### CI Stage
1. Generates a short commit ID
2. Authenticates with Docker Hub
3. Builds and pushes the Docker image with the commit ID as tag

### CD Stage
1. Updates Helm `values.yaml` with the new image tag
2. Commits the changes back to the repository
3. Installs ArgoCD CLI
4. Triggers ArgoCD app sync for deployment

### Required Secrets & Variables

GitHub Secrets:
- `DOCKERHUB_TOKEN`: Docker Hub access token
- `ARGOCD_PASSWORD`: ArgoCD admin password

GitHub Variables:
- `DOCKERHUB_USERNAME`: Docker Hub username
- `ARGOCD_USERNAME`: ArgoCD username

## API Reference

### GET /api/v1/details

Returns current system details.

**Response:**
```json
{
  "time": "05:30:45PM on October 03, 2025",
  "hostname": "python-app-2-7d8c9f5b6-xyz12",
  "message": "You are doing great, little human!!"
}
```

### GET /api/v1/healthz

Health check endpoint for Kubernetes probes.

**Response:**
```json
{
  "status": "up"
}
```

**Status Code:** 200

## Backstage Integration

This application is registered in Backstage service catalog with the following metadata:

- **Name**: python-app-2
- **Type**: service
- **Lifecycle**: experimental
- **Owner**: development
- **GitHub**: jusg/python-app-2

## Infrastructure

### Kubernetes Resources

- **Deployment**: Runs 1 replica by default, uses image `javiergrande/python-app-2:v2`
- **Service**: ClusterIP type, exposes port 5000
- **Ingress**: NGINX ingress controller, accessible at `python-app-2.test.com`

### Self-Hosted Runners

The project uses self-hosted GitHub Actions runners for CD operations:
- Deployed as Kubernetes RunnerDeployment
- 1 replica configured
- Bound to repository: `jusg/platform-python-app-2`

## Monitoring & Health Checks

The application includes health check endpoints configured for Kubernetes:

- **Liveness Probe**: `/api/v1/healthz`
- **Readiness Probe**: `/api/v1/healthz`

Both probes use HTTP GET requests to ensure the application is healthy and ready to serve traffic.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test locally
5. Submit a pull request

Changes to `src/**` will automatically trigger the CI/CD pipeline on merge to `main`.

## License

This is an experimental project for platform engineering demonstrations.

## Contact

- **Owner**: Development Team
- **Repository**: [jusg/platform-python-app-2](https://github.com/jusg/python-app-2)