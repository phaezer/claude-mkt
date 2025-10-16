---
description: Security review of Kubernetes configurations
argument-hint: Optional configurations to review
---

# Kubernetes Security Review

You are conducting a comprehensive security review of Kubernetes configurations and deployments using the k8s-security-reviewer agent.

## Workflow

### 1. Identify Review Scope

Determine what needs to be reviewed:
- **New configurations**: YAML manifests before deployment
- **Existing deployments**: Running workloads in cluster
- **Helm charts**: Chart templates and values
- **Entire namespace**: All resources in a namespace
- **Cluster-wide**: Cluster roles, policies, admission controllers

If user hasn't specified, ask for:
- Target configurations or namespace
- Environment criticality (dev/staging/production)
- Compliance requirements (CIS, PCI-DSS, SOC 2, HIPAA)
- Specific security concerns or focus areas

### 2. Gather Configuration Files

For file-based review:
- Use `Read` tool to access manifest files
- Use `Glob` to find all YAML files in directory
- Use `Bash` with `kubectl` to extract running configurations

For cluster review:
```bash
kubectl get all -n [namespace] -o yaml
kubectl get networkpolicies -n [namespace] -o yaml
kubectl get rolebindings,clusterrolebindings -o yaml
kubectl get psp,pdb -n [namespace] -o yaml
```

### 3. Launch Security Review Agent

Launch **k8s-security-reviewer** agent with:
- All configuration files or cluster export
- Environment context (production requires stricter standards)
- Compliance requirements
- Specific focus areas if any

### 4. Analyze Security Findings

The agent will assess:
- **Pod Security**: privileged containers, security contexts, capabilities
- **RBAC**: overly permissive roles, cluster-admin usage
- **Network Policies**: segmentation, default deny, egress control
- **Secrets Management**: hardcoded secrets, proper encryption
- **Image Security**: tag usage, registry sources, vulnerability scanning
- **Resource Limits**: DoS prevention, resource quotas
- **Admission Control**: PSS/PSP enforcement

### 5. Categorize Issues

Organize findings by severity:

**Critical** (Block deployment):
- Privileged containers in production
- Hardcoded secrets or credentials
- Missing network policies in production
- Overly permissive RBAC (cluster-admin for apps)

**High** (Fix before deployment):
- Running as root
- Missing resource limits
- No Pod Disruption Budgets in production
- Missing security contexts

**Medium** (Address soon):
- Using :latest tag
- Missing readiness/liveness probes
- Insufficient RBAC granularity

**Low** (Best practice):
- Missing labels
- No pod anti-affinity
- Verbose logging

### 6. Provide Remediation Guidance

For each critical and high finding:
1. Explain the security risk
2. Show the problematic configuration
3. Provide fixed configuration
4. Include verification steps

## Output Format

### Security Review Report

#### Executive Summary
- **Overall Risk Level**: [Critical/High/Medium/Low]
- **Critical Issues**: [count] - MUST fix before deployment
- **High Issues**: [count] - Fix before production
- **Medium Issues**: [count] - Address within sprint
- **Low Issues**: [count] - Best practice improvements

#### Critical Findings

**[CRITICAL] Privileged Container**
- **Location**: `deployment/myapp` container `app`
- **Risk**: Full host access, container escape, kernel exploits
- **Current Config**:
```yaml
securityContext:
  privileged: true  # DANGEROUS
```
- **Recommended Fix**:
```yaml
securityContext:
  privileged: false
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
  runAsNonRoot: true
  runAsUser: 1000
  capabilities:
    drop: [ALL]
```
- **Verification**: `kubectl describe pod [pod] | grep "Privileged:"`

#### High Priority Findings

[Similar format for each high-priority issue]

#### Compliance Assessment

- **CIS Kubernetes Benchmark**: [Pass/Fail items]
- **Pod Security Standards**: [Baseline/Restricted]
- **Industry Requirements**: [Specific to requested compliance]

#### Recommended Actions

Priority 1 (Before Deployment):
1. [Action with file:line reference]
2. [Action with file:line reference]

Priority 2 (This Sprint):
1. [Action]
2. [Action]

Priority 3 (Backlog):
1. [Action]
2. [Action]

### Validation Commands

After applying fixes:
```bash
# Verify security contexts
kubectl get pods -n [namespace] -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].securityContext}{"\n"}{end}'

# Check for privileged pods
kubectl get pods -n [namespace] -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].securityContext.privileged}{"\n"}{end}'

# Verify network policies exist
kubectl get networkpolicies -n [namespace]

# Check RBAC
kubectl auth can-i --list -n [namespace]
```

## Decision Matrix

**When to block deployment:**
- Any CRITICAL findings in production
- Multiple HIGH findings in production
- Compliance requirement violations

**When to allow with warnings:**
- Only MEDIUM/LOW findings
- HIGH findings in dev/staging with remediation plan

**When to require re-review:**
- After fixing CRITICAL issues
- After major configuration changes
- Before production promotion
