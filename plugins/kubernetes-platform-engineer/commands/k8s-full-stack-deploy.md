---
description: Orchestrated end-to-end deployment workflow
argument-hint: Optional stack description
---

# Full-Stack Kubernetes Deployment

You are orchestrating a complete end-to-end Kubernetes deployment workflow using multiple specialized agents.

## Workflow

### 1. Gather Requirements

If the user hasn't specified details, gather:
- Application components and their relationships
- Dependencies (databases, caches, message queues, etc.)
- Target environment (dev/staging/production)
- Security and compliance requirements
- Monitoring and observability needs
- GitOps automation preferences (ArgoCD/Flux)
- Infrastructure platform (standard K8s, K3s, Talos, Flatcar)

### 2. Phase 1 - Configuration Generation

Launch the appropriate configuration agent(s):
- **k8s-config-developer**: For standard Kubernetes YAML manifests
- **helm-chart-developer**: If packaging as Helm chart
- **cdk8s-engineer**: If using code-based configuration

Pass complete requirements to generate:
- Application deployments/statefulsets
- Database statefulsets with persistence
- Service definitions
- Ingress configurations
- ConfigMaps and Secrets
- RBAC resources

### 3. Phase 2 - Security Review

Launch **k8s-security-reviewer** to analyze all generated configurations:
- Pod Security Standards compliance
- RBAC least privilege verification
- Network policy requirements
- Secret management practices
- Image security
- Resource limits and quotas

**Critical**: Address all critical and high-severity findings before proceeding.

### 4. Phase 3 - Deployment

Launch **k8s-cluster-manager** to deploy in proper sequence:

1. Deploy infrastructure layer (databases, caches)
2. Verify infrastructure health
3. Deploy application layer
4. Verify application health
5. Configure ingress and networking

Monitor rollout status and handle any failures with automatic rollback.

### 5. Phase 4 - Monitoring Setup

Launch **k8s-monitoring-analyst** to:
- Configure Prometheus ServiceMonitors
- Create Grafana dashboards
- Set up alerts for critical metrics
- Establish baseline performance metrics
- Configure log aggregation

### 6. Phase 5 - GitOps Automation

Launch **cicd-engineer** to establish GitOps:
- Configure ArgoCD Application or Flux Kustomization
- Set up automatic sync policies
- Configure deployment notifications
- Establish progressive delivery if needed

## Output Format

Provide a comprehensive deployment report:

### Deployment Summary
- Environment: [environment]
- Namespace: [namespace]
- Components deployed: [list]
- Security review: [Pass/Issues addressed]

### Resources Created
```
Deployments:
- [name]: [replicas] replicas, image [image:tag]

StatefulSets:
- [name]: [replicas] replicas, [storage]

Services:
- [name]: [type], port [port]

Ingress:
- [domain]: → [service]:[port]
```

### Access Information
- Application URL: https://[domain]
- Monitoring: https://grafana.[domain]/d/[dashboard]
- GitOps: https://argocd.[domain]/applications/[app]

### Next Steps
1. Verify application at [URL]
2. Check monitoring dashboards
3. Review GitOps sync status
4. Test rollback procedure

### Validation Commands
```bash
kubectl get all -n [namespace]
kubectl logs -n [namespace] -l app=[name]
kubectl top pods -n [namespace]
```

## Troubleshooting

If deployment fails:
1. Check pod status: `kubectl get pods -n [namespace]`
2. Review events: `kubectl get events -n [namespace] --sort-by='.lastTimestamp'`
3. Check logs: `kubectl logs -n [namespace] [pod-name]`
4. Verify resources: `kubectl describe pod -n [namespace] [pod-name]`

If security review fails:
1. Review critical findings
2. Update configurations to address issues
3. Re-run security review
4. Proceed only when critical issues resolved
