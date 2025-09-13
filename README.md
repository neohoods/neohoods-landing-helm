# Neohoods Landing Page Helm Chart

This Helm chart deploys the Neohoods Landing Page application on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- cert-manager (for TLS certificates)
- NGINX Ingress Controller

## Installing the Chart

To install the chart with the release name `neohoods-landing`:

```bash
helm repo add neohoods-landing https://neohoods.github.io/neohoods-landing-helm
helm repo update
helm install neohoods-landing neohoods-landing/neohoods-landing
```

## Uninstalling the Chart

To uninstall/delete the `neohoods-landing` deployment:

```bash
helm delete neohoods-landing
```

## Configuration

The following table lists the configurable parameters and their default values.

| Parameter                   | Description          | Default                             |
| --------------------------- | -------------------- | ----------------------------------- |
| `replicaCount`              | Number of replicas   | `2`                                 |
| `image.repository`          | Image repository     | `ghcr.io/neohoods/neohoods-landing` |
| `image.tag`                 | Image tag            | `latest`                            |
| `image.pullPolicy`          | Image pull policy    | `IfNotPresent`                      |
| `service.type`              | Service type         | `ClusterIP`                         |
| `service.port`              | Service port         | `80`                                |
| `ingress.enabled`           | Enable ingress       | `true`                              |
| `ingress.hosts[0].host`     | Ingress hostname     | `neohoods.com`                      |
| `ingress.tls[0].secretName` | TLS secret name      | `neohoods-landing-tls`              |
| `resources.limits.cpu`      | CPU limit            | `500m`                              |
| `resources.limits.memory`   | Memory limit         | `512Mi`                             |
| `autoscaling.enabled`       | Enable HPA           | `false`                             |
| `healthCheck.enabled`       | Enable health checks | `true`                              |
| `healthCheck.path`          | Health check path    | `/health`                           |

## Environment Variables

| Variable   | Description      | Default                    |
| ---------- | ---------------- | -------------------------- |
| `NODE_ENV` | Node environment | `production`               |
| `API_URL`  | API base URL     | `https://api.neohoods.com` |

## Examples

### Development Environment

```yaml
replicaCount: 1
image:
  tag: "dev"
ingress:
  hosts:
    - host: dev.neohoods.com
      paths:
        - path: /
          pathType: Prefix
env:
  API_URL: "https://dev.api.neohoods.com"
```

### Production Environment

```yaml
replicaCount: 3
autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 20
resources:
  limits:
    cpu: 1000m
    memory: 1Gi
  requests:
    cpu: 200m
    memory: 256Mi
```

## Health Checks

The application includes health check endpoints:

- **Liveness Probe**: `/health`
- **Readiness Probe**: `/health`

## Security

The chart includes several security features:

- Non-root container execution
- Read-only root filesystem
- Security headers via NGINX
- TLS termination
- Resource limits

## Monitoring

To enable monitoring, set:

```yaml
monitoring:
  enabled: true
  serviceMonitor:
    enabled: true
```

## Troubleshooting

### Check Pod Status

```bash
kubectl get pods -l app.kubernetes.io/name=neohoods-landing
```

### View Logs

```bash
kubectl logs -l app.kubernetes.io/name=neohoods-landing
```

### Test Health Check

```bash
kubectl port-forward svc/neohoods-landing 8080:80
curl http://localhost:8080/health
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the chart: `helm lint . && helm template . | kubectl apply --dry-run=client -f -`
5. Submit a pull request

## License

© 2025 Neohoods - All rights reserved
