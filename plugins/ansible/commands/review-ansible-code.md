---
description: Review Ansible code for quality and best practices
argument-hint: Optional code to review
---

# Ansible Code Review

You are reviewing Ansible code for errors, best practices, idempotency, and maintainability using the ansible-code-reviewer agent.

## Workflow

### 1. Identify Review Scope

Determine what needs review:
- **Single role**: Specific role directory
- **Multiple roles**: All roles in project
- **Playbook**: Playbook file(s)
- **Tasks file**: Specific tasks/main.yml
- **Entire project**: All Ansible code

### 2. Gather Code and Context

If not specified, ask for:
- **Code location**:
  - File paths or directories
  - Or paste code directly
- **Code type**:
  - Role, playbook, tasks, handlers
- **Target environment**:
  - Development, staging, production
  - Criticality level
- **Specific concerns**:
  - Idempotency issues
  - Performance problems
  - Module selection
  - Code organization
- **Constraints**:
  - Required Ansible version
  - Target OS/distributions
  - Must-use modules

### 3. Pre-Review Analysis

Before launching agent, check:
- Code is accessible (file paths exist)
- Syntax is valid (won't fail parsing)
- Complete context provided

Run basic checks:
```bash
# Syntax validation
ansible-playbook playbook.yml --syntax-check

# Basic lint
ansible-lint roles/ --parseable
```

### 4. Launch Code Reviewer

Launch **ansible-code-reviewer** with:
```
"Review Ansible code at [location].
Type: [role/playbook/tasks]
Environment: [dev/staging/production]
Focus areas: [concerns if any]

Check for:
- Idempotency violations
- Module selection issues
- Task naming quality
- Variable management
- Handler usage
- Error handling
- Check mode support
- Performance issues
- Code organization"
```

### 5. Analyze Review Findings

The agent will categorize issues by severity:

**Critical** (Block deployment):
- Syntax errors preventing execution
- Idempotency violations causing data loss
- Destructive operations without safeguards
- Tasks that always fail
- Hardcoded credentials (security)

**High** (Fix before deployment):
- Non-idempotent operations
- Shell/command when module available
- Missing validation on critical changes
- No backup before destructive changes
- Improper handler usage
- Missing error handling

**Medium** (Address soon):
- Suboptimal code organization
- Poor task naming
- Generic variable names
- Missing documentation
- Performance inefficiencies
- Missing tags

**Low** (Best practice improvements):
- Style inconsistencies
- Could use better Ansible features
- Documentation could be more detailed
- Minor optimizations

### 6. Prioritize Remediation

Create action plan:

**Immediate** (Before any deployment):
1. Fix all Critical issues
2. Validate fixes with testing

**Short-term** (This sprint):
1. Fix High priority issues
2. Re-review after changes

**Medium-term** (Next sprint):
1. Address Medium issues
2. Improve documentation

**Long-term** (Backlog):
1. Low priority improvements
2. Refactoring for maintainability

## Review Categories

### Idempotency Check

Agent checks that tasks:
- Use proper state management modules
- Don't use shell/command without `creates`/`removes`
- Have `changed_when` set appropriately
- Can run multiple times safely
- Don't have uncontrolled side effects

**Example Issues**:
```yaml
# BAD - Not idempotent
- name: Install nginx
  ansible.builtin.shell: apt-get install -y nginx

# GOOD - Idempotent
- name: Ensure nginx is installed
  ansible.builtin.package:
    name: nginx
    state: present
```

### Module Selection

Agent verifies:
- Dedicated modules used over shell/command
- Correct module for each task
- Module parameters used properly
- No deprecated modules

**Example Issues**:
```yaml
# BAD - Shell for directory creation
- ansible.builtin.shell: mkdir -p /var/app

# GOOD - File module
- ansible.builtin.file:
    path: /var/app
    state: directory
```

### Task Naming

Agent checks:
- Names start with verbs
- Names are descriptive
- Consistent sentence case
- No generic names

**Example Issues**:
```yaml
# BAD
- name: nginx
- name: task1
- name: copy file

# GOOD
- name: Ensure Nginx is installed
- name: Configure Nginx virtual host
- name: Copy application configuration to server
```

### Variable Usage

Agent reviews:
- Variable namespacing (no conflicts)
- Defined defaults
- No hardcoded values
- Proper precedence understanding

**Example Issues**:
```yaml
# BAD - Generic names, hardcoded
port: 80
user: webapp

# GOOD - Namespaced, from defaults
nginx_port: "{{ nginx_default_port | default(80) }}"
nginx_user: "{{ nginx_service_user | default('www-data') }}"
```

### Handler Review

Agent checks:
- Handlers for service restarts
- Not direct task restarts
- Handler names clear
- Proper notify usage

**Example Issues**:
```yaml
# BAD - Direct restart
- name: Update config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf

- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted

# GOOD - Using handler
- name: Update configuration
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Reload nginx
```

### Error Handling

Agent verifies:
- Block/rescue for critical operations
- Validation before destructive changes
- Backup before modifications
- Rollback mechanisms

**Example Issues**:
```yaml
# BAD - No error handling
- name: Deploy new version
  ansible.builtin.copy:
    src: app.jar
    dest: /opt/app/app.jar

# GOOD - With rollback
- name: Deploy with rollback
  block:
    - name: Backup current version
      ansible.builtin.copy:
        src: /opt/app/app.jar
        dest: /opt/app/app.jar.backup
        remote_src: yes

    - name: Deploy new version
      ansible.builtin.copy:
        src: app.jar
        dest: /opt/app/app.jar

    - name: Restart service
      ansible.builtin.service:
        name: myapp
        state: restarted

  rescue:
    - name: Restore backup
      ansible.builtin.copy:
        src: /opt/app/app.jar.backup
        dest: /opt/app/app.jar
        remote_src: yes

    - name: Restart with old version
      ansible.builtin.service:
        name: myapp
        state: restarted
```

## Output Format

### Code Review Report

**Summary**:
- Overall Quality: [Excellent/Good/Needs Improvement/Poor]
- Critical Issues: [count]
- High Priority: [count]
- Medium Priority: [count]
- Low Priority: [count]
- Idempotency Status: [✓ Pass / ✗ Fail]

**Critical Findings**:
```
[CRITICAL] Idempotency: Non-idempotent shell command
Location: roles/nginx/tasks/main.yml:15
Issue: Using shell to install package
Impact: Task reports changed every run
Fix: Use ansible.builtin.package module
```

**Recommendations**:
1. Fix critical idempotency issues immediately
2. Replace shell/command with proper modules
3. Add error handling to deployment tasks
4. Improve task naming consistency
5. Add validation before config changes

### Validation Commands

After fixing issues:
```bash
# Syntax check
ansible-playbook playbook.yml --syntax-check

# Lint with fixes
ansible-lint roles/ --fix

# Check mode to verify idempotency
ansible-playbook playbook.yml --check
ansible-playbook playbook.yml --check  # Run twice

# Test in development
ansible-playbook playbook.yml -i inventory/dev -vv
```

## Best Practices

**Pre-Review**:
- Run syntax check first
- Run ansible-lint locally
- Fix obvious issues before agent review

**During Review**:
- Provide complete context
- Specify environment criticality
- Note any constraints or requirements
- Ask about specific concerns

**Post-Review**:
- Fix Critical and High issues first
- Test fixes in development
- Re-review after significant changes
- Document rationale for any exceptions

**Continuous Improvement**:
- Integrate ansible-lint in CI/CD
- Regular code reviews before production
- Keep roles modular and focused
- Maintain consistent coding standards
- Document patterns and decisions
