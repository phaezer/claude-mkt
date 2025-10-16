---
description: End-to-end orchestrated MCP development workflow
argument-hint: Project requirements
---

# MCP Full-Stack Development Workflow

Complete end-to-end development of an MCP server from requirements to deployment.

## Usage

```
/mcp-full-stack-dev [requirements]
```

**Arguments:**
- `requirements`: Brief description of what to build (optional if discussed interactively)

**Examples:**
```
/mcp-full-stack-dev GitHub integration server with issue and PR management
/mcp-full-stack-dev PostgreSQL database query tool with read-only access
/mcp-full-stack-dev File system operations server for project management
```

## What This Command Does

This is the **master orchestration command** that runs the complete MCP development lifecycle:
1. Requirements gathering
2. Architecture design
3. Implementation
4. Testing
5. Security review
6. Deployment configuration
7. Documentation
8. Final validation

This command uses all other MCP engineer agents in a coordinated workflow.

## Workflow Steps

### Step 1: Requirements Gathering

Have a comprehensive discussion with the user to understand:

**Project Basics:**
- What problem does this server solve?
- Who will use it?
- What makes this useful?

**Technical Requirements:**
1. **Server Purpose**: Detailed description of functionality
2. **Tools Needed**: What actions should the LLM perform?
   - Create, read, update, delete operations?
   - Search/query functionality?
   - External API interactions?
3. **Resources Needed**: What data should the LLM access?
   - File system resources?
   - Database queries?
   - API endpoints?
4. **Prompts Needed**: Common workflows that need templates?
5. **External Services**: APIs, databases, file systems?
6. **Authentication**: API keys, tokens, credentials?
7. **Language**: Python or TypeScript?
8. **Target Users**: Claude Desktop, web app, CLI?

**Document Requirements:**
Create a requirements document summarizing all inputs.

### Step 2: Phase 1 - Architecture Design

Launch `mcp-server-architect` agent via Task tool.

**Agent Task:**
```
Design comprehensive MCP server architecture:

Project: [project name]
Purpose: [detailed purpose]

Requirements:
Tools:
- [list each tool with description]

Resources:
- [list each resource with description]

Prompts:
- [list each prompt with description]

External Services:
- [list APIs, databases, etc.]

Transport: stdio (Claude Desktop)
Language: [Python/TypeScript]

Provide:
1. Complete tools specification
   - Names, descriptions
   - Input schemas with validation rules
   - Output formats
   - Error handling requirements

2. Resources specification
   - URI patterns
   - Data sources
   - Access patterns
   - Caching strategy

3. Prompts specification
   - Template content
   - Parameters
   - Use cases

4. Server structure
   - File organization
   - Module breakdown
   - Configuration needs

5. Security considerations
   - Input validation requirements
   - Access control needs
   - Credential management
```

**Output:** Complete architecture document with all specifications.

**Checkpoint:** Review architecture with user and confirm before proceeding.

### Step 3: Phase 2 - Project Initialization

Use `/mcp-init-project` command to create project structure.

```
/mcp-init-project server [python/typescript]
```

This creates:
- Complete directory structure
- Configuration files
- Testing setup
- Development tools
- Git repository

**Validate:** Ensure project structure is created correctly.

### Step 4: Phase 3 - Implementation

Launch `mcp-python-developer` or `mcp-typescript-developer` agent via Task tool.

**Agent Task:**
```
Implement MCP server based on architecture:

Architecture: [paste complete architecture from Step 2]
Project Location: [path]
Language: [Python/TypeScript]

Implementation Requirements:
1. Implement ALL tools with:
   - Complete input validation (Pydantic/Zod)
   - Proper error handling
   - Comprehensive logging
   - Return type consistency

2. Implement ALL resources with:
   - Efficient data access
   - Caching where appropriate
   - Access control validation
   - Error handling

3. Implement ALL prompts with:
   - Parameter handling
   - Template rendering
   - Example usage

4. Configuration management:
   - Environment variables for all secrets
   - Configuration validation
   - Sensible defaults

5. Server setup:
   - [stdio/SSE] transport configuration
   - Proper initialization
   - Graceful shutdown
   - Health monitoring

Code Quality Requirements:
- Type hints (Python) / TypeScript types
- Docstrings for all functions
- Clear variable names
- Modular organization
- No hardcoded values
```

**Output:** Complete, working server implementation.

**Validate:** Test server starts and basic functionality works.

### Step 5: Phase 4 - Testing

Launch `mcp-testing-engineer` agent via Task tool.

**Agent Task:**
```
Create comprehensive test suite for MCP server:

Project: [project name]
Location: [path]
Language: [Python/TypeScript]

Testing Requirements:
1. Unit Tests (target >80% coverage):
   - Test each tool individually
   - Test all success paths
   - Test all error paths
   - Test edge cases
   - Mock external dependencies

2. Integration Tests:
   - Test with MCP Inspector
   - Test tool chains
   - Test resource access
   - Test prompt generation
   - Test configuration loading

3. Error Scenario Tests:
   - Invalid inputs
   - Network failures
   - Timeouts
   - Missing credentials
   - Rate limiting

4. Performance Tests:
   - Response time checks
   - Concurrent request handling
   - Memory usage

5. Mock Fixtures:
   - Mock external APIs
   - Mock database responses
   - Sample test data

Deliverables:
- Complete test suite
- pytest/jest configuration
- Mock fixtures
- Coverage report (>80%)
- Integration test results
```

**Output:** Complete test suite with high coverage.

**Checkpoint:** Run tests and ensure all pass:
```bash
# Python
pytest --cov=src --cov-report=term-missing

# TypeScript
npm test -- --coverage
```

### Step 6: Phase 5 - Security Review

Launch `mcp-security-reviewer` agent via Task tool.

**Agent Task:**
```
Perform comprehensive security review:

Project: [project name]
Location: [path]
Language: [Python/TypeScript]

Review all code for:
1. Input Validation
   - Tool parameter validation
   - File path sanitization
   - SQL query safety
   - Command execution safety

2. Authentication & Authorization
   - Credential management
   - API key handling
   - No hardcoded secrets

3. Information Disclosure
   - Error messages
   - Logging practices
   - Stack trace exposure

4. Dependency Security
   - Known vulnerabilities
   - Outdated packages

5. Rate Limiting
   - Abuse prevention
   - Resource protection

Deliverables:
- Security review report
- Vulnerability list with severity
- Code fixes for all issues
- Updated secure implementation
```

**Output:** Security review report with fixes.

**Checkpoint:** Apply all critical and high severity fixes, re-run tests.

### Step 7: Phase 6 - Deployment Configuration

Launch `mcp-deployment-engineer` agent via Task tool.

**Agent Task:**
```
Create deployment configuration:

Project: [project name]
Location: [path]
Language: [Python/TypeScript]
Target: local and docker

Deployment Requirements:
1. Claude Desktop Integration
   - Configuration example
   - Installation instructions
   - Environment variable guide

2. Package Configuration
   - pip/npm package setup
   - Version configuration
   - Dependency management

3. Docker Configuration
   - Optimized Dockerfile
   - docker-compose.yml
   - Health checks
   - Security best practices

4. Documentation
   - Complete README.md
   - Installation guide
   - Configuration guide
   - Troubleshooting guide
   - Usage examples

5. Environment Management
   - .env.example with all variables
   - Variable documentation
   - Secret management guide

Deliverables:
- Claude Desktop config
- pyproject.toml/package.json
- Dockerfile and docker-compose.yml
- Complete documentation
- Troubleshooting guide
```

**Output:** Complete deployment configuration and documentation.

### Step 8: Phase 7 - Integration Testing

Test the complete deployment workflow:

**Test 1: Local Installation**
```bash
# Python
pip install -e .
my-mcp-server  # Should start without errors

# TypeScript
npm install
npm run build
node build/index.js  # Should start without errors
```

**Test 2: MCP Inspector Validation**
```bash
# Test protocol compliance
mcp-inspector python -m my_mcp_server
# or
mcp-inspector node build/index.js

# Verify:
# - Server starts
# - Tools discovered
# - Resources discovered
# - Prompts discovered
# - Protocol compliant
```

**Test 3: Claude Desktop Integration**
1. Add to claude_desktop_config.json
2. Restart Claude Desktop
3. Verify tools appear in Claude
4. Test each tool execution
5. Test resource access
6. Test prompt usage

**Test 4: Docker Deployment**
```bash
# Build image
docker build -t my-mcp-server:latest .

# Run container
docker run -i -e API_KEY=test my-mcp-server:latest

# Test with inspector
docker run -i my-mcp-server:latest | mcp-inspector -
```

**Test 5: Error Scenarios**
- Start without required environment variables
- Invalid API credentials
- Network connectivity issues
- Malformed inputs to tools

### Step 9: Phase 8 - Final Documentation

Compile comprehensive project documentation:

**README.md** sections:
1. Project Overview
   - What it does
   - Key features
   - Use cases

2. Quick Start
   - Fastest way to get running
   - Minimal configuration

3. Installation
   - Multiple installation methods
   - Prerequisites
   - Step-by-step instructions

4. Configuration
   - All environment variables
   - How to get API keys
   - Configuration file options

5. Usage Examples
   - Each tool with examples
   - Each resource with examples
   - Common workflows

6. Development
   - Setup dev environment
   - Running tests
   - Contributing

7. Troubleshooting
   - Common issues
   - Debug mode
   - Getting help

**Additional Documentation:**
- API.md (detailed tool/resource docs)
- SECURITY.md (security considerations)
- CHANGELOG.md (version history)
- CONTRIBUTING.md (contribution guide)
- LICENSE (license file)

### Step 10: Final Validation and Handoff

Create comprehensive project summary:

```
✓ MCP Server Development Complete: [project-name]

═══════════════════════════════════════════════════════
PROJECT SUMMARY
═══════════════════════════════════════════════════════

Purpose: [brief description]
Language: [Python/TypeScript]
Transport: stdio
Target: Claude Desktop

═══════════════════════════════════════════════════════
ARCHITECTURE
═══════════════════════════════════════════════════════

Tools Implemented: [count]
  1. [tool_name] - [description]
  2. [tool_name] - [description]
  ...

Resources Implemented: [count]
  1. [uri_pattern] - [description]
  2. [uri_pattern] - [description]
  ...

Prompts Implemented: [count]
  1. [prompt_name] - [description]
  ...

═══════════════════════════════════════════════════════
IMPLEMENTATION METRICS
═══════════════════════════════════════════════════════

Lines of Code: [count]
Files: [count]
Modules: [count]

Dependencies:
  - [dependency]: [version]
  - [dependency]: [version]
  ...

═══════════════════════════════════════════════════════
TESTING RESULTS
═══════════════════════════════════════════════════════

Unit Tests: [count]/[count] passed ✓
Integration Tests: [count]/[count] passed ✓
MCP Inspector: PASS ✓
Protocol Compliance: PASS ✓
Performance: PASS ✓

Code Coverage: [percentage]%
  - Tools: [percentage]%
  - Resources: [percentage]%
  - Prompts: [percentage]%
  - Config: [percentage]%

═══════════════════════════════════════════════════════
SECURITY REVIEW
═══════════════════════════════════════════════════════

Vulnerabilities Found: [count]
  - Critical: 0 ✓
  - High: 0 ✓
  - Medium: [count] (all fixed)
  - Low: [count] (documented)

Security Checklist: [percentage]% complete
Input Validation: ✓
Authentication: ✓
Authorization: ✓
Error Handling: ✓
Dependency Security: ✓

═══════════════════════════════════════════════════════
DEPLOYMENT STATUS
═══════════════════════════════════════════════════════

Local Deployment: ✓ Ready
  - Claude Desktop: Configured
  - pip/npm: Package ready
  - Documentation: Complete

Docker Deployment: ✓ Ready
  - Dockerfile: Optimized ([size]MB)
  - docker-compose.yml: Configured
  - Health checks: Implemented

═══════════════════════════════════════════════════════
DOCUMENTATION
═══════════════════════════════════════════════════════

✓ README.md - Complete guide
✓ API.md - Full API documentation
✓ TROUBLESHOOTING.md - Common issues and solutions
✓ SECURITY.md - Security considerations
✓ CONTRIBUTING.md - Contribution guidelines
✓ CHANGELOG.md - Version history

═══════════════════════════════════════════════════════
NEXT STEPS
═══════════════════════════════════════════════════════

1. Test Locally:
   $ pip install -e .        # or: npm install
   $ my-mcp-server           # Verify it starts

2. Test with Inspector:
   $ mcp-inspector python -m my_mcp_server

3. Configure Claude Desktop:
   Edit: ~/Library/Application Support/Claude/claude_desktop_config.json
   Add: [see claude-desktop-config.example.json]

4. Restart Claude Desktop

5. Test in Claude:
   - Verify tools appear
   - Test each tool
   - Try example workflows

6. (Optional) Publish:
   Python: python -m build && twine upload dist/*
   Node.js: npm publish

7. (Optional) Deploy Docker:
   $ docker-compose up -d

═══════════════════════════════════════════════════════
FILES CREATED
═══════════════════════════════════════════════════════

Source Code:
  [list all source files]

Tests:
  [list all test files]

Configuration:
  - pyproject.toml / package.json
  - .env.example
  - claude-desktop-config.example.json
  - Dockerfile
  - docker-compose.yml

Documentation:
  - README.md
  - API.md
  - TROUBLESHOOTING.md
  - SECURITY.md
  - CONTRIBUTING.md
  - CHANGELOG.md

═══════════════════════════════════════════════════════

Your MCP server is ready for use! 🎉

For support or questions, see TROUBLESHOOTING.md or open an issue.
```

## Example Full Workflow

```
User: /mcp-full-stack-dev GitHub integration server

[Step 1: Requirements]
Gathering requirements...
Tools: create_issue, list_repos, create_pr, search_code, get_pr_status
Resources: repos, issues, pull_requests
Authentication: GitHub Personal Access Token
Language: Python

[Step 2: Architecture]
Launching mcp-server-architect...
✓ Architecture complete
  - 5 tools designed
  - 3 resources designed
  - 2 prompts designed

[Step 3: Initialization]
Running /mcp-init-project...
✓ Project structure created
✓ Git repository initialized

[Step 4: Implementation]
Launching mcp-python-developer...
✓ Server implemented with FastMCP
✓ All tools implemented
✓ All resources implemented
✓ Configuration management added

[Step 5: Testing]
Launching mcp-testing-engineer...
✓ 28 tests created
✓ Coverage: 89%
✓ All tests passing

[Step 6: Security Review]
Launching mcp-security-reviewer...
✓ Security review complete
✓ 2 medium issues found and fixed
✓ No critical issues

[Step 7: Deployment]
Launching mcp-deployment-engineer...
✓ Claude Desktop config created
✓ Docker configuration created
✓ Documentation complete

[Step 8: Integration Testing]
✓ Local installation tested
✓ MCP Inspector validation passed
✓ Claude Desktop integration verified
✓ Docker deployment tested

[Step 9: Final Documentation]
✓ All documentation complete

[Step 10: Handoff]
✓ Complete project summary generated

🎉 github-mcp-server is ready for production use!
```

## Success Criteria

- [ ] Requirements fully documented
- [ ] Architecture designed and approved
- [ ] Project structure created
- [ ] Complete implementation
- [ ] All tests passing (>80% coverage)
- [ ] Security review complete with fixes
- [ ] Deployment configuration ready
- [ ] All documentation complete
- [ ] MCP Inspector validation passed
- [ ] Claude Desktop integration tested
- [ ] Docker deployment tested
- [ ] User has production-ready MCP server

This command provides the complete end-to-end orchestrated workflow for professional MCP server development.
