---
description: Setup GitOps CI/CD with ArgoCD or Flux
argument-hint: Optional GitOps tool preference
---

# GitOps CI/CD Setup

You are setting up GitOps-based continuous deployment using the k8s-cicd-engineer agent.

## Workflow

### 1. Choose GitOps Tool

If not specified, help user choose:

**ArgoCD** - Best for:
- UI-driven workflows
- Multi-cluster management
- RBAC and SSO integration
- Helm and Kustomize support

**Flux** - Best for:
- Pure GitOps (no UI needed)
- Kubernetes-native resources
- Helm controller integration
- Multi-tenancy

### 2. Gather Requirements

Ask for:
- **Git repository**:
  - Repository URL
  - Branch strategy (main, env branches, or directories)
  - Authentication method (SSH key, token)
- **Applications**:
  - List of applications to manage
  - Manifest locations in repo
  - Dependencies between apps
- **Environments**:
  - dev, staging, production
  - Separate clusters or namespaces
- **Sync policy**:
  - Automatic or manual sync
  - Auto-pruning resources
  - Self-healing enabled
- **Progressive delivery**:
  - Canary deployments
  - Blue-green deployments
  - Flagger integration

### 3. Install GitOps Tool

Launch **k8s-cicd-engineer** to install:

**For ArgoCD**:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**For Flux**:
```bash
flux bootstrap github \
  --owner=[org] \
  --repository=[repo] \
  --branch=main \
  --path=clusters/production \
  --personal
```

### 4. Configure Git Repository Access

**ArgoCD**:
```bash
argocd repo add https://github.com/org/repo \
  --username [user] \
  --password [token]
```

**Flux**:
- Flux bootstrap automatically creates deploy key
- Verify in GitHub Settings > Deploy keys

### 5. Create Application Definitions

**ArgoCD Application**:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/repo
    targetRevision: HEAD
    path: k8s/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

**Flux Kustomization**:
```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: myapp
  namespace: flux-system
spec:
  interval: 5m
  path: ./k8s/overlays/production
  prune: true
  sourceRef:
    kind: GitRepository
    name: myapp
```

### 6. Setup App-of-Apps Pattern (Optional)

For managing multiple applications:

**ArgoCD**:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: apps
  namespace: argocd
spec:
  source:
    path: argocd/applications
  destination:
    namespace: argocd
  syncPolicy:
    automated: {}
```

**Flux**: Use hierarchical Kustomizations

### 7. Configure Progressive Delivery (Optional)

If requested, install and configure Flagger:

```bash
helm install flagger flagger/flagger \
  --namespace flagger-system
```

Create Canary resource:
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
```

### 8. Setup Notifications

**ArgoCD**:
- Configure Slack/Teams webhooks
- Setup notification triggers

**Flux**:
- Configure notification-controller
- Create Alerts for Git events

### 9. Verify GitOps Workflow

1. Make change in Git repository
2. Commit and push
3. Verify automatic sync
4. Check application health

## Output Format

### GitOps Setup Summary

**GitOps Tool**: [ArgoCD/Flux]
**Version**: [version]
**Installation**: [namespace]

**Git Repository**:
- URL: [repo-url]
- Branch: [branch]
- Path: [path]
- Authentication: [Configured ✓]

**Applications Configured**:
1. [app-name]
   - Source: [path]
   - Destination: [namespace]
   - Sync: [Auto/Manual]
   - Status: [Synced/OutOfSync]

2. [app-name]
   - Source: [path]
   - Destination: [namespace]
   - Sync: [Auto/Manual]
   - Status: [Synced/OutOfSync]

**Access Information**:
- **ArgoCD UI**: https://argocd.[domain]
  - Username: admin
  - Password: [Use `kubectl get secret` to retrieve]
- **Flux**: `flux get all`

### Next Steps

**For ArgoCD**:
```bash
# Access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d

# Sync application
argocd app sync myapp

# Check status
argocd app list
```

**For Flux**:
```bash
# Check GitOps status
flux get all

# Reconcile immediately
flux reconcile source git myapp
flux reconcile kustomization myapp

# Check logs
flux logs
```

### Testing GitOps Workflow

1. **Make a change**:
```bash
git clone [repo]
cd [repo]
# Edit manifests
git add .
git commit -m "Update deployment replicas"
git push
```

2. **Watch sync** (ArgoCD):
```bash
argocd app wait myapp --sync
```

2. **Watch sync** (Flux):
```bash
flux reconcile kustomization myapp --with-source
watch flux get kustomizations
```

3. **Verify changes**:
```bash
kubectl get deployment myapp -n production
```

## Best Practices

**Repository Structure**:
```
repo/
├── base/              # Base manifests
│   ├── deployment.yaml
│   └── service.yaml
├── overlays/
│   ├── dev/          # Dev environment
│   ├── staging/      # Staging environment
│   └── production/   # Production environment
└── argocd/           # Application definitions
    └── applications/
```

**Security**:
- Use SSH keys for Git access
- Enable RBAC in ArgoCD
- Encrypt secrets (Sealed Secrets, External Secrets)
- Review before auto-sync in production

**Workflow**:
- Use pull requests for changes
- Require code review
- Test in dev/staging first
- Enable auto-sync only after testing

## Troubleshooting

**Application not syncing (ArgoCD)**:
```bash
# Check application status
argocd app get myapp

# Force sync
argocd app sync myapp --force

# Check events
kubectl get events -n argocd
```

**Kustomization failing (Flux)**:
```bash
# Check status
flux get kustomizations

# Check logs
flux logs --kind=Kustomization --name=myapp

# Force reconcile
flux reconcile kustomization myapp --with-source
```

**Git authentication failing**:
- Verify deploy key permissions (read/write)
- Check token hasn't expired
- Verify repository URL correct
- Check network policies allow Git access
