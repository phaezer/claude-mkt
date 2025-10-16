---
description: Deploy to Kubernetes cluster
argument-hint: Optional deployment details
---

# Kubernetes Deployment

You are deploying applications to a Kubernetes cluster using the k8s-cluster-manager agent.

## Workflow

### 1. Gather Deployment Information

If not specified, ask for:
- **What to deploy**:
  - Path to YAML manifests
  - Helm chart name/path
  - Kustomize directory
  - Docker image (for quick deployment)
- **Target cluster**:
  - Cluster context name
  - Namespace (create if doesn't exist)
  - Environment type (dev/staging/production)
- **Deployment strategy**:
  - RollingUpdate (default, zero downtime)
  - Recreate (stop old, start new)
  - Blue-Green (switch service selector)
  - Canary (gradual traffic shift)
- **Requirements**:
  - Resource requests/limits
  - Replica count
  - Health check configuration

### 2. Pre-Deployment Validation

Before deploying, verify:

**Cluster connectivity**:
```bash
kubectl cluster-info
kubectl get nodes
```

**Namespace exists or create**:
```bash
kubectl get namespace [namespace]
# If doesn't exist:
kubectl create namespace [namespace]
```

**Context verification**:
```bash
kubectl config current-context
# Switch if needed:
kubectl config use-context [cluster-name]
```

**Manifest validation** (for YAML files):
```bash
# Dry run to validate
kubectl apply -f [manifest.yaml] --dry-run=client

# Validate all files in directory
kubectl apply -f [directory]/ --dry-run=client

# Server-side validation
kubectl apply -f [manifest.yaml] --dry-run=server
```

### 3. Execute Deployment

Launch **k8s-cluster-manager** agent with deployment method:

#### Option A: Direct YAML Manifests

```bash
# Single file
kubectl apply -f deployment.yaml -n [namespace]

# Multiple files
kubectl apply -f deployment.yaml -f service.yaml -f ingress.yaml -n [namespace]

# Entire directory
kubectl apply -f k8s/ -n [namespace]

# Recursive directory
kubectl apply -f k8s/ -n [namespace] --recursive
```

#### Option B: Helm Chart

```bash
# Add repository (if needed)
helm repo add [repo-name] [repo-url]
helm repo update

# Install new release
helm install [release-name] [chart] -n [namespace] \
  --create-namespace \
  --set replicas=3 \
  --set image.tag=v1.2.3 \
  --values values.yaml

# Upgrade existing release
helm upgrade [release-name] [chart] -n [namespace] \
  --reuse-values \
  --set image.tag=v1.2.4

# Install or upgrade (idempotent)
helm upgrade --install [release-name] [chart] -n [namespace]
```

#### Option C: Kustomize

```bash
# Apply with kustomize
kubectl apply -k overlays/[environment]/ -n [namespace]

# Preview what will be applied
kubectl kustomize overlays/[environment]/
```

#### Option D: Quick Deployment (Image Only)

```bash
# Create deployment from image
kubectl create deployment [name] \
  --image=[image:tag] \
  --replicas=3 \
  -n [namespace]

# Expose as service
kubectl expose deployment [name] \
  --port=80 \
  --target-port=8080 \
  --type=LoadBalancer \
  -n [namespace]
```

### 4. Monitor Deployment Progress

**Watch rollout status**:
```bash
# For Deployments
kubectl rollout status deployment/[name] -n [namespace]

# For StatefulSets
kubectl rollout status statefulset/[name] -n [namespace]

# For DaemonSets
kubectl rollout status daemonset/[name] -n [namespace]
```

**Watch pods coming up**:
```bash
# Watch pods in real-time
kubectl get pods -n [namespace] -w

# Watch with labels
kubectl get pods -n [namespace] -l app=[name] -w

# Detailed view
kubectl get pods -n [namespace] -o wide
```

**Check events**:
```bash
kubectl get events -n [namespace] \
  --sort-by='.lastTimestamp' \
  --watch
```

### 5. Verify Deployment Health

**Pod status checks**:
```bash
# All pods running?
kubectl get pods -n [namespace]

# Check specific deployment
kubectl get deployment [name] -n [namespace]

# Detailed pod info
kubectl describe pod [pod-name] -n [namespace]
```

**Health check verification**:
```bash
# Check if pods are ready
kubectl get pods -n [namespace] -o json | \
  jq '.items[] | {name: .metadata.name, ready: .status.conditions[] | select(.type=="Ready") | .status}'

# Check readiness probes
kubectl describe pod [pod-name] -n [namespace] | grep -A5 "Readiness"
```

**Service connectivity**:
```bash
# Check service endpoints
kubectl get endpoints [service-name] -n [namespace]

# Describe service
kubectl describe service [service-name] -n [namespace]

# Test service from within cluster
kubectl run test-pod --image=curlimages/curl -i --rm -- \
  curl http://[service-name].[namespace].svc.cluster.local
```

**Resource usage**:
```bash
# Pod resource usage
kubectl top pods -n [namespace]

# Specific deployment
kubectl top pods -n [namespace] -l app=[name]
```

### 6. Post-Deployment Validation

**Application health checks**:
```bash
# Check application logs
kubectl logs -n [namespace] deployment/[name] --tail=50

# Follow logs
kubectl logs -n [namespace] -f deployment/[name]

# Logs from all pods
kubectl logs -n [namespace] -l app=[name] --all-containers=true
```

**Ingress/Route verification** (if applicable):
```bash
# Check ingress
kubectl get ingress -n [namespace]

# Test external access
curl https://[domain]
```

**ConfigMap/Secret verification**:
```bash
# Verify ConfigMaps mounted
kubectl get configmap -n [namespace]

# Verify Secrets exist
kubectl get secrets -n [namespace]
```

### 7. Update Deployment Records

Document deployment details:
- Deployment timestamp
- Image versions deployed
- Configuration changes
- Any issues encountered
- Rollback plan (previous version info)

## Deployment Strategies

### Rolling Update (Default)

**Configuration**:
```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Max pods above desired count
      maxUnavailable: 0   # Max pods below desired count
```

**Deploy**:
```bash
kubectl set image deployment/[name] \
  [container]=[image:new-tag] \
  -n [namespace]
```

### Recreate Strategy

**Configuration**:
```yaml
spec:
  strategy:
    type: Recreate
```

**Use case**: When you can afford downtime or need to avoid version mixing

### Blue-Green Deployment

**Steps**:
```bash
# 1. Deploy green version
kubectl apply -f deployment-green.yaml -n [namespace]

# 2. Verify green is healthy
kubectl get pods -n [namespace] -l version=green

# 3. Switch service selector
kubectl patch service [name] -n [namespace] \
  -p '{"spec":{"selector":{"version":"green"}}}'

# 4. Remove blue version
kubectl delete deployment [name]-blue -n [namespace]
```

### Canary Deployment

**Steps**:
```bash
# 1. Deploy canary with 1 replica
kubectl apply -f deployment-canary.yaml -n [namespace]

# 2. Monitor metrics (error rate, latency)
kubectl logs -n [namespace] -l version=canary

# 3. Gradually increase canary replicas
kubectl scale deployment [name]-canary --replicas=3 -n [namespace]

# 4. If successful, update main deployment
kubectl set image deployment/[name] [container]=[new-image] -n [namespace]

# 5. Remove canary
kubectl delete deployment [name]-canary -n [namespace]
```

## Output Format

### Deployment Summary

**Deployment Information**:
- **Name**: [deployment-name]
- **Namespace**: [namespace]
- **Environment**: [dev/staging/production]
- **Strategy**: [RollingUpdate/Recreate/Blue-Green/Canary]
- **Timestamp**: [YYYY-MM-DD HH:MM:SS UTC]

**Resources Deployed**:
```
Deployments:
  ✓ [name]: 3/3 replicas ready
    - Image: [image:tag]
    - CPU: 100m request, 500m limit
    - Memory: 128Mi request, 512Mi limit

Services:
  ✓ [name]: ClusterIP 10.96.1.10:80 → 8080
  ✓ [name]-lb: LoadBalancer [external-ip]:80 → 8080

Ingress:
  ✓ [name]: https://[domain] → [service]:80

ConfigMaps:
  ✓ [name]-config

Secrets:
  ✓ [name]-secrets
```

**Health Status**:
- Pods: 3/3 Running
- Ready: 3/3
- Restarts: 0
- Age: 2m30s

**Access Information**:
- Internal: http://[service].[namespace].svc.cluster.local:80
- External: https://[domain]
- Load Balancer: http://[external-ip]:80

### Verification Commands

Run these commands to verify deployment:
```bash
# Check deployment status
kubectl get deployment [name] -n [namespace]

# Check pod health
kubectl get pods -n [namespace] -l app=[name]

# View logs
kubectl logs -n [namespace] -l app=[name] --tail=20

# Test service
kubectl run test --image=curlimages/curl -i --rm -- \
  curl http://[service].[namespace].svc.cluster.local

# Check resource usage
kubectl top pods -n [namespace] -l app=[name]
```

### Rollback Information

If issues occur, rollback with:
```bash
# View rollout history
kubectl rollout history deployment/[name] -n [namespace]

# Rollback to previous version
kubectl rollout undo deployment/[name] -n [namespace]

# Rollback to specific revision
kubectl rollout undo deployment/[name] -n [namespace] --to-revision=[num]
```

**Previous Version**:
- Revision: [number]
- Image: [previous-image:tag]
- Change cause: [previous-deployment-reason]

## Troubleshooting

### Pods Not Starting

**ImagePullBackOff**:
```bash
# Check image pull errors
kubectl describe pod [pod-name] -n [namespace] | grep -A10 "Events:"

# Verify image exists
docker pull [image:tag]

# Check imagePullSecrets
kubectl get secrets -n [namespace]
```

**CrashLoopBackOff**:
```bash
# Check application logs
kubectl logs [pod-name] -n [namespace] --previous

# Check startup command
kubectl describe pod [pod-name] -n [namespace] | grep -A5 "Command:"

# Check resource limits
kubectl describe pod [pod-name] -n [namespace] | grep -A10 "Limits:"
```

**Pending Status**:
```bash
# Check why pod is pending
kubectl describe pod [pod-name] -n [namespace] | grep -A10 "Events:"

# Check node resources
kubectl top nodes

# Check PVC status (if using persistent volumes)
kubectl get pvc -n [namespace]
```

### Rollout Stuck

```bash
# Check rollout status
kubectl rollout status deployment/[name] -n [namespace]

# Check deployment events
kubectl describe deployment [name] -n [namespace]

# Check replica sets
kubectl get rs -n [namespace]

# Force rollout
kubectl rollout restart deployment/[name] -n [namespace]
```

### Service Not Accessible

```bash
# Check service selector matches pod labels
kubectl get service [name] -n [namespace] -o yaml | grep selector -A5
kubectl get pods -n [namespace] --show-labels

# Check endpoints
kubectl get endpoints [name] -n [namespace]

# Check network policies
kubectl get networkpolicies -n [namespace]

# Test from debug pod
kubectl run debug --image=nicolaka/netshoot -i --rm -- \
  curl http://[service].[namespace].svc.cluster.local
```

### High Resource Usage

```bash
# Check resource usage
kubectl top pods -n [namespace]

# Check for OOMKilled
kubectl get pods -n [namespace] -o json | \
  jq '.items[] | select(.status.containerStatuses[].lastState.terminated.reason=="OOMKilled") | .metadata.name'

# Increase resources
kubectl set resources deployment [name] -n [namespace] \
  --limits=cpu=1000m,memory=1Gi \
  --requests=cpu=200m,memory=256Mi
```

## Best Practices

**Pre-deployment**:
- Always use `--dry-run=client` first
- Test in dev/staging before production
- Review resource limits
- Verify image tags (avoid :latest in production)

**During deployment**:
- Monitor rollout status
- Watch logs for errors
- Check pod health continuously
- Verify endpoints are ready

**Post-deployment**:
- Document what was deployed
- Monitor for 10-15 minutes
- Keep previous version info for rollback
- Update monitoring dashboards

**Production deployments**:
- Use blue-green or canary for critical services
- Set PodDisruptionBudgets
- Configure HorizontalPodAutoscaler
- Enable auto-rollback on failure
- Schedule during maintenance windows
