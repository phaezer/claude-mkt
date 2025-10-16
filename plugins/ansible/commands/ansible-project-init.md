---
description: Initialize new Ansible project structure
argument-hint: Optional project requirements
---

# Ansible Project Initialization

You are setting up a new Ansible project with proper structure, configuration, and best practices using the ansible-developer agent.

## Workflow

### 1. Gather Project Requirements

If not specified, ask for:
- **Project information**:
  - Project name and purpose
  - Team or organization name
  - Git repository location
- **Target infrastructure**:
  - Target platforms (Linux, Windows, network devices)
  - Number of environments (dev, staging, production)
  - Approximate number of hosts per environment
- **Initial roles needed**:
  - Application roles (web server, database, app)
  - Infrastructure roles (common, security, monitoring)
  - Third-party roles to include
- **Security setup**:
  - Ansible Vault for secrets (yes/no)
  - Vault password file location or method
  - Separate vaults per environment
- **Testing setup**:
  - Include Molecule testing (yes/no)
  - Testing platforms (Docker, Vagrant, cloud)
  - CI/CD integration planned
- **Standards and requirements**:
  - ansible-lint configuration
  - Pre-commit hooks
  - Documentation requirements

### 2. Create Directory Structure

Launch **ansible-developer** to create standard Ansible project structure:

```
project-name/
├── ansible.cfg                 # Ansible configuration
├── .gitignore                  # Git ignore patterns
├── .ansible-lint              # Lint configuration
├── README.md                   # Project documentation
├── requirements.yml            # Galaxy role dependencies
├── requirements.txt            # Python dependencies
│
├── inventory/
│   ├── production/
│   │   ├── hosts.yml          # Production inventory
│   │   └── group_vars/
│   │       └── all/
│   │           ├── vars.yml   # Production variables
│   │           └── vault.yml  # Encrypted secrets
│   ├── staging/
│   │   ├── hosts.yml
│   │   └── group_vars/
│   └── development/
│       ├── hosts.yml
│       └── group_vars/
│
├── roles/
│   ├── common/                # Common role
│   │   ├── README.md
│   │   ├── defaults/main.yml
│   │   ├── handlers/main.yml
│   │   ├── tasks/main.yml
│   │   ├── templates/
│   │   ├── files/
│   │   └── vars/main.yml
│   └── .gitkeep
│
├── playbooks/
│   ├── site.yml               # Master playbook
│   ├── provision.yml          # Provisioning playbook
│   ├── deploy.yml             # Deployment playbook
│   └── maintenance.yml        # Maintenance playbook
│
├── group_vars/
│   └── all.yml                # Global variables
│
├── host_vars/                 # Host-specific variables
│   └── .gitkeep
│
├── files/                     # Static files
│   └── .gitkeep
│
├── templates/                 # Global templates
│   └── .gitkeep
│
├── filter_plugins/            # Custom filters
│   └── .gitkeep
│
├── library/                   # Custom modules
│   └── .gitkeep
│
└── molecule/                  # Testing (optional)
    └── default/
        ├── molecule.yml
        ├── converge.yml
        └── verify.yml
```

### 3. Configure ansible.cfg

Create optimized ansible.cfg:

```ini
[defaults]
# Inventory
inventory = ./inventory/development

# Connection
host_key_checking = False
timeout = 30
forks = 20

# Output
stdout_callback = yaml
callbacks_enabled = profile_tasks, timer
display_skipped_hosts = False

# Roles
roles_path = ./roles:~/.ansible/roles:/usr/share/ansible/roles

# Collections
collections_paths = ./collections:~/.ansible/collections:/usr/share/ansible/collections

# Logs
log_path = ./ansible.log

# Vault
vault_password_file = ./.vault-pass  # Do not commit this file!

# Retry
retry_files_enabled = False

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

[ssh_connection]
pipelining = True
control_path = /tmp/ansible-ssh-%%h-%%p-%%r
ssh_args = -o ControlMaster=auto -o ControlPersist=60s

[inventory]
enable_plugins = yaml, ini, script
```

### 4. Create .gitignore

Protect sensitive files:

```gitignore
# Ansible
*.retry
.ansible/
.vault-pass
vault-pass.txt
*.secret
*.key

# Logs
*.log
ansible.log

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/
ENV/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Molecule
.molecule/
.cache/

# Terraform (if using)
*.tfstate
*.tfstate.backup
.terraform/
```

### 5. Setup Ansible Vault

If vault enabled:

```bash
# Create vault password file (DO NOT COMMIT)
echo "your-secure-password" > .vault-pass
chmod 600 .vault-pass

# Create encrypted vault files for each environment
ansible-vault create inventory/production/group_vars/all/vault.yml
ansible-vault create inventory/staging/group_vars/all/vault.yml
ansible-vault create inventory/development/group_vars/all/vault.yml
```

**Vault file template**:
```yaml
---
# Encrypted secrets for [environment]

# Database credentials
vault_db_root_password: "changeme"
vault_db_user_password: "changeme"

# API keys
vault_api_key: "changeme"

# SSH keys
vault_deploy_key: |
  -----BEGIN OPENSSH PRIVATE KEY-----
  ...
  -----END OPENSSH PRIVATE KEY-----

# SSL certificates
vault_ssl_key: |
  -----BEGIN PRIVATE KEY-----
  ...
  -----END PRIVATE KEY-----
```

### 6. Create requirements.yml

Define Galaxy role dependencies:

```yaml
---
# Ansible Galaxy roles

roles:
  # Community roles
  - name: geerlingguy.docker
    version: "6.1.0"

  - name: geerlingguy.nginx
    version: "3.1.4"

collections:
  # Community collections
  - name: community.general
    version: ">=7.0.0"

  - name: ansible.posix
    version: ">=1.5.0"

  - name: community.postgresql
    version: ">=3.0.0"
```

**Install dependencies**:
```bash
ansible-galaxy install -r requirements.yml
```

### 7. Create Example Inventory

**inventory/development/hosts.yml**:
```yaml
---
all:
  children:
    webservers:
      hosts:
        web01:
          ansible_host: 192.168.1.10
          ansible_user: ansible
        web02:
          ansible_host: 192.168.1.11
          ansible_user: ansible
      vars:
        nginx_port: 80
        app_environment: development

    databases:
      hosts:
        db01:
          ansible_host: 192.168.1.20
          ansible_user: ansible
      vars:
        postgresql_version: "15"
        db_environment: development

    loadbalancers:
      hosts:
        lb01:
          ansible_host: 192.168.1.30
          ansible_user: ansible

  vars:
    ansible_python_interpreter: /usr/bin/python3
    ansible_become: yes
```

### 8. Create Master Playbook

**playbooks/site.yml**:
```yaml
---
- name: Configure all servers
  hosts: all
  gather_facts: yes
  become: yes

  pre_tasks:
    - name: Update apt cache (Debian/Ubuntu)
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"
      tags: always

    - name: Display environment
      ansible.builtin.debug:
        msg: "Configuring {{ inventory_hostname }} in {{ app_environment | default('unknown') }} environment"
      tags: always

  roles:
    - role: common
      tags: common

- name: Configure web servers
  hosts: webservers
  become: yes

  roles:
    - role: nginx
      tags: nginx
    - role: application
      tags: app

- name: Configure database servers
  hosts: databases
  become: yes

  roles:
    - role: postgresql
      tags: database

- name: Configure load balancers
  hosts: loadbalancers
  become: yes

  roles:
    - role: haproxy
      tags: loadbalancer
```

### 9. Create Example Role

Create a common role:

```bash
ansible-galaxy role init roles/common
```

**roles/common/tasks/main.yml**:
```yaml
---
- name: Ensure common packages are installed
  ansible.builtin.package:
    name:
      - curl
      - wget
      - git
      - vim
      - htop
      - net-tools
    state: present
  tags: packages

- name: Configure timezone
  community.general.timezone:
    name: "{{ timezone | default('UTC') }}"
  tags: timezone

- name: Configure NTP
  ansible.builtin.include_tasks: ntp.yml
  when: configure_ntp | default(true)
  tags: ntp

- name: Harden SSH configuration
  ansible.builtin.include_tasks: ssh.yml
  tags: ssh

- name: Configure firewall
  ansible.builtin.include_tasks: firewall.yml
  when: configure_firewall | default(true)
  tags: firewall
```

### 10. Setup Molecule Testing (Optional)

If testing enabled:

```bash
# Install molecule
pip install molecule molecule-plugins[docker] ansible-lint

# Initialize molecule for a role
cd roles/common
molecule init scenario default --driver-name docker
```

**molecule/default/molecule.yml**:
```yaml
---
dependency:
  name: galaxy
  options:
    requirements-file: requirements.yml

driver:
  name: docker

platforms:
  - name: ubuntu-20.04
    image: geerlingguy/docker-ubuntu2004-ansible
    privileged: true
    pre_build_image: true

  - name: ubuntu-22.04
    image: geerlingguy/docker-ubuntu2204-ansible
    privileged: true
    pre_build_image: true

provisioner:
  name: ansible
  config_options:
    defaults:
      callbacks_enabled: profile_tasks
  inventory:
    host_vars:
      ubuntu-20.04:
        ansible_python_interpreter: /usr/bin/python3
      ubuntu-22.04:
        ansible_python_interpreter: /usr/bin/python3

verifier:
  name: ansible

lint: |
  set -e
  ansible-lint
  yamllint .
```

### 11. Create ansible-lint Configuration

**.ansible-lint**:
```yaml
---
# Ansible-lint configuration

# Exclude paths
exclude_paths:
  - .github/
  - molecule/
  - .molecule/
  - venv/

# Enable specific rules
enable_list:
  - yaml
  - no-changed-when
  - no-handler

# Skip specific rules for entire project
skip_list:
  - experimental
  - role-name  # If using non-standard role names

# Profile to use (min, basic, moderate, safety, shared, production)
profile: production

# Offline mode (no internet required)
offline: false

# Use colors in output
use_color: true
```

### 12. Create README.md

**README.md**:
```markdown
# Project Name

Brief description of the Ansible project and its purpose.

## Requirements

- Ansible 2.9 or higher
- Python 3.8 or higher
- Target systems: Ubuntu 20.04+, Debian 11+

## Project Structure

- `inventory/` - Inventory files per environment
- `roles/` - Custom Ansible roles
- `playbooks/` - Ansible playbooks
- `group_vars/` - Group variables
- `host_vars/` - Host-specific variables

## Quick Start

### 1. Install Dependencies

\`\`\`bash
# Install Python dependencies
pip install -r requirements.txt

# Install Ansible Galaxy roles
ansible-galaxy install -r requirements.yml
\`\`\`

### 2. Configure Vault

\`\`\`bash
# Create vault password file (DO NOT COMMIT)
echo "your-secure-password" > .vault-pass
chmod 600 .vault-pass

# Create vault files
ansible-vault create inventory/development/group_vars/all/vault.yml
\`\`\`

### 3. Test Connection

\`\`\`bash
# Ping all hosts
ansible all -i inventory/development -m ping

# Gather facts
ansible all -i inventory/development -m setup
\`\`\`

### 4. Run Playbooks

\`\`\`bash
# Dry run (check mode)
ansible-playbook playbooks/site.yml -i inventory/development --check

# Run playbook
ansible-playbook playbooks/site.yml -i inventory/development

# Run specific tags
ansible-playbook playbooks/site.yml -i inventory/development --tags "common,nginx"

# Limit to specific hosts
ansible-playbook playbooks/site.yml -i inventory/development --limit "webservers"
\`\`\`

## Environments

- **development**: `inventory/development/`
- **staging**: `inventory/staging/`
- **production**: `inventory/production/`

Switch environments by changing the inventory path:
\`\`\`bash
ansible-playbook playbooks/site.yml -i inventory/production
\`\`\`

## Ansible Vault

### Encrypt/Decrypt Files

\`\`\`bash
# Encrypt a file
ansible-vault encrypt inventory/production/group_vars/all/vault.yml

# Decrypt a file (temporary)
ansible-vault decrypt inventory/production/group_vars/all/vault.yml

# Edit encrypted file
ansible-vault edit inventory/production/group_vars/all/vault.yml

# View encrypted file
ansible-vault view inventory/production/group_vars/all/vault.yml

# Change vault password
ansible-vault rekey inventory/production/group_vars/all/vault.yml
\`\`\`

### Encrypt Variables

\`\`\`bash
# Encrypt a string
ansible-vault encrypt_string 'secret_password' --name 'db_password'
\`\`\`

## Testing

### Syntax Check

\`\`\`bash
ansible-playbook playbooks/site.yml --syntax-check
\`\`\`

### Lint

\`\`\`bash
ansible-lint roles/
ansible-lint playbooks/
\`\`\`

### Molecule (Role Testing)

\`\`\`bash
cd roles/common

# Run all tests
molecule test

# Create test instance
molecule create

# Run playbook
molecule converge

# Verify tests
molecule verify

# Destroy test instance
molecule destroy
\`\`\`

## Development Workflow

1. Create/modify roles in `roles/`
2. Update playbooks in `playbooks/`
3. Test with ansible-lint
4. Run in check mode first
5. Test in development environment
6. Review and approve changes
7. Deploy to staging
8. Deploy to production

## Common Tasks

### Add New Server

1. Add host to inventory: `inventory/<env>/hosts.yml`
2. Configure host variables if needed: `host_vars/<hostname>.yml`
3. Run provisioning: `ansible-playbook playbooks/provision.yml -i inventory/<env> --limit <hostname>`

### Add New Role

\`\`\`bash
# Create role structure
ansible-galaxy role init roles/<role-name>

# Add role to requirements.yml if third-party
# Include role in appropriate playbook
\`\`\`

### Deploy Application

\`\`\`bash
ansible-playbook playbooks/deploy.yml -i inventory/<env> \
  --extra-vars "app_version=v1.2.3"
\`\`\`

## Troubleshooting

### Connection Issues

\`\`\`bash
# Test SSH connectivity
ansible all -i inventory/development -m ping -vvv

# Check inventory
ansible-inventory -i inventory/development --list
ansible-inventory -i inventory/development --graph
\`\`\`

### Playbook Failures

\`\`\`bash
# Run with increased verbosity
ansible-playbook playbooks/site.yml -vvv

# Check specific host
ansible-playbook playbooks/site.yml --limit <hostname> -vv
\`\`\`

### Vault Issues

\`\`\`bash
# Verify vault password
ansible-vault view inventory/development/group_vars/all/vault.yml

# Check vault password file permissions
ls -la .vault-pass  # Should be 600
\`\`\`

## Contributing

1. Follow Ansible best practices
2. Test changes with ansible-lint
3. Test in development environment first
4. Document changes in playbook/role README
5. Use meaningful commit messages

## License

[Your License]

## Author

[Your Name/Team]
```

### 13. Verify Project Setup

Run validation commands:

```bash
# Check ansible.cfg syntax
ansible-config dump

# Verify inventory structure
ansible-inventory -i inventory/development --graph
ansible-inventory -i inventory/development --list

# Test connection to hosts
ansible all -i inventory/development -m ping

# Syntax check playbooks
ansible-playbook playbooks/site.yml --syntax-check

# Lint the project
ansible-lint roles/
ansible-lint playbooks/

# Dry run
ansible-playbook playbooks/site.yml -i inventory/development --check
```

## Output Format

### Project Initialization Summary

**Project**: [project-name]
**Location**: [path]
**Environments**: [dev, staging, production]
**Initial Roles**: [list of roles created]

**Directory Structure Created**:
```
✓ ansible.cfg - Ansible configuration
✓ inventory/ - Multi-environment inventory
✓ roles/ - Custom roles
✓ playbooks/ - Master and environment playbooks
✓ group_vars/ - Global variables
✓ vault setup - Encrypted secrets per environment
✓ requirements.yml - Galaxy dependencies
✓ .gitignore - Git exclusions
✓ README.md - Project documentation
✓ .ansible-lint - Lint configuration
✓ molecule/ - Testing framework (optional)
```

### Next Steps

**1. Configure Inventory**:
```bash
# Edit inventory file
vi inventory/development/hosts.yml

# Add your hosts and variables
```

**2. Setup Vault**:
```bash
# Create vault password
echo "your-password" > .vault-pass
chmod 600 .vault-pass

# Create encrypted secrets
ansible-vault create inventory/development/group_vars/all/vault.yml
```

**3. Install Dependencies**:
```bash
ansible-galaxy install -r requirements.yml
pip install -r requirements.txt
```

**4. Test Connection**:
```bash
ansible all -i inventory/development -m ping
```

**5. Develop Roles**:
```bash
# Create new role
ansible-galaxy role init roles/myapp

# Edit role tasks
vi roles/myapp/tasks/main.yml
```

**6. Run Playbooks**:
```bash
# Check mode first
ansible-playbook playbooks/site.yml -i inventory/development --check

# Execute
ansible-playbook playbooks/site.yml -i inventory/development
```

## Best Practices

**Security**:
- Never commit `.vault-pass` or unencrypted secrets
- Use Ansible Vault for all sensitive data
- Separate vault files per environment
- Rotate vault passwords regularly
- Use SSH keys, not passwords

**Organization**:
- One role per purpose (single responsibility)
- Keep playbooks simple, logic in roles
- Use tags for selective execution
- Group related tasks in separate files
- Follow ansible-galaxy role structure

**Testing**:
- Always run in check mode first (`--check`)
- Test in development before production
- Use ansible-lint for code quality
- Implement Molecule tests for roles
- Use CI/CD for automated testing

**Version Control**:
- Commit early and often
- Use meaningful commit messages
- Tag releases (v1.0.0, v1.1.0, etc.)
- Branch for features/fixes
- Review changes before production

**Documentation**:
- README in every role
- Document all variables
- Include example playbooks
- Keep documentation up to date
- Explain non-obvious decisions
