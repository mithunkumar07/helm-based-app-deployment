# Application Helm Chart

A Helm chart for deploying containerized applications with production-ready configurations including health checks, resource limits, and rolling updates.

## Overview

This Helm chart deploys any containerized application as a scalable service with the following features:
- Configurable replica count
- Health probes (startup, readiness, liveness)
- Resource requests and limits
- Rolling update strategy
- ConfigMap for environment variables
- Docker registry authentication support

## Prerequisites

- Kubernetes cluster
- Helm 3.x installed
- Docker pull secret configured in the cluster (if using private registry)

## Installation

### Install with default values:
```bash
helm install app-release .
```

### Install with custom values:
```bash
helm install app-release . -f my-values.yaml
```

### Install into specific namespace:
```bash
helm install app-release . --namespace <namespace> --create-namespace
```

## Configuration

### Default Values (`values.yaml`)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `namespace` | Target namespace | `default` |
| `replicaCount` | Number of replicas | `1` |
| `app.name` | Application name | `app` |
| `app.healthCheckPath` | Health check path | `/` |
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `80` |
| `image.repository` | Docker image | `app` |
| `image.tag` | Image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `Always` |
| `image.pullSecret` | Docker registry secret | `docker-creds` |
| `configmap.name` | ConfigMap name | `app-configmap` |
| `resources.requests.cpu` | CPU request | `500m` |
| `resources.requests.memory` | Memory request | `1024Mi` |
| `resources.limits.cpu` | CPU limit | `1000m` |
| `resources.limits.memory` | Memory limit | `2048Mi` |

### Health Probe Configuration

The chart includes three types of health probes:

- **Startup Probe**: Ensures the container has started before routing traffic
- **Readiness Probe**: Determines if the container is ready to serve traffic
- **Liveness Probe**: Ensures the container is still running properly

### Rolling Update Strategy

- `maxUnavailable: 0` - Ensures no pods are unavailable during updates
- `maxSurge: 1` - Allows one additional pod during updates

## Files Structure

```
.
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default configuration values
├── my-values.yaml      # Custom configuration example
└── templates/
    ├── configmap.yaml  # ConfigMap for environment variables
    ├── deployment.yaml # Kubernetes Deployment
    └── service.yaml    # Kubernetes Service
```

## Usage Examples

### Scale to 3 replicas:
```bash
helm upgrade app-release . --set replicaCount=3
```

### Use specific application version:
```bash
helm upgrade app-release . --set image.tag=v1.0.0
```

### Change service type to NodePort:
```bash
helm upgrade nginx-release . --set service.type=NodePort
```

## Upgrading

To upgrade the deployment:
```bash
helm upgrade app-release .
```

## Uninstalling

To remove the deployment:
```bash
helm uninstall app-release
```

## Monitoring

The deployment includes health checks that can be monitored through:
- `kubectl get pods` - Check pod status
- `kubectl describe pod <pod-name>` - View health probe status
- `kubectl logs <pod-name>` - View application logs

## Customization

You can customize the deployment by:
1. Modifying `values.yaml` for permanent changes
2. Using `--set` flags for one-time modifications
3. Creating separate values files for different environments (dev, staging, prod)

## Notes

- Ensure the Docker pull secret `docker-creds` exists in the target namespace
- Adjust resource limits based on your cluster capacity
- Consider using an Ingress controller for external access
- The ConfigMap can be extended with additional environment variables as needed