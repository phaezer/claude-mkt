---
description: Develop MCP server with tools, resources, and prompts
argument-hint: Server requirements and features
---

# Develop MCP Server

Orchestrate the complete development of an MCP server from architecture to implementation.

## Usage

```
/develop-mcp-server [requirements]
```

**Arguments:**
- `requirements`: Brief description of what the server should do (optional if discussed interactively)

**Examples:**
```
/develop-mcp-server GitHub integration with issue and PR management
/develop-mcp-server Database query tool for PostgreSQL
/develop-mcp-server File system operations for project management
```

## What This Command Does

This command orchestrates the full MCP server development workflow by:
1. Designing server architecture (tools, resources, prompts)
2. Implementing the server in chosen language
3. Creating comprehensive tests
4. Performing security review
5. Setting up deployment configuration

## Workflow Steps

### Step 1: Gather Requirements

Ask the user to clarify:

1. **Server Purpose**: What problem does this server solve?
2. **Target Users**: Who will use this server?
3. **External Integrations**: What APIs/services does it integrate with?
4. **Required Tools**: What actions should the LLM be able to perform?
5. **Required Resources**: What data should the LLM be able to read?
6. **Required Prompts**: What workflows need templates?
7. **Language Preference**: Python or TypeScript?
8. **Transport**: stdio (Claude Desktop) or SSE (remote access)?

### Step 2: Launch Architecture Agent

Use the Task tool to launch `mcp-server-architect` agent with requirements.

**Agent Task:**
```
Design MCP server architecture for [purpose].

Requirements:
- Tools: [list of tools]
- Resources: [list of resources]
- Prompts: [list of prompts]
- Transport: [stdio/SSE]
- External APIs: [list]

Provide:
1. Complete tools specification with input/output schemas
2. Resources specification with URI patterns
3. Prompts specification with parameters
4. Server structure and organization
5. Configuration requirements
6. Security considerations
```

**Expected Output:**
- Tools specification (name, description, parameters, outputs)
- Resources specification (URI patterns, data sources)
- Prompts specification (templates, arguments)
- Configuration needs (API keys, database URLs, etc.)
- Security requirements

### Step 3: Launch Development Agent

Based on language choice, launch `mcp-python-developer` or `mcp-typescript-developer` agent.

**Agent Task:**
```
Implement MCP server based on architecture:

Architecture:
[paste architecture specification from Step 2]

Language: [Python/TypeScript]

Requirements:
1. Implement all tools with proper error handling
2. Implement all resources with efficient data access
3. Implement all prompts with parameter handling
4. Configure [stdio/SSE] transport
5. Add comprehensive input validation
6. Include proper logging
7. Create configuration management
8. Follow MCP protocol specifications

Deliverables:
- Complete server implementation
- All tools implemented
- All resources implemented
- All prompts implemented
- Configuration setup
- Documentation
```

**Expected Output:**
- Complete server code
- All tools implemented with validation
- All resources with proper access controls
- Transport configured
- Configuration management
- README with usage examples

### Step 4: Launch Testing Agent

Use the Task tool to launch `mcp-testing-engineer` agent.

**Agent Task:**
```
Create comprehensive test suite for MCP server:

Server: [project name]
Language: [Python/TypeScript]

Requirements:
1. Unit tests for all tools
2. Unit tests for all resources
3. Unit tests for all prompts
4. Integration tests with MCP Inspector
5. Error scenario testing
6. Mock external dependencies
7. Coverage > 80%

Deliverables:
- Complete test suite
- pytest/jest configuration
- Mock fixtures
- Integration tests
- Coverage report
- Test documentation
```

**Expected Output:**
- Comprehensive test suite
- Test configuration files
- Mock fixtures for external APIs
- MCP Inspector integration tests
- Coverage report showing >80%

### Step 5: Launch Security Review Agent

Use the Task tool to launch `mcp-security-reviewer` agent.

**Agent Task:**
```
Perform security review of MCP server:

Server: [project name]
Code: [location of server code]

Review for:
1. Input validation vulnerabilities
2. Path traversal issues
3. Command injection risks
4. SQL injection vulnerabilities
5. API key exposure
6. Information disclosure in errors
7. Rate limiting implementation
8. Dependency vulnerabilities

Deliverables:
- Security review report
- List of vulnerabilities (Critical/High/Medium/Low)
- Remediation recommendations
- Fixed code examples
- Security checklist results
```

**Expected Output:**
- Security review report
- List of vulnerabilities with severity
- Specific code fixes for each issue
- Updated secure implementation

### Step 6: Apply Security Fixes

Based on security review:
1. Review all critical and high severity findings
2. Apply recommended fixes to server code
3. Re-run tests to ensure fixes don't break functionality
4. Update security documentation

### Step 7: Launch Deployment Agent

Use the Task tool to launch `mcp-deployment-engineer` agent.

**Agent Task:**
```
Create deployment configuration for MCP server:

Server: [project name]
Language: [Python/TypeScript]
Transport: [stdio/SSE]

Requirements:
1. Claude Desktop configuration (stdio)
2. Package configuration (pip/npm)
3. Docker container setup
4. Environment variable management
5. Installation documentation
6. Troubleshooting guide

Deliverables:
- Claude Desktop config example
- pyproject.toml / package.json for distribution
- Dockerfile
- .env.example
- Installation README
- Troubleshooting guide
```

**Expected Output:**
- Complete deployment configuration
- Claude Desktop setup instructions
- Docker container
- Package distribution setup
- Comprehensive documentation

### Step 8: Integration and Final Testing

1. **Test complete installation flow:**
   ```bash
   # Python
   pip install -e .
   python -m project_name

   # TypeScript
   npm install
   npm run build
   node build/index.js
   ```

2. **Test with MCP Inspector:**
   ```bash
   mcp-inspector python -m project_name
   # or
   mcp-inspector node build/index.js
   ```

3. **Test in Claude Desktop:**
   - Add to claude_desktop_config.json
   - Restart Claude Desktop
   - Verify tools appear
   - Test tool execution

### Step 9: Create Final Documentation

Compile comprehensive documentation:

**README.md** should include:
- Project overview and features
- Installation instructions (multiple methods)
- Configuration guide with all env vars
- Usage examples for each tool
- Development setup instructions
- Testing instructions
- Troubleshooting guide
- Contributing guidelines

**Additional Documentation:**
- API.md (detailed tool/resource/prompt documentation)
- SECURITY.md (security considerations and reporting)
- CHANGELOG.md (version history)
- CONTRIBUTING.md (contribution guidelines)

### Step 10: Provide Summary and Next Steps

Give the user a complete project summary:

```
✓ MCP Server Development Complete: [project-name]

Architecture:
- Tools: [count] ([list names])
- Resources: [count] ([list names])
- Prompts: [count] ([list names])

Implementation:
- Language: [Python/TypeScript]
- Transport: [stdio/SSE]
- Lines of Code: [count]

Testing:
- Unit Tests: [count] (100% passing)
- Integration Tests: [count] (100% passing)
- Code Coverage: [percentage]%

Security:
- Vulnerabilities Found: [count]
- Vulnerabilities Fixed: [count]
- Critical Issues: 0
- Security Score: [rating]

Deployment:
- Claude Desktop: Ready
- pip/npm: Ready
- Docker: Ready

Next Steps:
1. Review generated code and documentation
2. Test server locally with MCP Inspector
3. Test in Claude Desktop
4. Customize for your specific needs
5. Consider publishing to PyPI/npm

Files created:
[list all files]
```

## Example Full Workflow

```
User: /develop-mcp-server GitHub integration

[Gathering Requirements]
What GitHub operations should be supported?
> Issue creation, PR management, code search

What authentication method?
> GitHub Personal Access Token

Language preference?
> Python

[Step 2: Architecture]
Launching mcp-server-architect agent...
✓ Architecture designed:
  - 5 tools (create_issue, list_repos, create_pr, search_code, get_pr_status)
  - 3 resources (repos, issues, pull_requests)
  - 2 prompts (issue_template, pr_review)

[Step 3: Development]
Launching mcp-python-developer agent...
✓ Server implemented with FastMCP
✓ All tools implemented with PyGithub
✓ Input validation with Pydantic
✓ Configuration with environment variables

[Step 4: Testing]
Launching mcp-testing-engineer agent...
✓ 25 unit tests created (all passing)
✓ 5 integration tests created
✓ Coverage: 87%

[Step 5: Security Review]
Launching mcp-security-reviewer agent...
✓ Security review complete
✓ Found: 2 medium, 3 low issues
✓ All issues fixed

[Step 6: Deployment]
Launching mcp-deployment-engineer agent...
✓ Claude Desktop config created
✓ pyproject.toml configured
✓ Dockerfile created
✓ Documentation complete

[Integration Testing]
✓ MCP Inspector validation passed
✓ Claude Desktop integration verified

✓ COMPLETE: github-mcp-server ready for use!
```

## Success Criteria

- [ ] Requirements gathered and confirmed
- [ ] Architecture designed by architect agent
- [ ] Server implemented by developer agent
- [ ] Tests created by testing agent (>80% coverage)
- [ ] Security review completed with fixes applied
- [ ] Deployment configuration created
- [ ] MCP Inspector validation passed
- [ ] Claude Desktop integration tested
- [ ] Documentation complete
- [ ] User has working MCP server

This command provides end-to-end orchestrated MCP server development following best practices.
