---
description: Complete code quality and security review
argument-hint: Optional code to review
---

# Complete Ansible Review

You are conducting a comprehensive Ansible review including both code quality and security using the ansible-orchestrator agent to coordinate multiple reviewer agents.

## Workflow

### 1. Identify Review Scope

Determine what needs comprehensive review:
- **New production code**: Before initial production deployment
- **Major changes**: Significant refactoring or new features
- **Pre-release**: Before tagging a release
- **Compliance audit**: Regulatory compliance check
- **Security incident**: Post-incident code review
- **Regular audit**: Scheduled quarterly review

### 2. Gather Code and Context

If not specified, ask for:
- **Code to review**:
  - File paths or directories
  - Role names
  - Entire project
  - Git commit range (e.g., main..feature-branch)
- **Code type**:
  - Roles, playbooks, collections
  - Inventory and variables
  - Custom modules or plugins
- **Environment details**:
  - Target environment (production/staging/development)
  - Criticality level (mission-critical/important/standard)
  - Number of managed hosts
  - Infrastructure type (bare metal/VMs/cloud/containers)
- **Compliance requirements**:
  - PCI-DSS, HIPAA, GDPR, SOC 2
  - CIS Benchmarks
  - Company-specific policies
  - Industry standards
- **Specific concerns**:
  - Known issues to verify
  - Areas of technical debt
  - Recent changes to review
  - Performance bottlenecks
- **Review depth**:
  - Quick review (high/critical only)
  - Standard review (high/medium)
  - Comprehensive review (all findings)
  - Compliance audit (focus on compliance)

### 3. Pre-Review Preparation

Run automated scans before launching agents:

```bash
# Syntax validation
find roles/ playbooks/ -name "*.yml" -exec ansible-playbook {} --syntax-check \;

# ansible-lint (all profiles)
ansible-lint --profile production roles/
ansible-lint --profile security roles/

# Secret scanning
trufflehog filesystem . --only-verified
git secrets --scan

# Check vault files encrypted
find . -name "vault*.yml" -exec file {} \; | grep -v "data"

# Complexity analysis
find roles/ -name "*.yml" -exec wc -l {} \; | sort -rn | head -20

# Inventory verification
ansible-inventory -i inventory/production --graph
ansible-inventory -i inventory/production --list
```

### 4. Launch Orchestrator

Launch **ansible-orchestrator** to coordinate both reviews:

```
"Conduct comprehensive review of Ansible code at [location].

**Code Context**:
- Type: [roles/playbooks/collection]
- Environment: [production/staging/development]
- Criticality: [mission-critical/important/standard]
- Target infrastructure: [description]

**Compliance Requirements**:
- [List: PCI-DSS, HIPAA, GDPR, SOC 2, etc.]
- [Company-specific policies]

**Review Scope**:
- Code quality and best practices (ansible-code-reviewer)
- Security vulnerabilities and compliance (ansible-security-reviewer)
- Both reviews should run in parallel

**Focus Areas**:
- [List specific concerns if any]
- Known issues: [list if any]
- Recent changes: [git commit range if applicable]

**Review Depth**: [Quick/Standard/Comprehensive/Compliance]

Coordinate both reviews and synthesize findings into:
1. Executive summary
2. Combined findings by severity
3. Prioritized action plan
4. Compliance status per requirement"
```

### 5. Orchestrator Review Process

The orchestrator will:

**Phase 1: Parallel Reviews**
- Launch **ansible-code-reviewer** for quality review
- Launch **ansible-security-reviewer** for security review
- Both agents work in parallel

**Phase 2: Analysis**
- Collect findings from both agents
- Identify overlapping issues
- Cross-reference findings
- Categorize by severity

**Phase 3: Synthesis**
- Create unified findings list
- Prioritize by severity and impact
- Generate compliance matrix
- Create remediation roadmap

### 6. Analyze Combined Findings

Review categorizes all findings by severity:

**Critical** (Block all deployment):
- **Code**: Syntax errors, idempotency violations causing data loss
- **Security**: Hardcoded credentials, command injection, exposed secrets
- **Compliance**: Violations of mandatory requirements
- **Impact**: Production outages, data breaches, compliance penalties

**High** (Fix before production):
- **Code**: Non-idempotent operations, improper module usage
- **Security**: Missing encryption, excessive privileges, weak permissions
- **Compliance**: Violations of important requirements
- **Impact**: Reliability issues, security risks, audit findings

**Medium** (Address within sprint):
- **Code**: Poor organization, suboptimal performance
- **Security**: Missing audit logs, unpinned versions
- **Compliance**: Best practice violations
- **Impact**: Technical debt, maintainability issues

**Low** (Best practice improvements):
- **Code**: Style inconsistencies, documentation gaps
- **Security**: Could use stricter controls
- **Compliance**: Documentation improvements
- **Impact**: Code quality, team efficiency

### 7. Compliance Assessment

Generate compliance matrix:

```
Requirement | Status | Findings | Priority
------------|--------|----------|----------
PCI-DSS 3.4 | FAIL   | Secrets in logs | Critical
PCI-DSS 7.1 | PASS   | Access controls OK | -
PCI-DSS 8.2 | FAIL   | Weak passwords | High
HIPAA §164  | FAIL   | PHI in logs | Critical
GDPR Art 32 | PASS   | Encryption OK | -
SOC 2 CC6.1 | FAIL   | No MFA | High
```

### 8. Create Remediation Plan

Orchestrator creates prioritized action plan:

**Immediate (0-24 hours)** - Block deployment:
1. [Critical Code Issue] - Location, Impact, Fix
2. [Critical Security Issue] - Location, Impact, Fix
3. [Critical Compliance Issue] - Location, Impact, Fix

**Short-term (1-7 days)** - Before production:
1. [High Priority Issues] - Categorized and prioritized
2. [High Security Issues]
3. [High Compliance Issues]

**Medium-term (1-4 weeks)** - Address in sprint:
1. [Medium Priority Issues] - Grouped by area
2. [Technical debt items]
3. [Performance improvements]

**Long-term (1-3 months)** - Continuous improvement:
1. [Low priority improvements]
2. [Architecture improvements]
3. [Documentation and training]
4. [Automation and tooling]

## Output Format

### Comprehensive Review Report

#### Executive Summary

**Overall Assessment**:
- **Code Quality**: [Excellent/Good/Needs Improvement/Poor]
- **Security Posture**: [Strong/Adequate/Weak/Critical]
- **Compliance Status**: [Compliant/Mostly Compliant/Non-Compliant]
- **Deployment Recommendation**: [✓ Approve / ⚠ Conditional / ✗ Block]

**Findings Summary**:
- Critical Issues: [count] - MUST FIX BEFORE DEPLOYMENT
- High Priority: [count] - Fix before production
- Medium Priority: [count] - Address in sprint
- Low Priority: [count] - Continuous improvement

**Key Risks Identified**:
1. [Most critical risk with impact]
2. [Second critical risk]
3. [Third critical risk]

**Recommendations**:
1. [Primary recommendation]
2. [Secondary recommendation]
3. [Tertiary recommendation]

#### Combined Findings by Severity

**[CRITICAL] Findings**

**Code Quality**:
```
[CRITICAL] Idempotency: Non-idempotent database operation
Location: roles/database/tasks/restore.yml:42
Issue: Shell command modifies database without idempotency check
Impact: Data loss on repeated runs
Fix: Use database module with state management
```

**Security**:
```
[CRITICAL] Credential Management: Hardcoded API Key
Location: roles/api/defaults/main.yml:15
Issue: API key in plain text in version control
Impact: Exposed credentials, PCI-DSS violation
Fix: Remove from code, use Ansible Vault
```

**[HIGH] Findings**

[Similar format for High priority findings]

**[MEDIUM] Findings**

[Similar format for Medium priority findings]

**[LOW] Findings**

[Similar format for Low priority findings]

#### Compliance Assessment Matrix

**PCI-DSS Compliance**:
- ✗ Requirement 3.4: Cardholder data in logs (FAIL - Critical)
- ✓ Requirement 4.1: Encrypted transmission (PASS)
- ✗ Requirement 7.1: Access controls insufficient (FAIL - High)
- ✗ Requirement 8.2: Weak password policy (FAIL - High)
- ✓ Requirement 10.1: Audit trails present (PASS)

**Overall PCI-DSS**: ✗ NON-COMPLIANT (3 failures)

**HIPAA Compliance**:
- ✗ §164.312(a)(1): PHI in logs (FAIL - Critical)
- ✓ §164.312(a)(2): Encryption (PASS)
- ✗ §164.312(d): Audit controls missing (FAIL - High)

**Overall HIPAA**: ✗ NON-COMPLIANT (2 failures)

#### Remediation Roadmap

**Phase 1: Immediate (0-24 hours)**

BLOCK DEPLOYMENT until fixed:

1. **[Code] Fix idempotency violation**
   - Location: roles/database/tasks/restore.yml:42
   - Action: Replace shell with postgresql_db module
   - Owner: [Developer name]
   - Verification: Run twice in check mode, both should be idempotent

2. **[Security] Remove hardcoded API key**
   - Location: roles/api/defaults/main.yml:15
   - Action: Remove from code, add to vault, use {{ vault_api_key }}
   - Owner: [Security team]
   - Verification: Git history scan, no secrets found

3. **[Security] Fix PHI in logs**
   - Location: roles/patient/tasks/main.yml:*
   - Action: Add no_log: true to all PHI-handling tasks
   - Owner: [Compliance team]
   - Verification: Test run, no PHI in logs

**Phase 2: Short-term (1-7 days)**

Required before production:

1. Fix all High priority code issues (5 issues)
2. Implement proper access controls
3. Strengthen password policy
4. Add audit logging
5. Re-review after fixes

**Phase 3: Medium-term (1-4 weeks)**

During sprint:

1. Refactor complex roles (technical debt)
2. Improve test coverage
3. Update documentation
4. Performance optimization

**Phase 4: Long-term (1-3 months)**

Continuous improvement:

1. Migrate to external secret management
2. Implement automated security scanning
3. Regular compliance audits
4. Team training on secure coding

#### Testing and Validation

**After Critical Fixes**:
```bash
# Re-run automated checks
ansible-lint --profile production roles/
ansible-lint --profile security roles/
trufflehog filesystem . --only-verified

# Verify idempotency
ansible-playbook playbook.yml --check
ansible-playbook playbook.yml --check  # Run twice

# Security validation
git log -p | grep -i "password\|secret\|key"  # Should find nothing
find . -name "vault*.yml" -exec file {} \;  # Should show "data"

# Test in staging
ansible-playbook playbook.yml -i inventory/staging -vv
```

**Before Production Deployment**:
```bash
# Full test suite
ansible-playbook playbook.yml --syntax-check
ansible-lint --profile production roles/
ansible-playbook playbook.yml -i inventory/staging
molecule test  # For role testing

# Re-review
# Launch full review again to verify all fixes
```

#### Deployment Decision

**Current Status**: [✓ Approved / ⚠ Conditional / ✗ BLOCKED]

**Conditions for Approval** (if conditional):
- [ ] Fix all Critical issues
- [ ] Fix High priority security issues
- [ ] Address compliance violations
- [ ] Pass automated testing
- [ ] Re-review completed and passed

**Blocking Issues** (if blocked):
1. [Issue preventing deployment]
2. [Issue preventing deployment]
3. [Issue preventing deployment]

**Sign-off Requirements**:
- [ ] Code quality review passed
- [ ] Security review passed
- [ ] Compliance requirements met
- [ ] Testing completed successfully
- [ ] Deployment runbook prepared
- [ ] Rollback plan documented

## Best Practices

**Pre-Review**:
- Run all automated scans first
- Fix obvious issues before agent review
- Prepare context and requirements
- Identify stakeholders for sign-off

**During Review**:
- Provide complete context to orchestrator
- Specify all compliance requirements
- Note any constraints or special concerns
- Allow parallel reviews to complete

**Post-Review**:
- Address Critical issues immediately
- Plan High priority fixes before production
- Document decisions and rationale
- Schedule re-review after fixes

**Continuous Improvement**:
- Integrate reviews in CI/CD pipeline
- Regular scheduled reviews (quarterly)
- Track metrics (issues found, time to fix)
- Team training based on findings
- Update standards based on lessons learned

**Sign-off Process**:
- Development lead: Code quality
- Security team: Security review
- Compliance officer: Compliance requirements
- Operations: Deployment readiness
- All stakeholders before production deployment
