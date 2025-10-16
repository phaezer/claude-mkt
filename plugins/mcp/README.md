# MCP Engineer Plugin

Comprehensive Claude Code plugin for architecting, developing, testing, and deploying MCP (Model Context Protocol) servers and clients in Python and TypeScript.

## Overview

The MCP Engineer plugin provides specialized agents and orchestrated workflows for complete MCP development from requirements to production deployment. It follows industry best practices for secure, tested, and well-documented MCP implementations.

## Features

- **Complete MCP Lifecycle**: Requirements → Architecture → Development → Testing → Security → Deployment
- **Multi-Language Support**: Python (3.11+, FastMCP, official SDK) and TypeScript (CommonJS, official SDK)
- **Orchestrated Workflows**: Coordinated agent collaboration for complex development tasks
- **Security-First**: Built-in security reviews and vulnerability scanning
- **Production-Ready**: Deployment configurations for Claude Desktop, pip/npm, and Docker
- **Comprehensive Testing**: Unit tests, integration tests, MCP Inspector validation

## Agents (8 Specialists)

### Orchestration
- **mcp-orchestrator** (Opus, purple): Main coordinator for complex MCP development projects

### Architecture
- **mcp-server-architect** (Sonnet, blue): Designs MCP server architecture (tools, resources, prompts)
- **mcp-client-architect** (Sonnet, cyan): Designs MCP client architecture for server integration

### Development
- **mcp-python-developer** (Sonnet, green): Develops MCP servers/clients in Python (FastMCP, Python 3.11+)
- **mcp-typescript-developer** (Sonnet, blue): Develops MCP servers/clients in TypeScript (CommonJS)

### Quality & Deployment
- **mcp-testing-engineer** (Sonnet, yellow): Creates unit and integration tests with MCP Inspector
- **mcp-deployment-engineer** (Sonnet, purple): Handles local installation and Docker deployment
- **mcp-security-reviewer** (Sonnet, red): Security review and vulnerability assessment

## Slash Commands (7 Workflows)

### Project Management
- `/mcp-init-project`: Initialize new MCP server or client project with complete structure

### Development Workflows
- `/develop-mcp-server`: Orchestrated MCP server development (architecture → implementation → testing → security → deployment)
- `/develop-mcp-client`: Orchestrated MCP client development for integrating with MCP servers
- `/mcp-full-stack-dev`: Complete end-to-end MCP development workflow

### Quality Assurance
- `/test-mcp`: Comprehensive testing (unit, integration, MCP Inspector, protocol compliance)
- `/review-mcp-security`: Security review with vulnerability scanning and remediation

### Deployment
- `/deploy-mcp`: Deploy MCP server locally or with Docker

## Technology Stack

### Python Support
- **Framework**: FastMCP (recommended) or official `mcp` SDK
- **Python Version**: 3.11+
- **Validation**: Pydantic 2.0+
- **Testing**: pytest, pytest-asyncio, pytest-cov
- **Deployment**: pip, uvx, Docker

### TypeScript Support
- **Framework**: Official `@modelcontextprotocol/sdk`
- **Module System**: CommonJS (require/module.exports)
- **Validation**: Zod
- **Testing**: Jest or Vitest
- **Deployment**: npm, npx, Docker

### Transport Layers
- **stdio**: For local Claude Desktop integration
- **SSE**: For remote server access (HTTP with Server-Sent Events)

## Quick Start Examples

### Create a New MCP Server

```bash
# Initialize project
/mcp-init-project server python

# Full orchestrated development
/mcp-full-stack-dev GitHub integration server with issue and PR management
```

### Test an Existing MCP Server

```bash
# Comprehensive testing
/test-mcp ./my-mcp-server

# Security review
/review-mcp-security ./my-mcp-server
```

### Deploy MCP Server

```bash
# Local deployment (Claude Desktop)
/deploy-mcp local

# Docker deployment
/deploy-mcp docker

# Both
/deploy-mcp both
```

## Workflow Patterns

### Pattern 1: New MCP Server Development

1. **Architecture** → `mcp-server-architect` designs tools, resources, prompts
2. **Development** → `mcp-python-developer` or `mcp-typescript-developer` implements
3. **Testing** → `mcp-testing-engineer` creates comprehensive test suite
4. **Security** → `mcp-security-reviewer` identifies and fixes vulnerabilities
5. **Deployment** → `mcp-deployment-engineer` creates deployment configs
6. **Delivery** → Complete, production-ready MCP server

### Pattern 2: MCP Client Development

1. **Architecture** → `mcp-client-architect` designs connection and integration patterns
2. **Development** → Language-specific developer implements client
3. **Testing** → `mcp-testing-engineer` validates integration
4. **Security** → `mcp-security-reviewer` checks security
5. **Deployment** → `mcp-deployment-engineer` packages client

### Pattern 3: Security Review

1. **Automated Scanning** → bandit/safety (Python) or npm audit/snyk (TypeScript)
2. **Manual Review** → `mcp-security-reviewer` examines code for vulnerabilities
3. **Remediation** → Specific fixes for each vulnerability
4. **Validation** → Re-test to ensure fixes work

## Deployment Targets

### Claude Desktop (Primary)
- stdio transport for local integration
- Configuration via `claude_desktop_config.json`
- Environment variable management
- Automatic tool discovery

### Package Distribution
- **Python**: PyPI via pip, or uvx for development
- **TypeScript**: npm registry, or npx for development

### Docker
- Optimized Dockerfiles (multi-stage builds)
- Non-root user configuration
- Health checks
- docker-compose support

## Security Features

The plugin emphasizes security throughout the development lifecycle:

- **Input Validation**: Automatic Pydantic/Zod validation generation
- **Path Traversal Prevention**: Base directory restrictions
- **Command Injection Prevention**: Command whitelisting, no shell=True
- **SQL Injection Prevention**: Parameterized queries only
- **Credential Management**: Environment variables, no hardcoded secrets
- **Error Handling**: No information disclosure in errors
- **Rate Limiting**: Abuse prevention patterns
- **Dependency Scanning**: Automated vulnerability detection

## Testing Support

Comprehensive testing at multiple levels:

- **Unit Tests**: Individual tool/resource/prompt testing
- **Integration Tests**: Server-client interaction testing
- **Protocol Compliance**: MCP Inspector validation
- **Error Scenarios**: Timeout, disconnection, invalid input testing
- **Performance Tests**: Response time and concurrency testing
- **Coverage Analysis**: Target >80% code coverage

## MCP Protocol Concepts

### Tools
Functions the LLM can call to perform actions (like API endpoints).

**Example**: `create_file`, `search_github`, `query_database`

### Resources
Read-only data sources for LLM context (via URIs).

**Example**: `file:///path/to/file`, `db://table/id`, `api://endpoint`

### Prompts
Pre-written prompt templates for common workflows.

**Example**: `code_review`, `issue_triage`, `debug_assistant`

### Transport
Communication layer between LLM host and MCP server.
- **stdio**: Subprocess communication (local)
- **SSE**: HTTP with Server-Sent Events (remote)

## Example Projects

### GitHub Integration Server
```bash
/mcp-full-stack-dev GitHub server with create_issue, list_repos, create_pr, search_code tools
```

**Result**: Complete server with 5 tools, 3 resources, 2 prompts, full test suite, security review, and deployment configs.

### Database Query Client
```bash
/develop-mcp-client CLI tool for PostgreSQL MCP server with read-only queries
```

**Result**: CLI client with connection management, tool invocation, and resource access.

### File System Server
```bash
/develop-mcp-server File operations with read_file, write_file, list_directory, and file:// resources
```

**Result**: Secure file system server with path validation and access controls.

## Best Practices Enforced

- **Type Safety**: Pydantic/Zod schemas for all inputs/outputs
- **Error Handling**: Comprehensive try-catch with user-friendly messages
- **Input Validation**: Length limits, character whitelisting, type checking
- **Secure Configuration**: Environment variables for all secrets
- **Documentation**: README, API docs, troubleshooting guides
- **Testing**: Unit + integration + protocol compliance
- **Security**: Regular reviews and dependency scanning
- **Logging**: Structured logging without credential exposure

## Documentation Generated

Every project includes:
- **README.md**: Installation, configuration, usage, troubleshooting
- **API.md**: Detailed tool/resource/prompt documentation
- **SECURITY.md**: Security considerations and reporting
- **CONTRIBUTING.md**: Contribution guidelines
- **CHANGELOG.md**: Version history
- **TROUBLESHOOTING.md**: Common issues and solutions

## Requirements

### For Python Development
- Python 3.11 or higher
- pip or uv package manager
- pytest for testing
- MCP Inspector (`npx @modelcontextprotocol/inspector`)

### For TypeScript Development
- Node.js 18 or higher
- npm or pnpm package manager
- Jest or Vitest for testing
- MCP Inspector (`npx @modelcontextprotocol/inspector`)

### For All Development
- Git for version control
- Docker (optional, for containerization)
- Claude Desktop (for testing integration)

## Resources

- **MCP Documentation**: https://modelcontextprotocol.io
- **MCP Specification**: https://spec.modelcontextprotocol.io
- **FastMCP**: https://github.com/jlowin/fastmcp
- **Official Python SDK**: https://github.com/modelcontextprotocol/python-sdk
- **Official TypeScript SDK**: https://github.com/modelcontextprotocol/typescript-sdk
- **MCP Inspector**: https://github.com/modelcontextprotocol/inspector

## Version History

### 1.0.0 (2025-10-15)
- Initial release
- 8 specialized agents (orchestrator, 2 architects, 2 developers, 3 QA/deployment)
- 7 slash commands for complete MCP development lifecycle
- Python 3.11+ support with FastMCP and official SDK
- TypeScript CommonJS support with official SDK
- Security-first development approach
- Comprehensive testing support
- Multi-platform deployment (Claude Desktop, pip/npm, Docker)

## License

MIT

## Author

Eric Austin <e@plsr.io>
