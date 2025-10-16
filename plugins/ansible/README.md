# Ansible Plugin

A comprehensive Claude Code plugin for developing, reviewing, and securing Ansible automation with specialized agents for roles, playbooks, Jinja2 templates, and orchestrated workflows.

## Overview

The Ansible plugin provides specialized agents and workflows for:
- Ansible role and playbook development
- Comprehensive code review for quality and best practices
- Security review for vulnerabilities and compliance
- Jinja2 template creation with domain-specific expertise
- Orchestrated workflows coordinating multiple specialist agents

## Agents

### ansible-orchestrator
Main orchestrator agent that coordinates complex Ansible development and review tasks across specialized agents. Use this for multi-step workflows requiring coordination between development, templating, and reviews.

**Use when:**
- Developing complete automation solutions
- Coordinating multiple specialist agents
- Full project reviews (code + security)
- Complex workflows requiring orchestration

### ansible-developer
Develops Ansible roles, playbooks, tasks, handlers, variables, and modules following best practices.

**Creates:**
- Complete Ansible roles with proper structure
- Playbooks for various use cases
- Reusable tasks and handlers
- Well-organized variable files
- Comprehensive documentation

**Follows:**
- Idempotency principles
- Proper module selection
- Standard role structure
- Best practices for maintainability

### ansible-code-reviewer
Reviews Ansible code for errors, potential mistakes, organization, and best practices.

**Reviews:**
- Idempotency of operations
- Module selection and usage
- Task naming and organization
- Error handling and resilience
- Variable usage and naming
- Handler implementation
- Check mode support
- Performance considerations
- ansible-lint compliance

### ansible-security-reviewer
Reviews Ansible code for security vulnerabilities and compliance with security standards.

**Evaluates:**
- Credential and secret management
- Privilege escalation (sudo/become)
- File and directory permissions
- Command injection risks
- Template injection vulnerabilities
- Network security (HTTPS, certificates)
- Logging of sensitive data
- Supply chain security
- Compliance requirements (PCI-DSS, HIPAA, GDPR)

### jinja2-developer
Develops Jinja2 templates for Ansible with the ability to coordinate with domain-specific expert agents.

**Capabilities:**
- Create syntactically correct Jinja2 templates
- Consult domain specialists for technical accuracy
- Incorporate Ansible-specific features
- Implement proper validation and security
- Generate comprehensive documentation

**Unique Feature:**
Before creating templates with domain-specific content, this agent asks which specialist to consult (e.g., network-engineer for network configs, database specialist for DB configs) to ensure technical accuracy.

## Slash Commands

### /develop-ansible
Develop Ansible roles and playbooks.

**Usage:**
```
/develop-ansible
```

Creates production-ready Ansible automation with proper structure, idempotency, and documentation.

### /review-ansible-code
Review Ansible code for errors, organization, and best practices.

**Usage:**
```
/review-ansible-code
```

Performs comprehensive code quality review with prioritized findings and recommendations.

### /review-ansible-security
Security review of Ansible code for vulnerabilities and compliance.

**Usage:**
```
/review-ansible-security
```

Conducts thorough security assessment covering credentials, privileges, permissions, and compliance.

### /create-jinja2-template
Create Jinja2 templates with domain-specific expertise.

**Usage:**
```
/create-jinja2-template
```

Develops templates by coordinating with domain specialist agents for technical accuracy.

### /ansible-full-review
Complete Ansible review including both code quality and security.

**Usage:**
```
/ansible-full-review
```

Orchestrated review workflow combining code and security reviews with synthesized findings.

### /ansible-project-init
Initialize new Ansible project with proper structure.

**Usage:**
```
/ansible-project-init
```

Creates complete project structure including roles, inventory, variables, vault, and documentation.

## Key Features

### Idempotency Focus
All development follows Ansible's idempotency principles, ensuring operations can be safely run multiple times without side effects.

### Security-First Approach
Built-in security review capabilities for:
- Ansible Vault integration
- Credential management
- Privilege escalation
- Secure file permissions
- Injection prevention
- Compliance validation

### Domain Expert Integration
The jinja2-developer can coordinate with specialist agents from other plugins (like network-engineer) to create technically accurate templates for specialized configurations.

### Best Practices Enforcement
Reviews check for:
- ansible-lint compliance
- Proper module usage
- Standard role structure
- Error handling
- Documentation completeness
- Check mode support

### Multi-Environment Support
Project initialization and development support multiple environments (dev, staging, production) with proper inventory and variable organization.

## Example Workflows

### Develop a New Ansible Role
```
/develop-ansible

# Provide requirements:
# - Role name: nginx_secure
# - Purpose: Deploy and secure Nginx web server
# - Platforms: Ubuntu 20.04+, Debian 11+
# - Features: SSL/TLS, security hardening, log rotation

# Result: Complete role with:
# - tasks/main.yml with idempotent tasks
# - handlers/main.yml for service management
# - defaults/main.yml with sensible defaults
# - templates/ with Nginx configs
# - README.md with documentation
```

### Create Jinja2 Template with Network Expertise
```
/create-jinja2-template

# Agent asks: "This requires network expertise. Which specialist should I consult?"
# You specify: "Use frr-config-generator from network-engineer plugin"

# Agent coordinates:
# 1. Consults frr-config-generator for BGP best practices
# 2. Creates Jinja2 template incorporating specialist guidance
# 3. Validates template syntax
# 4. Provides complete documentation

# Result: Production-ready template with expert-level configuration
```

### Full Project Review
```
/ansible-full-review

# Orchestrator coordinates:
# 1. ansible-code-reviewer: Quality and best practices
# 2. ansible-security-reviewer: Security and compliance
# 3. Synthesizes findings with priorities
# 4. Provides remediation roadmap

# Result: Comprehensive report with:
# - Executive summary
# - Detailed findings by severity
# - Code and security issues
# - Prioritized action items
# - Remediation guidance
```

### Initialize New Project
```
/ansible-project-init

# Specify:
# - Project name: infrastructure-automation
# - Roles needed: common, webserver, database
# - Environments: dev, staging, prod
# - Include Vault: yes
# - Include Molecule: yes

# Creates:
# - Complete directory structure
# - ansible.cfg
# - Inventory files
# - Group and host vars
# - Encrypted vault files
# - Molecule test structure
# - Documentation
```

## Best Practices

### Development
1. Always create roles with standard structure
2. Use descriptive task names starting with verbs
3. Implement proper error handling with block/rescue
4. Make tasks idempotent
5. Support check mode (--check)
6. Use ansible_managed in templates
7. Document all variables and requirements

### Security
1. Use Ansible Vault for all secrets
2. Never hardcode credentials
3. Use `no_log: true` for sensitive operations
4. Minimal privilege escalation (become)
5. Secure file permissions (0600 for secrets)
6. Validate all user input
7. Use HTTPS with certificate validation

### Review
1. Review after development (always)
2. Security review for production code (always)
3. Re-review after significant changes
4. Address critical and high-priority issues before deployment
5. Run ansible-lint regularly
6. Test with check mode before applying

### Templates
1. Consult domain experts for specialized content
2. Document all variables and their purpose
3. Include ansible_managed header
4. Use defaults for optional variables
5. Validate required variables
6. Test rendered output
7. Secure file permissions in deployment

## Tools and Validation

### Ansible Commands
```bash
# Syntax check
ansible-playbook playbook.yml --syntax-check

# Check mode (dry run)
ansible-playbook playbook.yml --check

# Run ansible-lint
ansible-lint role_name/

# Test role with Molecule
cd role_name/
molecule test

# Encrypt with Vault
ansible-vault encrypt vars/secrets.yml

# Edit vault file
ansible-vault edit vars/secrets.yml

# Run playbook with vault
ansible-playbook playbook.yml --ask-vault-pass
```

### Project Structure
```
ansible-project/
├── ansible.cfg              # Ansible configuration
├── requirements.yml         # Role dependencies
├── .gitignore              # Git ignore rules
├── inventory/
│   ├── dev/
│   │   ├── hosts           # Development inventory
│   │   └── group_vars/
│   ├── staging/
│   │   ├── hosts
│   │   └── group_vars/
│   └── prod/
│       ├── hosts
│       └── group_vars/
├── roles/
│   ├── common/
│   ├── webserver/
│   └── database/
├── playbooks/
│   ├── site.yml            # Main playbook
│   ├── webservers.yml
│   └── databases.yml
└── group_vars/
    └── all/
        ├── vars.yml        # Common variables
        └── vault.yml       # Encrypted secrets
```

## Security Standards

The ansible-security-reviewer evaluates against:
- **PCI-DSS**: Payment card industry standards
- **HIPAA**: Healthcare data protection
- **GDPR**: General data protection regulation
- **CIS Benchmarks**: Center for Internet Security
- **NIST**: National Institute of Standards and Technology

## Integration with Other Plugins

### Domain Specialist Coordination
The jinja2-developer can coordinate with specialist agents from other plugins:

**Example: Network Configuration Template**
- Template needs FRR BGP config
- User specifies: "Consult frr-config-generator from network-engineer plugin"
- jinja2-developer launches frr-config-generator
- Incorporates specialist's BGP configuration into template
- Delivers Jinja2 template with expert-level network configuration

**Example: Database Configuration Template**
- Template needs PostgreSQL configuration
- User specifies: "Consult database-specialist agent"
- jinja2-developer coordinates with database expert
- Creates template with optimized DB configuration
- Validates and documents template

## Supported Ansible Versions

- Ansible Core 2.12+
- Ansible 2.9+ (with limitations)

## Installation

This plugin is part of the claude-mkt marketplace. To use:

1. Ensure Claude Code is installed
2. Load this marketplace
3. Agents and slash commands will be available

## Common Use Cases

1. **Infrastructure as Code**: Develop complete infrastructure automation
2. **Configuration Management**: Manage configurations across environments
3. **Application Deployment**: Automate application deployments
4. **Security Hardening**: Implement security configurations
5. **Compliance Automation**: Ensure compliance with standards
6. **Multi-Cloud Deployment**: Deploy across different cloud platforms
7. **Disaster Recovery**: Automate recovery procedures

## License

[Specify your license]

## Version

Version 1.0.0
