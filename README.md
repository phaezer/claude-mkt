# Claude Code Plugin Marketplace

A curated marketplace for professional Claude Code plugins focused on infrastructure, automation, and platform engineering.

## Available Plugins

### 🚢 Kubernetes Platform Engineer
**Comprehensive Kubernetes platform engineering and operations**

- **11 Specialized Agents**: Helm charts, cluster management, monitoring, security, Talos/Flatcar Linux, GitOps, CDK8s
- **6 Slash Commands**: Deployments, security reviews, cluster setup (Talos/Flatcar), GitOps configuration
- **Focus Areas**: Production-grade Kubernetes operations, security hardening, GitOps workflows, platform automation

[View Plugin →](./plugins/k8s/)

### 🔧 Ansible Engineer
**Enterprise Ansible automation development and operations**

- **5 Specialized Agents**: Orchestration, playbook development, code review, security review, Jinja2 templates
- **6 Slash Commands**: Project initialization, development workflows, comprehensive reviews
- **Focus Areas**: Infrastructure as Code, configuration management, security compliance, best practices

[View Plugin →](./plugins/ansible/)

### 🌐 Network Engineer
**Professional network infrastructure design and configuration**

- **7 Specialized Agents**: FRR routing, interfaces, netplan, SONiC NOS, architecture review, NIST security
- **8 Slash Commands**: Network design, configuration generation, security reviews, deployment orchestration
- **Focus Areas**: Data center networking, routing protocols (BGP/OSPF/IS-IS), NIST compliance, infrastructure automation

[View Plugin →](./plugins/networking/)

### 🔌 MCP Engineer
**Model Context Protocol server and client development**

- **8 Specialized Agents**: Server/client architecture, Python/TypeScript development, testing, deployment, security
- **7 Slash Commands**: Project initialization, server/client development, testing, security review, deployment
- **Focus Areas**: MCP protocol implementation, FastMCP & official SDKs, MCP Inspector testing, Docker deployment

[View Plugin →](./plugins/mcp/)

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
   cd plugins/k8s

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
- `k8s-orchestrator` - Coordinates complex multi-phase Kubernetes operations
- `k8s-config-developer` - Develops Kubernetes manifests for standard K8s and K3s
- `helm-chart-developer` - Develops production-ready Helm charts
- `k8s-cluster-manager` - Manages clusters using kubectl for deployments and operations
- `k8s-monitoring-analyst` - Analyzes monitoring data and provides optimization recommendations
- `k8s-security-reviewer` - Security audits based on CIS benchmarks
- `k8s-network-engineer` - Engineers cluster networking with CNIs (Cilium, Calico)
- `talos-linux-expert` - Talos Linux cluster specialist
- `flatcar-linux-expert` - Flatcar Container Linux cluster specialist
- `k8s-cicd-engineer` - GitOps CI/CD with ArgoCD and Flux
- `cdk8s-engineer` - CDK8s (Cloud Development Kit for Kubernetes) development

**Commands:**
- `/k8s-deploy` - Deploy applications with full validation
- `/k8s-full-stack-deploy` - Complete stack deployment orchestration
- `/k8s-security-review` - Comprehensive security audits
- `/k8s-setup-talos` - Talos Linux cluster setup
- `/k8s-setup-flatcar` - Flatcar Linux cluster setup
- `/k8s-setup-gitops` - GitOps environment configuration (ArgoCD/Flux)

### Ansible Engineer

**Agents:**
- `ansible-orchestrator` - Coordinates Ansible project workflows
- `ansible-developer` - Develops playbooks, roles, and tasks
- `ansible-code-reviewer` - Code quality and best practices review
- `ansible-security-reviewer` - Security compliance and vulnerability review
- `jinja2-developer` - Jinja2 template development

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

### MCP Engineer

**Agents:**
- `mcp-orchestrator` - Coordinates MCP development tasks across specialized agents
- `mcp-server-architect` - Designs MCP server architecture (tools, resources, prompts)
- `mcp-client-architect` - Designs MCP client architecture for server integration
- `mcp-python-developer` - Develops MCP servers/clients in Python (FastMCP & SDK, Python 3.11+)
- `mcp-typescript-developer` - Develops MCP servers/clients in TypeScript (@modelcontextprotocol/sdk)
- `mcp-testing-engineer` - Tests MCP servers/clients with unit tests and MCP Inspector
- `mcp-deployment-engineer` - Handles local and Docker deployment of MCP servers
- `mcp-security-reviewer` - Security reviews for MCP implementations

**Commands:**
- `/mcp-init-project` - Initialize new MCP server or client project
- `/develop-mcp-server` - Develop MCP server with tools, resources, and prompts
- `/develop-mcp-client` - Develop MCP client for server integration
- `/test-mcp` - Test MCP server or client functionality
- `/review-mcp-security` - Security review of MCP implementations
- `/deploy-mcp` - Deploy MCP server locally or with Docker
- `/mcp-full-stack-dev` - End-to-end orchestrated MCP development workflow

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
│   ├── k8s/
│   │   ├── plugin.json           # Plugin configuration
│   │   ├── README.md             # Plugin documentation
│   │   ├── agents/               # Specialized agents
│   │   │   ├── k8s-orchestrator.md
│   │   │   ├── helm-chart-developer.md
│   │   │   └── ...
│   │   └── commands/             # Slash commands
│   │       ├── k8s-deploy.md
│   │       ├── k8s-security-review.md
│   │       └── ...
│   ├── ansible/
│   │   ├── plugin.json
│   │   ├── README.md
│   │   ├── agents/
│   │   └── commands/
│   ├── networking/
│   │   ├── plugin.json
│   │   ├── README.md
│   │   ├── agents/
│   │   └── commands/
│   └── mcp/
│       ├── plugin.json
│       ├── README.md
│       ├── agents/
│       └── commands/
└── README.md                     # This file
```

## Best Practices

### Using Orchestrators
For complex multi-step operations, use orchestrator agents that can coordinate multiple specialist agents:

```
/k8s-full-stack-deploy my-app
  └─ k8s-orchestrator
      ├─ helm-chart-developer (generate charts)
      ├─ k8s-config-developer (develop manifests)
      ├─ k8s-security-reviewer (security audit)
      └─ k8s-cluster-manager (deploy with validation)
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
1. Task tool → helm-chart-developer (develop chart)
2. Task tool → k8s-security-reviewer (review security)
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
- ✅ Kubernetes Platform Engineer plugin (11 agents, 6 commands)
- ✅ Ansible Engineer plugin (5 agents, 6 commands)
- ✅ Network Engineer plugin (7 agents, 8 commands)
- ✅ MCP Engineer plugin (8 agents, 7 commands)
- ✅ 31 specialized agents total
- ✅ 27 slash commands total
- ✅ Comprehensive documentation
- ✅ Production-ready workflows
