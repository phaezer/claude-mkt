---
description: Security review of Ansible code
argument-hint: Optional code to review
---

# Ansible Security Review

You are conducting a security review of Ansible code for vulnerabilities, credential handling, privilege escalation, and compliance using the ansible-security-reviewer agent.

## Workflow

### 1. Identify Security Review Scope

Determine what requires security review:
- **New code**: Before initial deployment
- **Existing code**: Regular security audits
- **Production code**: Before production deployment
- **High-risk operations**: Privileged access, credential handling
- **Compliance check**: PCI-DSS, HIPAA, GDPR, SOC2

### 2. Gather Code and Requirements

If not specified, ask for:
- **Code to review**:
  - File paths or directories
  - Or paste code directly
- **Environment criticality**:
  - Production (strict standards)
  - Staging (moderate standards)
  - Development (baseline standards)
- **Compliance requirements**:
  - PCI-DSS (payment card data)
  - HIPAA (healthcare)
  - GDPR (EU data privacy)
  - SOC 2 (security controls)
  - CIS Benchmarks
- **Specific security concerns**:
  - Credential management
  - Privilege escalation
  - File permissions
  - Network security
  - Supply chain security
- **Risk tolerance**:
  - Zero tolerance (financial, healthcare)
  - Moderate (enterprise)
  - Standard (general business)

### 3. Pre-Security Scan

Run automated security scans first:

```bash
# Check for hardcoded secrets
trufflehog filesystem . --only-verified

# Git secrets scan
git secrets --scan

# ansible-lint security profile
ansible-lint --profile security roles/

# Check vault files are encrypted
find . -name "vault*.yml" -exec ansible-vault view {} \;
```

### 4. Launch Security Reviewer

Launch **ansible-security-reviewer** with:
```
"Perform security review of Ansible code at [location].
Environment: [production/staging/development]
Compliance: [requirements if any]
Risk tolerance: [zero/moderate/standard]

Focus on:
- Hardcoded credentials and secrets
- Ansible Vault usage
- Privilege escalation (become/sudo)
- File and directory permissions
- Command injection risks
- Template injection risks
- Network security (HTTPS, certs)
- Logging secrets (no_log)
- Supply chain security
- Audit and compliance"
```

### 5. Analyze Security Findings

Agent categorizes by severity:

**Critical** (Block all deployment):
- Hardcoded passwords, API keys, tokens
- Credentials in unencrypted files
- Credentials committed to Git
- Missing Ansible Vault
- World-readable sensitive files
- Command injection vulnerabilities
- Privileged containers without justification

**High** (Fix before production):
- Secrets in logs (missing `no_log`)
- Excessive privilege escalation
- Weak file permissions on sensitive files
- Disabled certificate validation
- No backup before destructive operations
- Missing input validation
- Unencrypted network protocols

**Medium** (Address within sprint):
- Generic variable names (potential conflicts)
- No audit logging
- Supply chain: unpinned versions
- Missing security headers
- Verbose logging in production
- No secrets rotation plan

**Low** (Best practice improvements):
- Could use stricter permissions
- Documentation of security decisions
- Security testing gaps
- Monitoring improvements

### 6. Remediation Planning

Create security fix plan:

**Immediate** (0-24 hours):
1. Remove all hardcoded credentials
2. Encrypt with Ansible Vault
3. Fix critical file permissions
4. Add `no_log` to sensitive tasks

**Short-term** (1-7 days):
1. Implement external secret management
2. Reduce privilege escalation
3. Fix injection vulnerabilities
4. Enable certificate validation
5. Add audit logging

**Long-term** (1-3 months):
1. Migrate to HashiCorp Vault / AWS Secrets Manager
2. Implement secret rotation
3. Security scanning in CI/CD
4. Regular security audits

## Security Review Categories

### Credential Management

Agent scans for:
- Hardcoded passwords
- API keys in plain text
- SSH keys in code
- Database credentials
- Certificates and private keys
- Unencrypted vault files

**Critical Issues**:
```yaml
# CRITICAL - Hardcoded password
- name: Create database user
  postgresql_user:
    name: appuser
    password: "SuperSecret123!"  # EXPOSED!

# FIX - Use Vault
- name: Create database user
  postgresql_user:
    name: appuser
    password: "{{ vault_db_password }}"
  no_log: true
```

### Privilege Escalation

Agent checks:
- Unnecessary root/sudo usage
- Overly broad sudo permissions
- become_user validation
- Principle of least privilege

**Issues**:
```yaml
# BAD - Unnecessary root
- name: Create user file
  become: yes
  become_user: root
  ansible.builtin.copy:
    src: user_config
    dest: /home/appuser/.config

# GOOD - Run as user
- name: Create user file
  become: yes
  become_user: appuser
  ansible.builtin.copy:
    src: user_config
    dest: /home/appuser/.config
```

### File Permissions

Agent verifies:
- Sensitive files not world-readable
- Directories properly restricted
- SSH config and keys secured
- Vault files protected

**Critical Issues**:
```yaml
# CRITICAL - World readable
- name: Create SSH private key
  ansible.builtin.copy:
    src: id_rsa
    dest: /home/user/.ssh/id_rsa
    # Missing mode! Defaults to 0644

# FIX - Proper permissions
- name: Create SSH private key
  ansible.builtin.copy:
    src: id_rsa
    dest: /home/user/.ssh/id_rsa
    owner: user
    group: user
    mode: '0400'  # Read-only by owner
```

### Command Injection

Agent identifies:
- Unsanitized variables in shell
- SQL injection in database commands
- Unquoted variables in commands

**Critical Issues**:
```yaml
# CRITICAL - Command injection
- name: Process user input
  ansible.builtin.shell: echo "{{ user_input }}" > /tmp/file
  # user_input could be: "; rm -rf / #"

# FIX - Use copy module
- name: Write user input safely
  ansible.builtin.copy:
    content: "{{ user_input }}"
    dest: /tmp/file
```

### Secrets in Logs

Agent checks for:
- Missing `no_log` on sensitive tasks
- Debug statements with secrets
- Verbose logging of credentials

**Issues**:
```yaml
# BAD - Password in logs
- name: Set database password
  ansible.builtin.lineinfile:
    path: /etc/app/config.ini
    line: "password={{ db_password }}"
  # Password appears in logs!

# GOOD - no_log enabled
- name: Set database password
  ansible.builtin.lineinfile:
    path: /etc/app/config.ini
    line: "password={{ db_password }}"
  no_log: true
```

### Network Security

Agent reviews:
- HTTP vs HTTPS
- Certificate validation
- Secure protocols
- Exposed services

**Issues**:
```yaml
# BAD - Disabled cert validation
- name: Download file
  ansible.builtin.get_url:
    url: https://example.com/file
    dest: /tmp/file
    validate_certs: no  # SECURITY RISK!

# GOOD - Cert validation enabled
- name: Download file
  ansible.builtin.get_url:
    url: https://example.com/file
    dest: /tmp/file
    validate_certs: yes
```

## Output Format

### Security Review Report

**Executive Summary**:
- Overall Security Posture: [Critical Risk/High Risk/Medium Risk/Low Risk]
- Critical Findings: [count] - MUST FIX IMMEDIATELY
- High Findings: [count] - Fix before production
- Medium Findings: [count] - Address within sprint
- Low Findings: [count] - Best practice improvements
- Compliance Status: [Pass/Fail per requirement]

**Critical Security Findings**:
```
[CRITICAL] Credential Management: Hardcoded Password
Location: roles/database/tasks/main.yml:25
Vulnerability: Database password in plain text
Evidence:
    password: "SuperSecret123!"
Risk:
  - Credentials exposed in version control
  - No ability to rotate passwords
  - Violates PCI-DSS 8.2.1, GDPR Article 32
Recommendation:
  1. Remove hardcoded password
  2. Encrypt with: ansible-vault encrypt_string 'password' --name 'vault_db_password'
  3. Use: password: "{{ vault_db_password }}"
  4. Add: no_log: true
```

**Security Checklist**:
- [ ] No hardcoded credentials
- [ ] Ansible Vault for all secrets
- [ ] `no_log` on sensitive tasks
- [ ] Minimal privilege escalation
- [ ] Secure file permissions (0600/0400 for secrets)
- [ ] No command injection vectors
- [ ] HTTPS with certificate validation
- [ ] Audit logging enabled
- [ ] Supply chain: pinned versions
- [ ] Backup before destructive operations

**Remediation Priority**:

**Immediate (0-24 hours)**:
1. Remove hardcoded password at roles/database/tasks/main.yml:25
2. Fix world-readable SSH key at roles/deploy/files/id_rsa
3. Add no_log to vault password task at roles/vault/tasks/main.yml:10

**Short-term (1-7 days)**:
1. Implement Ansible Vault for all secrets
2. Reduce unnecessary sudo/root usage
3. Enable certificate validation
4. Add audit logging

**Long-term (1-3 months)**:
1. Migrate to HashiCorp Vault
2. Implement secrets rotation
3. Security CI/CD integration
4. Regular penetration testing

### Validation Commands

After fixing security issues:

```bash
# Scan for secrets
trufflehog filesystem . --only-verified

# Verify vault encryption
find . -name "vault*.yml" -exec file {} \;
# Should show "data" not "ASCII text"

# Security lint
ansible-lint --profile security roles/

# Verify no secrets in git history
git log -p | grep -i "password\|secret\|key"

# Test with production-like settings
ansible-playbook playbook.yml --check -i inventory/production
```

## Compliance Frameworks

### PCI-DSS Requirements
- No cardholder data in logs (Req 3.4)
- Strong access controls (Req 7)
- Encrypted transmission (Req 4)
- Audit trails (Req 10)

### HIPAA Requirements
- PHI encryption at rest and transit
- Access controls and audit logging
- Regular security assessments
- Breach notification procedures

### GDPR Requirements
- Data encryption (Article 32)
- Access controls (Article 32)
- Audit logging (Article 30)
- Data minimization (Article 5)

### SOC 2 Requirements
- Access controls (CC6.1)
- Encryption (CC6.7)
- Change management (CC8.1)
- Monitoring and logging (CC7.2)

## Best Practices

**Pre-Review**:
- Run automated secret scans
- Check all vault files encrypted
- Review git history for leaked secrets
- Document security requirements

**During Review**:
- Specify compliance requirements
- Note environment criticality
- Identify high-risk operations
- Provide complete context

**Post-Review**:
- Fix Critical issues immediately
- Never deploy with Critical findings
- Document security decisions
- Re-review after changes

**Continuous Security**:
- Integrate security scanning in CI/CD
- Regular security audits (quarterly)
- Rotate secrets regularly
- Monitor security advisories
- Keep Ansible and collections updated
- Train team on secure coding practices
