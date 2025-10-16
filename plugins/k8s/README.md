# Kubernetes Platform Engineer Plugin

A comprehensive Claude Code plugin for Kubernetes platform engineering covering cluster management, configuration development, monitoring, security, networking, and CI/CD with support for standard K8s, K3s, Talos Linux, Flatcar Linux, and GitOps.

## Overview

The Kubernetes Platform Engineer plugin provides specialized agents and workflows for:
- Kubernetes manifest and Helm chart development
- Cluster management with kubectl (deployments, rollbacks)
- Monitoring analysis and optimization recommendations
- Security reviews and hardening
- CNI configuration (Cilium, Calico)
- Talos and Flatcar Linux expertise
- GitOps CI/CD (ArgoCD, Flux)
- CDK8s (code-based config development)
- Orchestrated end-to-end workflows

## Agents

### k8s-orchestrator
Main orchestrator coordinating complex Kubernetes platform engineering tasks across all specialist agents. Use for multi-step workflows requiring coordination.

**Workflows:**
- Full-stack application deployment
- New cluster setup
- Monitoring and optimization
- End-to-end CI/CD setup

### k8s-config-developer
Develops Kubernetes manifests for standard K8s and K3s distributions.

**Creates:**
- Deployments, StatefulSets, DaemonSets
- Services and Ingress
- ConfigMaps and Secrets
- RBAC resources
- NetworkPolicies
- K3s-optimized configurations

### helm-chart-developer
Develops and maintains Helm charts for Kubernetes applications.

**Creates:**
- Complete Helm chart structure
- Flexible values.yaml
- Template helpers
- Chart dependencies
- Hooks and tests
- Comprehensive documentation

### k8s-cluster-manager
Manages Kubernetes clusters using kubectl and standard tooling.

**Operations:**
- Deploy applications
- Execute rollbacks
- Scale workloads
- Troubleshoot issues
- Update configurations
- Verify deployments

### k8s-monitoring-analyst
Analyzes monitoring data from Prometheus, Grafana, and kubectl to provide optimization recommendations.

**Analyzes:**
- Resource usage (CPU, memory)
- Pod health and restarts
- Network and disk I/O
- Application performance
- Cost optimization opportunities

### k8s-security-reviewer
Reviews Kubernetes configurations and architectures for security vulnerabilities.

**Reviews:**
- Pod Security Standards
- RBAC configurations
- Network policies
- Secret management
- Image security
- Compliance (CIS Benchmarks)

### k8s-network-engineer
Engineers cluster networking with CNI plugins including Cilium and Calico.

**Configures:**
- CNI installation (Cilium, Calico)
- Network policies
- Service mesh integration
- Load balancing
- DNS configuration
- Troubleshooting connectivity

### talos-linux-expert
Specialist for Talos Linux-based Kubernetes clusters.

**Expertise:**
- Talos configuration (machine configs)
- talosctl operations
- Cluster bootstrapping
- OS upgrades
- High availability setup
- Security hardening

### flatcar-linux-expert
Specialist for Flatcar Container Linux-based Kubernetes clusters.

**Expertise:**
- Ignition configuration
- kubeadm setup
- systemd services
- Update strategies
- Container runtime configuration
- System maintenance

### k8s-cicd-engineer
CI/CD specialist for GitOps with ArgoCD, Flux, and container workflows.

**Implements:**
- ArgoCD applications
- Flux controllers
- GitOps workflows
- Progressive delivery (Flagger)
- CI/CD pipelines
- Automated deployments

### cdk8s-engineer
Develops Kubernetes configurations using CDK8s (TypeScript, Python, Java, Go).

**Creates:**
- Type-safe configurations
- Reusable constructs
- CDK8s+ high-level abstractions
- Tested infrastructure code
- CI/CD integration

## Slash Commands

### /k8s-develop-config
Develop Kubernetes manifests for standard K8s or K3s.

### /k8s-create-helm-chart
Create Helm chart for application packaging.

### /k8s-deploy
Deploy applications to Kubernetes cluster.

### /k8s-rollback
Rollback Kubernetes deployment to previous version.

### /k8s-analyze-monitoring
Analyze monitoring data and provide optimization recommendations.

### /k8s-security-review
Security review of Kubernetes configurations and cluster.

### /k8s-setup-networking
Configure Kubernetes networking with CNI (Cilium or Calico).

### /k8s-setup-talos
Configure Talos Linux-based Kubernetes cluster.

### /k8s-setup-flatcar
Configure Flatcar Container Linux-based cluster.

### /k8s-setup-gitops
Setup GitOps CI/CD with ArgoCD or Flux.

### /k8s-cdk8s-develop
Develop Kubernetes configs using CDK8s (code-based).

### /k8s-full-stack-deploy
Orchestrated end-to-end deployment workflow coordinating multiple agents.

## Supported Technologies

### Kubernetes Distributions
- Standard Kubernetes (1.24+)
- K3s (lightweight Kubernetes)
- K8s on Talos Linux
- K8s on Flatcar Container Linux

### CNI Plugins
- Cilium (eBPF-based)
- Calico (network policy)
- Flannel (K3s default)

### GitOps Tools
- ArgoCD
- Flux
- Flagger (progressive delivery)

### Development Tools
- kubectl
- Helm
- CDK8s
- talosctl
- Ignition

### Monitoring
- Prometheus
- Grafana
- kubectl top
- Hubble (Cilium observability)

## Example Workflows

### Deploy Microservices Application
```
/k8s-full-stack-deploy

Orchestrator coordinates:
1. k8s-config-developer: Generate all manifests
2. k8s-security-reviewer: Security review
3. k8s-cluster-manager: Deploy to cluster
4. k8s-monitoring-analyst: Monitor deployment
5. k8s-cicd-engineer: Setup GitOps

Result: Production-ready deployment with monitoring and automation
```

### Setup Production Cluster on Talos
```
/k8s-setup-talos

talos-linux-expert generates Talos config
k8s-network-engineer configures Cilium CNI
k8s-security-reviewer hardens security
k8s-cluster-manager validates operations
k8s-monitoring-analyst sets up monitoring
cicd-engineer configures ArgoCD

Result: Secure, monitored, production-ready cluster
```

### Optimize Existing Deployment
```
/k8s-analyze-monitoring

k8s-monitoring-analyst:
- Analyzes metrics from Prometheus/Grafana
- Identifies resource over/under-provisioning
- Detects performance bottlenecks
- Provides cost optimization recommendations

k8s-config-developer: Generates optimized configs
k8s-cluster-manager: Applies optimizations
k8s-monitoring-analyst: Validates improvements

Result: Optimized deployment with cost savings
```

### Create and Deploy Helm Chart
```
/k8s-create-helm-chart

helm-chart-developer: Creates chart structure
k8s-security-reviewer: Reviews chart security
k8s-cluster-manager: Tests deployment
cicd-engineer: Configures automated releases

Result: Production Helm chart with CI/CD
```

## Best Practices

### Configuration
1. Use declarative configurations
2. Version control all manifests
3. Separate config from secrets
4. Use namespaces for isolation
5. Label resources consistently
6. Set resource requests and limits
7. Configure health checks

### Security
1. Enforce Pod Security Standards
2. Use least-privilege RBAC
3. Implement network policies
4. Scan images for vulnerabilities
5. Encrypt secrets (Sealed Secrets, External Secrets)
6. Regular security audits
7. Enable audit logging

### Operations
1. GitOps for all changes
2. Test in non-production first
3. Have rollback plans
4. Monitor everything
5. Automate deployments
6. Document runbooks
7. Regular backups (etcd)

### High Availability
1. Multi-replica deployments
2. Pod disruption budgets
3. Anti-affinity rules
4. Multiple availability zones
5. Load balancer health checks
6. Automatic failover
7. Regular disaster recovery testing

## Tools and Commands

### kubectl Essentials
```bash
# Get resources
kubectl get pods -n namespace
kubectl get all -n namespace

# Describe resources
kubectl describe pod pod-name -n namespace

# Logs
kubectl logs -f deployment/myapp -n namespace

# Execute commands
kubectl exec -it pod-name -n namespace -- /bin/bash

# Apply configs
kubectl apply -f manifests/

# Rollout management
kubectl rollout status deployment/myapp -n namespace
kubectl rollout undo deployment/myapp -n namespace

# Resource usage
kubectl top pods -n namespace
kubectl top nodes
```

### Helm Commands
```bash
# Install chart
helm install myapp ./mychart -n namespace

# Upgrade release
helm upgrade myapp ./mychart -n namespace

# Rollback
helm rollback myapp 1 -n namespace

# List releases
helm list -n namespace
```

### Talos Commands
```bash
# Apply config
talosctl apply-config --nodes IP --file config.yaml

# Bootstrap cluster
talosctl bootstrap --nodes IP

# Get kubeconfig
talosctl kubeconfig --nodes IP

# Upgrade
talosctl upgrade --nodes IP --image ghcr.io/siderolabs/installer:v1.5.0
```

### ArgoCD Commands
```bash
# Sync application
argocd app sync myapp

# Get application status
argocd app get myapp

# List applications
argocd app list
```

## Integration Points

All agents can work together through the k8s-orchestrator:

**Example: Full Platform Setup**
1. talos-linux-expert → OS configuration
2. k8s-network-engineer → CNI setup
3. k8s-security-reviewer → Security hardening
4. k8s-config-developer → Application manifests
5. k8s-cluster-manager → Deployment
6. k8s-monitoring-analyst → Monitoring setup
7. k8s-cicd-engineer → GitOps automation

**Example: Application Lifecycle**
1. cdk8s-engineer → Generate configs (TypeScript)
2. k8s-security-reviewer → Security review
3. helm-chart-developer → Package as Helm chart
4. k8s-cicd-engineer → Configure ArgoCD
5. k8s-cluster-manager → Deploy to cluster
6. k8s-monitoring-analyst → Monitor and optimize

## Supported Versions

- Kubernetes: 1.24+
- K3s: 1.24+
- Helm: 3.x
- ArgoCD: 2.x
- Flux: 2.x
- Talos Linux: 1.5+
- Flatcar Container Linux: 3000+
- CDK8s: 2.x

## License

[Specify your license]

## Version

Version 1.0.0
