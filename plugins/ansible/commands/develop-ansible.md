---
description: Develop Ansible roles and playbooks
argument-hint: Optional role or playbook requirements
---

# Ansible Development

You are developing Ansible automation (roles, playbooks, tasks, handlers) using the ansible-developer agent.

## Workflow

### 1. Identify Development Type

Determine what to develop:
- **New role**: Complete role with tasks, handlers, defaults
- **New playbook**: Standalone playbook or site playbook
- **Task enhancement**: Add/modify tasks in existing role
- **Handler creation**: Create service handlers
- **Variable management**: Define defaults and vars
- **Module development**: Custom Ansible modules

### 2. Gather Requirements

If not specified, ask for:

**For Roles**:
- Role name and purpose
- Target systems (OS, distributions)
- Dependencies (packages, services, other roles)
- Configuration files needed
- Variables and their defaults
- Handlers required (restarts, reloads)
- Tags for selective execution

**For Playbooks**:
- Playbook purpose (deploy, configure, maintain)
- Target host groups
- Roles to include
- Pre-tasks and post-tasks
- Variables needed
- Execution order

**For Tasks**:
- What the task should accomplish
- Modules to use (package, service, template, etc.)
- Idempotency requirements
- Error handling needs
- Conditionals (OS-specific, etc.)

### 3. Launch Development Agent

Launch **ansible-developer** with complete requirements:

```
"Create Ansible role named [role-name] that [purpose].
Target systems: [OS/distro]
Requirements:
- Install and configure [software]
- Manage [service] with handlers
- Template [config-files]
- Variables: [list with defaults]
- Support check mode
- Include example playbook"
```

### 4. Review Generated Code

Check the developed automation for:
- **Idempotency**: All tasks can run multiple times safely
- **Module usage**: Proper Ansible modules (not shell when module exists)
- **Task naming**: Descriptive names starting with verbs
- **Variables**: Proper namespacing (role_name_variable)
- **Handlers**: Appropriate handler usage
- **Error handling**: Block/rescue where needed
- **Check mode**: Support for `--check` flag
- **Tags**: Meaningful tags for selective execution

### 5. Test Development

Run validation commands:

```bash
# Syntax check
ansible-playbook playbooks/test.yml --syntax-check

# Lint check
ansible-lint roles/[role-name]/

# Check mode (dry run)
ansible-playbook playbooks/test.yml --check

# Run on test environment
ansible-playbook playbooks/test.yml -i inventory/development

# Run specific tags
ansible-playbook playbooks/test.yml --tags "install,config"

# Verbose output for debugging
ansible-playbook playbooks/test.yml -vv
```

### 6. Iterate if Needed

If issues found:
- Launch ansible-code-reviewer to identify problems
- Fix issues with ansible-developer
- Re-test until all checks pass

## Common Development Patterns

### Creating a Role

**Example: Web Server Role**

```yaml
# roles/nginx/tasks/main.yml
---
- name: Ensure Nginx is installed
  ansible.builtin.package:
    name: nginx
    state: present
  tags: install

- name: Ensure Nginx configuration directory exists
  ansible.builtin.file:
    path: /etc/nginx/sites-available
    state: directory
    owner: root
    group: root
    mode: '0755'
  tags: config

- name: Template Nginx main configuration
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    owner: root
    group: root
    mode: '0644'
    validate: nginx -t -c %s
    backup: yes
  notify: Reload nginx
  tags: config

- name: Ensure Nginx is started and enabled
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: yes
  tags: service
```

**Handlers**:
```yaml
# roles/nginx/handlers/main.yml
---
- name: Reload nginx
  ansible.builtin.service:
    name: nginx
    state: reloaded

- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```

**Defaults**:
```yaml
# roles/nginx/defaults/main.yml
---
nginx_worker_processes: auto
nginx_worker_connections: 1024
nginx_user: www-data
nginx_keepalive_timeout: 65
nginx_server_names_hash_bucket_size: 64
```

### Creating a Playbook

**Example: Site Deployment Playbook**

```yaml
---
- name: Deploy web application
  hosts: webservers
  become: yes
  gather_facts: yes

  vars:
    app_version: "1.0.0"
    deployment_user: deploy

  pre_tasks:
    - name: Update package cache
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"
      tags: always

    - name: Verify deployment user exists
      ansible.builtin.user:
        name: "{{ deployment_user }}"
        state: present
      tags: always

  roles:
    - role: common
      tags: common
    - role: nginx
      tags: nginx
    - role: application
      tags: app

  tasks:
    - name: Deploy application version {{ app_version }}
      ansible.builtin.copy:
        src: "app-{{ app_version }}.tar.gz"
        dest: "/opt/app/"
        owner: "{{ deployment_user }}"
        group: "{{ deployment_user }}"
        mode: '0644'
      tags: deploy

    - name: Extract application
      ansible.builtin.unarchive:
        src: "/opt/app/app-{{ app_version }}.tar.gz"
        dest: "/opt/app/"
        remote_src: yes
        owner: "{{ deployment_user }}"
        group: "{{ deployment_user }}"
      tags: deploy

  post_tasks:
    - name: Verify application is accessible
      ansible.builtin.uri:
        url: "http://{{ ansible_default_ipv4.address }}/"
        status_code: 200
      tags: verify

    - name: Log deployment
      ansible.builtin.lineinfile:
        path: /var/log/deployments.log
        line: "{{ ansible_date_time.iso8601 }} - Deployed {{ app_version }}"
        create: yes
      tags: always
```

## Output Format

### Development Summary

**Created**: [role-name / playbook-name]
**Purpose**: [description]
**Files Generated**:
```
✓ tasks/main.yml - Main task list
✓ handlers/main.yml - Service handlers
✓ defaults/main.yml - Default variables
✓ templates/ - Configuration templates
✓ README.md - Role documentation
```

### Usage Instructions

**Install role**:
```bash
# If using Galaxy
ansible-galaxy install [role-name]

# Local role (no action needed)
```

**Run playbook**:
```bash
# Syntax check
ansible-playbook playbook.yml --syntax-check

# Dry run
ansible-playbook playbook.yml --check

# Execute
ansible-playbook playbook.yml -i inventory/production

# Specific tags
ansible-playbook playbook.yml --tags "install,config"

# Limit to hosts
ansible-playbook playbook.yml --limit "web01,web02"
```

### Testing Commands

```bash
# Lint check
ansible-lint roles/[role-name]/

# Test with Molecule
cd roles/[role-name]
molecule test

# Manual test
ansible-playbook tests/test.yml -i tests/inventory
```

## Best Practices

**Idempotency**:
- Use proper Ansible modules (not shell/command when module exists)
- Always set `changed_when` for command/shell tasks
- Use state management (present/absent, started/stopped)

**Task Naming**:
- Start with verb (Ensure, Configure, Install, Copy, etc.)
- Be specific about what task does
- Use sentence case consistently

**Variables**:
- Namespace with role name: `nginx_port`, `app_user`
- Provide sensible defaults in `defaults/main.yml`
- Document all variables in README

**Error Handling**:
- Use `failed_when` to define failure conditions
- Use `block/rescue` for rollback capability
- Validate before destructive operations

**Templates**:
- Add `{{ ansible_managed }}` header
- Use validate parameter when possible
- Always backup with `backup: yes`

**Handlers**:
- One handler per service action
- Use notify instead of direct service restarts
- Name handlers clearly (Restart nginx, Reload postgresql)

**Tags**:
- Add meaningful tags to tasks
- Use common tags (install, config, service)
- Allow selective execution

**Testing**:
- Always support check mode
- Test on multiple OS if targeting multiple platforms
- Use Molecule for automated testing
- Include example playbook in role
