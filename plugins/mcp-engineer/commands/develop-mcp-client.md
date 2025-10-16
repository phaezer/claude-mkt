---
description: Develop MCP client for integration with MCP servers
argument-hint: Client requirements and target servers
---

# Develop MCP Client

Orchestrate the complete development of an MCP client for connecting to and using MCP servers.

## Usage

```
/develop-mcp-client [requirements]
```

**Arguments:**
- `requirements`: Brief description of client needs (optional if discussed interactively)

**Examples:**
```
/develop-mcp-client CLI tool to interact with GitHub MCP server
/develop-mcp-client Desktop app aggregating multiple MCP servers
/develop-mcp-client Web application using remote MCP servers via SSE
```

## What This Command Does

This command orchestrates the full MCP client development workflow by:
1. Designing client architecture and integration patterns
2. Implementing the client in chosen language
3. Creating comprehensive tests
4. Performing security review
5. Setting up deployment configuration

## Workflow Steps

### Step 1: Gather Requirements

Ask the user to clarify:

1. **Client Purpose**: What will this client do?
2. **Target Servers**: Which MCP servers will it connect to?
3. **Client Type**:
   - CLI tool
   - Desktop application
   - Web application
   - Library/SDK
   - Proxy/Gateway
4. **Connection Pattern**:
   - Single server
   - Multiple servers (aggregation)
   - Dynamic server discovery
5. **Transport**: stdio, SSE, or both?
6. **Language Preference**: Python or TypeScript?
7. **User Interface**: Command-line, GUI, API, or library?

### Step 2: Launch Client Architect Agent

Use the Task tool to launch `mcp-client-architect` agent with requirements.

**Agent Task:**
```
Design MCP client architecture for [purpose].

Requirements:
- Target servers: [list of servers]
- Client type: [CLI/Desktop/Web/Library]
- Connection pattern: [single/multiple/proxy]
- Transport: [stdio/SSE/both]
- Use cases: [list of what user wants to do]

Provide:
1. Client architecture pattern
2. Connection management strategy
3. Tool invocation design
4. Resource access patterns
5. Error handling and retry logic
6. Caching strategy (if applicable)
7. Configuration requirements

Deliverables:
- Complete client architecture
- Connection lifecycle design
- Tool/resource access patterns
- Error handling strategy
- Configuration specification
```

**Expected Output:**
- Client architecture pattern (single-purpose, aggregator, proxy, gateway)
- Connection management design
- Tool call workflow
- Resource access workflow
- Error handling and retry strategies
- Configuration needs

### Step 3: Launch Development Agent

Based on language choice, launch `mcp-python-developer` or `mcp-typescript-developer` agent.

**Agent Task:**
```
Implement MCP client based on architecture:

Architecture:
[paste architecture specification from Step 2]

Language: [Python/TypeScript]
Client Type: [CLI/Desktop/Web/Library]

Requirements:
1. Implement server connection management
2. Implement tool discovery and invocation
3. Implement resource access
4. Implement prompt template usage
5. Configure [stdio/SSE] transport(s)
6. Add comprehensive error handling
7. Implement retry logic
8. Add connection health monitoring
9. Include proper logging
10. Create configuration management

Deliverables:
- Complete client implementation
- Connection manager
- Tool/resource access methods
- Configuration setup
- CLI/UI implementation (if applicable)
- Documentation
```

**Expected Output:**
- Complete client code
- Server connection management
- Tool invocation methods
- Resource access methods
- Transport layer configuration
- User interface (if CLI/GUI)
- Configuration management
- Usage examples

### Step 4: Launch Testing Agent

Use the Task tool to launch `mcp-testing-engineer` agent.

**Agent Task:**
```
Create comprehensive test suite for MCP client:

Client: [project name]
Language: [Python/TypeScript]
Target Servers: [list]

Requirements:
1. Unit tests for connection management
2. Unit tests for tool invocation
3. Unit tests for resource access
4. Integration tests with mock servers
5. Integration tests with real servers (if available)
6. Error scenario testing (disconnects, timeouts, etc.)
7. Mock MCP server for testing
8. Coverage > 80%

Deliverables:
- Complete test suite
- Mock MCP server for testing
- pytest/jest configuration
- Integration tests
- Coverage report
- Test documentation
```

**Expected Output:**
- Comprehensive test suite
- Mock MCP server implementation
- Test configuration
- Integration tests
- Coverage report >80%

### Step 5: Launch Security Review Agent

Use the Task tool to launch `mcp-security-reviewer` agent.

**Agent Task:**
```
Perform security review of MCP client:

Client: [project name]
Code: [location of client code]

Review for:
1. Server connection security
2. Tool parameter validation
3. Resource URI validation
4. Credential management (API keys, tokens)
5. Information disclosure in errors
6. Dependency vulnerabilities
7. Transport security (if SSE)
8. Man-in-the-middle protection

Deliverables:
- Security review report
- List of vulnerabilities
- Remediation recommendations
- Fixed code examples
```

**Expected Output:**
- Security review report
- Vulnerability list with severity
- Specific code fixes
- Security best practices

### Step 6: Apply Security Fixes

Based on security review:
1. Review all critical and high severity findings
2. Apply recommended fixes to client code
3. Re-run tests to ensure fixes work
4. Update security documentation

### Step 7: Launch Deployment Agent

Use the Task tool to launch `mcp-deployment-engineer` agent.

**Agent Task:**
```
Create deployment configuration for MCP client:

Client: [project name]
Language: [Python/TypeScript]
Client Type: [CLI/Desktop/Web/Library]

Requirements:
1. Package configuration (pip/npm)
2. Installation documentation
3. Configuration guide (how to specify servers)
4. Docker container (if applicable)
5. Environment variable management
6. Troubleshooting guide

Deliverables:
- pyproject.toml / package.json
- Dockerfile (if applicable)
- .env.example
- Installation README
- Configuration guide
- Troubleshooting guide
```

**Expected Output:**
- Package distribution setup
- Docker container (if needed)
- Installation documentation
- Configuration guide
- Troubleshooting guide

### Step 8: Integration and Final Testing

1. **Test client installation:**
   ```bash
   # Python
   pip install -e .
   myclient --help

   # TypeScript
   npm install
   npm run build
   node build/index.js --help
   ```

2. **Test with mock server:**
   ```bash
   # Start mock server in one terminal
   python tests/mock_server.py

   # Run client in another terminal
   myclient connect --server mock list-tools
   ```

3. **Test with real server (if available):**
   ```bash
   myclient connect --server github list-tools
   myclient call-tool create_issue --repo user/repo --title "Test"
   ```

4. **Test error scenarios:**
   - Server unavailable
   - Network timeout
   - Invalid tool parameters
   - Connection drops

### Step 9: Create Final Documentation

Compile comprehensive documentation:

**README.md** should include:
- Project overview and features
- Installation instructions
- Configuration guide
  - How to specify MCP servers
  - How to configure authentication
- Usage examples
  - Connecting to servers
  - Listing tools
  - Calling tools
  - Reading resources
- Development setup
- Testing instructions
- Troubleshooting

**For CLI clients, include:**
- Complete command reference
- Configuration file format
- Environment variables

**For Library clients, include:**
- API reference
- Code examples
- Integration guide

### Step 10: Provide Summary and Next Steps

Give the user a complete project summary:

```
✓ MCP Client Development Complete: [project-name]

Architecture:
- Client Type: [CLI/Desktop/Web/Library]
- Connection Pattern: [pattern]
- Supported Servers: [count]

Implementation:
- Language: [Python/TypeScript]
- Transport: [stdio/SSE/both]
- Features: [list key features]

Testing:
- Unit Tests: [count] (100% passing)
- Integration Tests: [count] (100% passing)
- Code Coverage: [percentage]%

Security:
- Vulnerabilities Found: [count]
- Vulnerabilities Fixed: [count]
- Security Score: [rating]

Deployment:
- Package: Ready for pip/npm
- Docker: [Yes/No]

Next Steps:
1. Review generated code
2. Test client with target MCP servers
3. Customize for your specific needs
4. Consider publishing to PyPI/npm

Files created:
[list all files]
```

## Example CLI Client Workflow

```
User: /develop-mcp-client CLI tool for GitHub MCP server

[Gathering Requirements]
What operations should the CLI support?
> List tools, call tools, read resources

Authentication method?
> GitHub token from environment variable

[Step 2: Architecture]
Launching mcp-client-architect agent...
✓ Architecture designed:
  - CLI with subcommands (connect, list-tools, call-tool, read-resource)
  - Single server connection pattern
  - stdio transport
  - Configuration via CLI args and config file

[Step 3: Development]
Launching mcp-python-developer agent...
✓ CLI implemented with Click framework
✓ Server connection via official MCP SDK
✓ Tool invocation with result formatting
✓ Configuration management

[Step 4: Testing]
Launching mcp-testing-engineer agent...
✓ 18 unit tests created (all passing)
✓ Mock MCP server for testing
✓ Integration tests
✓ Coverage: 84%

[Step 5: Security Review]
Launching mcp-security-reviewer agent...
✓ Security review complete
✓ Found: 1 medium issue (token logging)
✓ Issue fixed

[Step 6: Deployment]
Launching mcp-deployment-engineer agent...
✓ pyproject.toml configured
✓ Entry point configured
✓ Documentation complete

[Integration Testing]
✓ CLI tested with mock server
✓ CLI tested with real GitHub MCP server

✓ COMPLETE: github-mcp-cli ready for use!

Example usage:
  github-mcp-cli list-tools
  github-mcp-cli call-tool create_issue --repo user/repo --title "Bug"
  github-mcp-cli read-resource "github://repos/user"
```

## Example Aggregator Client Workflow

```
User: /develop-mcp-client Desktop app aggregating GitHub, Database, and Filesystem servers

[Gathering Requirements]
What should the app do?
> Provide unified interface to multiple MCP servers

UI type?
> Electron-based desktop GUI

[Step 2: Architecture]
Launching mcp-client-architect agent...
✓ Architecture designed:
  - Multi-server aggregator pattern
  - Server registry with discovery
  - Tool namespacing (github:create_issue, fs:read_file)
  - Connection health monitoring
  - Unified error handling

[Step 3: Development]
Launching mcp-typescript-developer agent...
✓ Electron app implemented
✓ Server connection manager (manages 3+ servers)
✓ UI for tool browsing and execution
✓ Result display and error handling
✓ Configuration UI

[Integration Testing]
✓ Tested with all three servers
✓ Tool namespacing works correctly
✓ Connection recovery tested

✓ COMPLETE: mcp-desktop-aggregator ready for use!
```

## Client Architecture Patterns

This command supports these client patterns:

1. **Single-Purpose Client**: Connect to one specific server
2. **Multi-Server Aggregator**: Aggregate capabilities from multiple servers
3. **MCP Proxy**: Translate between transports (stdio ↔ SSE)
4. **MCP Gateway**: Centralized access point for organization
5. **Library/SDK**: Reusable client library for integration

## Success Criteria

- [ ] Requirements gathered and confirmed
- [ ] Architecture designed by architect agent
- [ ] Client implemented by developer agent
- [ ] Tests created by testing agent (>80% coverage)
- [ ] Security review completed with fixes applied
- [ ] Deployment configuration created
- [ ] Integration testing passed
- [ ] Documentation complete
- [ ] User has working MCP client

This command provides end-to-end orchestrated MCP client development following best practices.
