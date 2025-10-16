---
description: Configure Talos Linux-based cluster
argument-hint: Optional cluster requirements
---

# Talos Linux Cluster Setup

You are setting up a Kubernetes cluster on Talos Linux using the talos-linux-expert agent.

## Workflow

### 1. Gather Cluster Requirements

If not specified, ask for:
- **Node configuration**:
  - Number of control plane nodes (1 or 3+ for HA)
  - Number of worker nodes
  - IP addresses for each node
  - Hostnames
- **Network configuration**:
  - Control plane endpoint (load balancer IP for HA)
  - CNI preference (none/Cilium/Calico - recommend installing separately)
  - Pod and service CIDR ranges
- **High availability**:
  - Load balancer for control plane (required for HA)
  - Distributed storage requirements
- **Talos version**: Latest stable or specific version

### 2. Generate Machine Configurations

Launch **talos-linux-expert** to generate configs:

```bash
talosctl gen config cluster-name https://[endpoint]:6443
```

This creates:
- `controlplane.yaml` - For control plane nodes
- `worker.yaml` - For worker nodes
- `talosconfig` - For talosctl client

### 3. Customize Configurations

Apply necessary patches for:
- **Network settings**: Static IPs, routes, VLANs
- **CNI**: Disable built-in CNI if using Cilium/Calico
- **Install disk**: Specify correct disk path
- **Certificate SANs**: Add load balancer IP/hostname
- **Cluster discovery**: Configure if needed

Example patch:
```yaml
machine:
  network:
    interfaces:
      - interface: eth0
        addresses:
          - 192.168.1.10/24
        routes:
          - network: 0.0.0.0/0
            gateway: 192.168.1.1
cluster:
  network:
    cni:
      name: none  # Install Cilium separately
```

### 4. Apply Configurations to Nodes

For each node:
```bash
# Control plane nodes
talosctl apply-config --insecure --nodes [IP] --file controlplane.yaml

# Worker nodes
talosctl apply-config --insecure --nodes [IP] --file worker.yaml
```

Wait for nodes to boot and apply configurations.

### 5. Bootstrap Kubernetes

On first control plane node only:
```bash
talosctl bootstrap --nodes [first-controlplane-IP]
```

This initializes etcd and starts Kubernetes.

### 6. Retrieve kubeconfig

```bash
talosctl kubeconfig --nodes [controlplane-IP]
```

### 7. Verify Cluster

```bash
# Check Talos health
talosctl health --nodes [all-nodes]

# Check Kubernetes nodes
kubectl get nodes

# Verify etcd
talosctl etcd members --nodes [controlplane-IP]
```

### 8. Install CNI (if using Cilium/Calico)

If CNI set to `none`, launch **k8s-network-engineer** to install:
```bash
helm install cilium cilium/cilium --namespace kube-system
```

### 9. Post-Installation Tasks

- Configure storage (if needed)
- Set up monitoring
- Apply security policies
- Configure backups (etcd snapshots)

## Output Format

### Talos Cluster Configuration Summary

**Cluster Information:**
- Name: [cluster-name]
- Talos Version: [version]
- Kubernetes Version: [version]
- Endpoint: https://[endpoint]:6443

**Control Plane Nodes:**
- [hostname]: [IP] - [status]
- [hostname]: [IP] - [status]
- [hostname]: [IP] - [status]

**Worker Nodes:**
- [hostname]: [IP] - [status]
- [hostname]: [IP] - [status]

**Network Configuration:**
- CNI: [Cilium/Calico/None]
- Pod CIDR: [range]
- Service CIDR: [range]

**Configuration Files:**
```
✓ controlplane.yaml - Apply to control plane nodes
✓ worker.yaml - Apply to worker nodes
✓ talosconfig - Configure talosctl client
```

### Next Steps

1. **Configure talosctl**:
```bash
export TALOSCONFIG=$PWD/talosconfig
talosctl config endpoint [controlplane-IPs]
talosctl config node [any-controlplane-IP]
```

2. **Verify cluster**:
```bash
kubectl get nodes
kubectl get pods -A
```

3. **Install CNI** (if needed):
```bash
helm install cilium cilium/cilium -n kube-system
```

4. **Deploy workloads**:
```bash
kubectl apply -f your-manifests/
```

### Useful talosctl Commands

```bash
# Check node status
talosctl dashboard --nodes [IP]

# View logs
talosctl logs --nodes [IP] kubelet

# Upgrade Talos
talosctl upgrade --nodes [IP] --image ghcr.io/siderolabs/installer:v1.6.0

# Upgrade Kubernetes
talosctl upgrade-k8s --nodes [IP] --to 1.29.0

# Restart services
talosctl restart kubelet --nodes [IP]

# etcd operations
talosctl etcd snapshot --nodes [IP]
```

## Troubleshooting

**Nodes not joining:**
- Verify network connectivity
- Check firewall rules (6443, 50000, 50001)
- Verify machine config applied correctly

**etcd not starting:**
- Ensure only one bootstrap command run
- Check time synchronization
- Verify disk space

**CNI not working:**
- Verify CNI set to `none` in config
- Check Cilium/Calico installation
- Verify network policies not blocking
