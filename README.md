# GitOps Kubernetes Manifests - Event Booking Application

This repository contains Kubernetes manifests for deploying an event booking application using GitOps principles with ArgoCD and Kustomize.

## Architecture Overview

The event booking application consists of three main components:
- **Frontend**: React-based user interface
- **Backend**: API server handling business logic
- **Database**: PostgreSQL for data persistence

## Repository Structure

```
.
├── README.md                           # This file
├── kustomization.yaml                  # Kustomize configuration
├── namespace.yaml                      # Kubernetes namespace definition
├── argocd-application.yaml            # ArgoCD application configuration
├── frontend-complete.yaml             # Frontend deployment and service
├── backend-complete.yaml              # Backend deployment and service
├── postgres-complete.yaml             # PostgreSQL database setup
└── external-secrets-placeholder.yaml  # External secrets configuration
```

## Deployment

### Prerequisites

- Kubernetes cluster (v1.20+)
- ArgoCD installed in the cluster
- kubectl configured to access your cluster

### GitOps Deployment with ArgoCD

1. **Apply the ArgoCD Application**:
   ```bash
   kubectl apply -f argocd-application.yaml
   ```

2. **Access ArgoCD UI** to monitor the deployment status

3. **Sync the Application** (if not using automated sync):
   ```bash
   argocd app sync event-booking-app-dev
   ```

### Manual Deployment with Kustomize

If you prefer manual deployment:

```bash
# Apply all manifests using Kustomize
kubectl apply -k .

# Or apply individual components
kubectl apply -f namespace.yaml
kubectl apply -f postgres-complete.yaml
kubectl apply -f backend-complete.yaml
kubectl apply -f frontend-complete.yaml
```

## Configuration

### Environment: Development (`event-booking-dev`)

| Component | Image | Tag | Replicas |
|-----------|-------|-----|----------|
| Frontend | `akpadetsi/event-booking-frontend` | `v2` | 2 |
| Backend | `akpadetsi/event-booking-backend` | `latest` | 2 |
| PostgreSQL | `postgres` | `15` | 1 |

### Kustomize Configuration

The `kustomization.yaml` file provides:
- **Namespace targeting**: All resources deployed to `event-booking-dev`
- **Image management**: Centralized image tags for easy updates
- **Replica scaling**: Configure replicas for each component
- **Common annotations**: Applied to all resources

### Updating Image Tags

To update application versions, modify the `images` section in `kustomization.yaml`:

```yaml
images:
  - name: akpadetsi/event-booking-frontend
    newTag: v3  # Update this
  - name: akpadetsi/event-booking-backend
    newTag: v1.2.0  # Update this
```

## Security & Secrets

- **External Secrets**: Configured via `external-secrets-placeholder.yaml`
- **Database Credentials**: Managed through Kubernetes secrets
- **TLS/HTTPS**: Configure ingress for production environments

## Monitoring & Observability

The deployment includes:
- Resource limits and requests for proper resource management
- Health checks (readiness and liveness probes)
- Service discovery through Kubernetes services

## Development Workflow

### Making Changes

1. **Update manifests** in this repository
2. **Commit changes** following [Conventional Commits](https://www.conventionalcommits.org/)
3. **Push to main branch**
4. **ArgoCD automatically syncs** the changes (if auto-sync is enabled)

### Local Testing

Test your changes locally before committing:

```bash
# Validate Kubernetes manifests
kubectl --dry-run=client apply -k .

# Check Kustomize output
kubectl kustomize .
```

## Access Points

After deployment, the application will be available through:

- **Frontend Service**: `http://frontend-service.event-booking-dev.svc.cluster.local`
- **Backend API**: `http://backend-service.event-booking-dev.svc.cluster.local`
- **Database**: `postgres-service.event-booking-dev.svc.cluster.local:5432`

### Port Forwarding (for local development)

```bash
# Access frontend locally
kubectl port-forward -n event-booking-dev service/frontend-service 3000:80

# Access backend API locally  
kubectl port-forward -n event-booking-dev service/backend-service 8000:80

# Access database locally
kubectl port-forward -n event-booking-dev service/postgres-service 5432:5432
```

## CI/CD Integration

This repository is designed to work with:
- **ArgoCD** for GitOps continuous delivery
- **GitHub Actions** (optional) for CI pipeline
- **Image scanning** and security checks
- **Automated testing** before deployment

## Troubleshooting

### Common Issues

1. **Pods not starting**: Check resource limits and image pull policies
2. **Database connection issues**: Verify service names and port configurations
3. **ArgoCD sync failures**: Check YAML syntax and Kubernetes API versions

### Debug Commands

```bash
# Check pod status
kubectl get pods -n event-booking-dev

# View pod logs
kubectl logs -n event-booking-dev deployment/frontend
kubectl logs -n event-booking-dev deployment/backend

# Describe resources for detailed info
kubectl describe deployment frontend -n event-booking-dev
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test locally using `kubectl --dry-run=client apply -k .`
5. Commit using conventional commit format
6. Push to your fork and create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
- Create an issue in this repository
- Contact the DevOps team
- Check ArgoCD UI for deployment status

---

**Note**: This is a development environment configuration. For production deployments, additional security hardening, monitoring, and backup strategies should be implemented.
