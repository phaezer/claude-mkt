# Claude Code Plugin Marketplace

A curated marketplace for professional Claude Code plugins focused on infrastructure, automation, and platform engineering.

## Available Plugins

### 🚢 Kubernetes Platform Engineer
**Comprehensive Kubernetes platform engineering and operations**

- **7 Specialized Agents**: Helm charts, deployments, troubleshooting, security, Talos Linux, GitOps, architecture review
- **12 Slash Commands**: Full-stack deployments, security reviews, cluster setup, troubleshooting workflows
- **Focus Areas**: Production-grade Kubernetes operations, security hardening, GitOps workflows, platform automation

[View Plugin →](./plugins/kubernetes-platform-engineer/)

### 🔧 Ansible Engineer
**Enterprise Ansible automation development and operations**

- **5 Specialized Agents**: Orchestration, playbook development, code review, security review, Jinja2 templates
- **6 Slash Commands**: Project initialization, development workflows, comprehensive reviews
- **Focus Areas**: Infrastructure as Code, configuration management, security compliance, best practices

[View Plugin →](./plugins/ansible-engineer/)

### 🌐 Network Engineer
**Professional network infrastructure design and configuration**

- **7 Specialized Agents**: FRR routing, interfaces, netplan, SONiC NOS, architecture review, NIST security
- **8 Slash Commands**: Network design, configuration generation, security reviews, deployment orchestration
- **Focus Areas**: Data center networking, routing protocols (BGP/OSPF/IS-IS), NIST compliance, infrastructure automation

[View Plugin →](./plugins/network-engineer/)

## Quick Start

### Installation

1. Clone this marketplace repository:
   ```bash
   git clone <repository-url> claude-mkt
   cd claude-mkt
   ```

2. Install a plugin in Claude Code:
   ```bash
   # Navigate to the plugin directory
   cd plugins/kubernetes-platform-engineer

   # Follow Claude Code plugin installation instructions
   ```

### Usage

Each plugin provides:
- **Specialized Agents** - Autonomous experts that can be launched via the Task tool
- **Slash Commands** - Quick-access workflows accessible via `/command-name`

Example workflow:
```
# Use a slash command for guided workflow
/k8s-deploy my-app production

# Or launch an agent directly for complex tasks
Task tool → kubernetes-orchestrator agent
```

## Plugin Details

### Kubernetes Platform Engineer

**Agents:**
- `kubernetes-orchestrator` - Coordinates complex multi-phase Kubernetes operations
- `helm-chart-developer` - Develops production-ready Helm charts
- `kubernetes-deployer` - Handles application deployments with validation
- `kubernetes-troubleshooter` - Diagnoses and resolves cluster issues
- `kubernetes-security-reviewer` - Security audits based on CIS benchmarks
- `talos-linux-engineer` - Talos Linux cluster management
- `gitops-engineer` - ArgoCD and Flux GitOps workflows
- `kubernetes-architecture-reviewer` - Architecture and manifest review

**Commands:**
- `/k8s-deploy` - Deploy applications with full validation
- `/k8s-full-stack-deploy` - Complete stack deployment orchestration
- `/k8s-troubleshoot` - Guided troubleshooting workflows
- `/k8s-security-review` - Comprehensive security audits
- `/k8s-setup-talos` - Talos Linux cluster setup
- `/k8s-setup-gitops` - GitOps environment configuration
- `/develop-helm-chart` - Helm chart development
- `/review-k8s-manifests` - Manifest review and validation
- And 4 more specialized commands

### Ansible Engineer

**Agents:**
- `ansible-orchestrator` - Coordinates Ansible project workflows
- `ansible-developer` - Develops playbooks, roles, and tasks
- `ansible-code-reviewer` - Code quality and best practices review
- `ansible-security-reviewer` - Security compliance and vulnerability review
- `jinja2-template-engineer` - Jinja2 template development

**Commands:**
- `/ansible-project-init` - Initialize complete Ansible projects
- `/develop-ansible` - Role and playbook development
- `/review-ansible-code` - Code quality review
- `/review-ansible-security` - Security and compliance review
- `/create-jinja2-template` - Template development workflow
- `/ansible-full-review` - Comprehensive code and security review

### Network Engineer

**Agents:**
- `network-orchestrator` - Coordinates network engineering workflows
- `frr-config-generator` - FRRouting (BGP, OSPF, IS-IS) configuration
- `interfaces-config-generator` - Debian/Ubuntu /etc/network/interfaces
- `netplan-config-generator` - Modern Ubuntu netplan YAML
- `sonic-engineer` - SONiC NOS configuration (data center switches)
- `network-architecture-reviewer` - Configuration review and validation
- `network-security-reviewer` - NIST-based security review

**Commands:**
- `/design-network` - Network architecture design
- `/generate-frr-config` - FRR routing configuration
- `/generate-interfaces-config` - Traditional interfaces configuration
- `/generate-netplan-config` - Modern Ubuntu networking
- `/sonic-config` - SONiC switch configuration
- `/review-network-config` - Configuration review
- `/review-network-security` - NIST security review
- `/deploy-network` - End-to-end deployment orchestration

## Features

### Production-Ready
- Comprehensive step-by-step workflows
- Extensive validation and testing procedures
- Rollback procedures for safe operations
- Best practices and security hardening

### Security-Focused
- NIST compliance frameworks
- CIS benchmark alignment
- Security reviews and audits
- Vulnerability scanning and remediation

### Well-Documented
- Detailed agent descriptions
- Example configurations
- Troubleshooting guides
- Validation commands

### Modular Design
- Orchestrator pattern for complex workflows
- Specialized agents for focused tasks
- Composable commands and workflows
- Easy to extend and customize

## Contributing

### Adding a New Plugin

1. Create plugin directory:
   ```bash
   mkdir -p plugins/my-plugin/{agents,commands}
   ```

2. Create `plugin.json`:
   ```json
   {
     "name": "my-plugin",
     "version": "1.0.0",
     "description": "Plugin description",
     "agents": [...],
     "commands": [...]
   }
   ```

3. Add agents in `agents/` directory with YAML frontmatter:
   ```yaml
   ---
   name: agent-name
   description: Use this agent when...
   model: sonnet|opus
   color: color-name
   ---
   ```

4. Add commands in `commands/` directory with frontmatter and workflows:
   ```yaml
   ---
   description: Command description
   argument-hint: Optional arguments
   ---
   ```

5. Update `.claude-plugin/marketplace.json`

### Plugin Quality Standards

- **Agents**: Include frontmatter, comprehensive documentation, use cases, examples
- **Commands**: Step-by-step workflows, validation commands, troubleshooting, rollback procedures
- **Testing**: Test all agents and commands before publishing
- **Documentation**: Clear README with usage examples

## Repository Structure

```
claude-mkt/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace metadata
├── plugins/
│   ├── kubernetes-platform-engineer/
│   │   ├── plugin.json           # Plugin configuration
│   │   ├── agents/               # Specialized agents
│   │   │   ├── kubernetes-orchestrator.md
│   │   │   ├── helm-chart-developer.md
│   │   │   └── ...
│   │   └── commands/             # Slash commands
│   │       ├── k8s-deploy.md
│   │       ├── k8s-troubleshoot.md
│   │       └── ...
│   ├── ansible-engineer/
│   │   ├── plugin.json
│   │   ├── agents/
│   │   └── commands/
│   └── network-engineer/
│       ├── plugin.json
│       ├── agents/
│       └── commands/
└── README.md                     # This file
```

## Best Practices

### Using Orchestrators
For complex multi-step operations, use orchestrator agents that can coordinate multiple specialist agents:

```
/k8s-full-stack-deploy my-app
  └─ kubernetes-orchestrator
      ├─ helm-chart-developer (generate charts)
      ├─ kubernetes-architecture-reviewer (review)
      ├─ kubernetes-security-reviewer (security audit)
      └─ kubernetes-deployer (deploy with validation)
```

### Security Reviews
Always run security reviews for production deployments:

```
/k8s-security-review production-namespace
/review-ansible-security playbook.yml
/review-network-security network-config/
```

### Iterative Development
Use specialist agents for focused development, then review:

```
1. /develop-helm-chart my-app
2. /review-k8s-manifests charts/my-app
3. /k8s-deploy my-app staging
4. /k8s-security-review staging
5. /k8s-deploy my-app production
```

## Support

For issues, questions, or contributions:
- Open an issue in this repository
- Review existing plugin documentation
- Check agent descriptions for usage guidance

## License

See individual plugin directories for licensing information.

## Version History

### 1.0.0 (Current)
- ✅ Kubernetes Platform Engineer plugin
- ✅ Ansible Engineer plugin
- ✅ Network Engineer plugin
- ✅ 22 specialized agents
- ✅ 26 slash commands
- ✅ Comprehensive documentation
- ✅ Production-ready workflows
