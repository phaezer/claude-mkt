---
description: Deploy MCP server locally or with Docker
argument-hint: Deployment target (local/docker/both)
---

# Deploy MCP Server

Deploy an MCP server for local usage (Claude Desktop, pip/npm) or Docker containers.

## Usage

```
/deploy-mcp [target]
```

**Arguments:**
- `target`: Deployment target - `local`, `docker`, or `both` (optional, defaults to `local`)

**Examples:**
```
/deploy-mcp local
/deploy-mcp docker
/deploy-mcp both
```

## What This Command Does

This command handles the complete deployment workflow for MCP servers:
- Local deployment (Claude Desktop integration, pip/npm packages)
- Docker containerization
- Configuration management
- Documentation creation
- Troubleshooting guides

## Workflow Steps

### Step 1: Detect Project Details

Analyze the project:
1. **Language**: Python or TypeScript?
2. **Server Type**: stdio, SSE, or both?
3. **Dependencies**: What external services are needed?
4. **Configuration**: What environment variables required?

### Step 2: Launch Deployment Engineer Agent

Use the Task tool to launch `mcp-deployment-engineer` agent.

**Agent Task:**
```
Create deployment configuration for MCP server:

Project: [project name]
Location: [project path]
Language: [Python/TypeScript]
Transport: [stdio/SSE/both]
Target: [local/docker/both]

Requirements:
1. Local Deployment:
   - Claude Desktop configuration
   - Package configuration (pip/npm)
   - Installation documentation
   - Environment variable management

2. Docker Deployment (if requested):
   - Dockerfile optimized for size and security
   - Docker Compose configuration
   - Health checks
   - Volume management

3. Documentation:
   - Installation guide
   - Configuration guide
   - Troubleshooting guide
   - Usage examples

Deliverables:
- Claude Desktop config example
- Package configuration (pyproject.toml/package.json)
- Dockerfile and docker-compose.yml (if requested)
- .env.example with all variables
- Complete README.md
- Troubleshooting guide
```

### Step 3: Prepare for Local Deployment

**Python Package Preparation:**

1. **Verify pyproject.toml:**
```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "my-mcp-server"
version = "0.1.0"
description = "MCP server for [purpose]"
requires-python = ">=3.11"
dependencies = [
    "fastmcp>=0.1.0",
    # other dependencies
]

[project.scripts]
my-mcp-server = "my_mcp_server.__main__:main"
```

2. **Create distribution:**
```bash
# Install build tools
pip install build

# Build package
python -m build

# Test installation locally
pip install dist/my_mcp_server-0.1.0-py3-none-any.whl

# Test running
my-mcp-server
```

**TypeScript Package Preparation:**

1. **Verify package.json:**
```json
{
  "name": "my-mcp-server",
  "version": "0.1.0",
  "main": "./build/index.js",
  "bin": {
    "my-mcp-server": "./build/index.js"
  },
  "files": [
    "build",
    "README.md"
  ],
  "scripts": {
    "build": "tsc",
    "prepare": "npm run build"
  }
}
```

2. **Build and test:**
```bash
# Build
npm run build

# Test locally
npm link
my-mcp-server

# Or test with npx
npx .
```

### Step 4: Create Claude Desktop Configuration

Generate Claude Desktop config for both uvx/npx and local installations:

**Python (uvx - Recommended):**
```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "uvx",
      "args": ["my-mcp-server"],
      "env": {
        "API_KEY": "your-api-key-here",
        "LOG_LEVEL": "info"
      }
    }
  }
}
```

**Python (local installation):**
```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "/path/to/venv/bin/python",
      "args": ["-m", "my_mcp_server"],
      "env": {
        "API_KEY": "your-api-key-here",
        "LOG_LEVEL": "info"
      }
    }
  }
}
```

**TypeScript (npx - Recommended):**
```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "npx",
      "args": ["-y", "my-mcp-server"],
      "env": {
        "API_KEY": "your-api-key-here",
        "LOG_LEVEL": "info"
      }
    }
  }
}
```

**TypeScript (local installation):**
```json
{
  "mcpServers": {
    "my-mcp-server": {
      "command": "node",
      "args": ["/path/to/project/build/index.js"],
      "env": {
        "API_KEY": "your-api-key-here"
      }
    }
  }
}
```

### Step 5: Create Docker Configuration (if requested)

**Python Dockerfile:**
```dockerfile
FROM python:3.11-slim as builder

WORKDIR /app

# Install build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy and install
COPY pyproject.toml README.md ./
COPY src/ ./src/
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir .

# Production stage
FROM python:3.11-slim

WORKDIR /app

# Copy from builder
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

# Non-root user
RUN useradd -m -u 1000 mcpuser && \
    chown -R mcpuser:mcpuser /app

USER mcpuser

# Health check
HEALTHCHECK --interval=30s --timeout=10s \
    CMD python -c "import sys; sys.exit(0)"

CMD ["my-mcp-server"]
```

**TypeScript Dockerfile:**
```dockerfile
# Build stage
FROM node:18-slim as builder

WORKDIR /app

COPY package*.json tsconfig.json ./
COPY src/ ./src/
RUN npm ci && npm run build && npm prune --production

# Production stage
FROM node:18-slim

WORKDIR /app

COPY --from=builder /app/build ./build
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

# Non-root user
RUN useradd -m -u 1000 mcpuser && \
    chown -R mcpuser:mcpuser /app

USER mcpuser

HEALTHCHECK --interval=30s --timeout=10s \
    CMD node -e "process.exit(0)"

CMD ["node", "build/index.js"]
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  mcp-server:
    build: .
    container_name: my-mcp-server
    restart: unless-stopped
    environment:
      - API_KEY=${API_KEY}
      - LOG_LEVEL=${LOG_LEVEL:-info}
    # For stdio
    stdin_open: true
    tty: true
    # For SSE
    # ports:
    #   - "3000:3000"
    volumes:
      - ./data:/app/data
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### Step 6: Create Environment Configuration

**Create .env.example:**
```bash
# API Keys (REQUIRED)
API_KEY=your-api-key-here
GITHUB_TOKEN=ghp_your_github_token

# Database (if applicable)
DATABASE_URL=postgresql://user:pass@localhost:5432/db

# Server Configuration
SERVER_NAME=my-mcp-server
LOG_LEVEL=info

# Rate Limiting
RATE_LIMIT_REQUESTS=100
RATE_LIMIT_WINDOW=60

# Feature Flags
ENABLE_CACHING=true
CACHE_TTL=300
```

**Create .env for local development:**
```bash
cp .env.example .env
# Edit .env with actual values
```

### Step 7: Create Comprehensive Documentation

**README.md:**
```markdown
# My MCP Server

[Brief description]

## Features

- ✅ [Feature 1]
- ✅ [Feature 2]
- ✅ [Feature 3]

## Installation

### Quick Start (Claude Desktop)

1. Install the server:
   ```bash
   uvx my-mcp-server  # Python
   # or
   npx my-mcp-server  # Node.js
   ```

2. Configure Claude Desktop:

   Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

   ```json
   {
     "mcpServers": {
       "my-server": {
         "command": "uvx",
         "args": ["my-mcp-server"],
         "env": {
           "API_KEY": "your-api-key"
         }
       }
     }
   }
   ```

3. Restart Claude Desktop

### Local Installation

**Python:**
```bash
pip install my-mcp-server
my-mcp-server
```

**Node.js:**
```bash
npm install -g my-mcp-server
my-mcp-server
```

### Docker

```bash
# Build
docker build -t my-mcp-server .

# Run with stdio
docker run -i \
  -e API_KEY=your-key \
  my-mcp-server

# Or use docker-compose
docker-compose up -d
```

## Configuration

### Environment Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| API_KEY | Yes | API key for service | - |
| LOG_LEVEL | No | Logging level | info |
| RATE_LIMIT | No | Requests per minute | 100 |

### Getting API Keys

1. Go to [service provider]
2. Create account
3. Generate API key
4. Add to configuration

## Available Tools

[List and document all tools]

## Troubleshooting

### Server not starting

- Check environment variables are set
- Verify API key is valid
- Check logs for specific errors

### Tools not appearing in Claude

- Restart Claude Desktop
- Check configuration file syntax
- Verify server starts manually

## Development

```bash
# Clone
git clone [repo]

# Install
pip install -e .[dev]  # or npm install

# Run tests
pytest  # or npm test

# Run locally
python -m my_mcp_server  # or npm run dev
```

## License

MIT
```

**TROUBLESHOOTING.md:**
```markdown
# Troubleshooting Guide

## Installation Issues

### Python: Command not found

**Problem**: `my-mcp-server: command not found`

**Solutions**:
1. Verify installation: `pip list | grep my-mcp-server`
2. Check PATH includes pip bin directory
3. Reinstall: `pip install --force-reinstall my-mcp-server`

### Node.js: Module not found

**Problem**: `Cannot find module 'my-mcp-server'`

**Solutions**:
1. Verify installation: `npm list -g my-mcp-server`
2. Reinstall: `npm install -g my-mcp-server`
3. Try with npx: `npx my-mcp-server`

## Configuration Issues

[More troubleshooting content...]
```

### Step 8: Test Deployment

**Local Testing:**

1. **Test package installation:**
```bash
# Python
pip install -e .
my-mcp-server --help

# TypeScript
npm install
npm run build
node build/index.js --help
```

2. **Test with MCP Inspector:**
```bash
mcp-inspector python -m my_mcp_server
# or
mcp-inspector node build/index.js
```

3. **Test in Claude Desktop:**
- Add to configuration
- Restart Claude Desktop
- Verify tools appear
- Test tool execution

**Docker Testing:**

```bash
# Build image
docker build -t my-mcp-server:test .

# Test stdio
docker run -i -e API_KEY=test my-mcp-server:test

# Test with inspector
docker run -i my-mcp-server:test | mcp-inspector -

# Check image size
docker images my-mcp-server:test
```

### Step 9: Create Deployment Checklist

```markdown
# Deployment Checklist

## Pre-Deployment
- [ ] All tests passing
- [ ] Security review complete
- [ ] Documentation complete
- [ ] Version number updated
- [ ] CHANGELOG updated

## Package Configuration
- [ ] pyproject.toml/package.json correct
- [ ] Dependencies specified
- [ ] Entry points configured
- [ ] Build succeeds
- [ ] Package installs locally

## Claude Desktop
- [ ] Config example created
- [ ] Environment variables documented
- [ ] Installation instructions clear
- [ ] Tested with real Claude Desktop

## Docker (if applicable)
- [ ] Dockerfile optimized
- [ ] Image builds successfully
- [ ] Image size reasonable (<500MB)
- [ ] Health check works
- [ ] Non-root user configured
- [ ] docker-compose.yml provided

## Documentation
- [ ] README complete
- [ ] API documentation clear
- [ ] Configuration documented
- [ ] Troubleshooting guide provided
- [ ] Examples included

## Testing
- [ ] Tested locally
- [ ] Tested with MCP Inspector
- [ ] Tested in Claude Desktop
- [ ] Tested with Docker (if applicable)
```

### Step 10: Provide Deployment Summary

```
✓ Deployment Configuration Complete: [project-name]

Local Deployment:
  ✓ Claude Desktop config created
  ✓ Package configuration ready
  ✓ Installation instructions documented

  Installation:
    Python: uvx my-mcp-server
    Node.js: npx my-mcp-server

  Claude Desktop Config:
    Location: ~/Library/Application Support/Claude/claude_desktop_config.json
    Example: ./claude-desktop-config.example.json

Docker Deployment:
  ✓ Dockerfile created (optimized, 180MB)
  ✓ docker-compose.yml created
  ✓ Health checks configured
  ✓ Non-root user configured

  Build: docker build -t my-mcp-server .
  Run: docker-compose up -d

Documentation:
  ✓ README.md complete
  ✓ TROUBLESHOOTING.md created
  ✓ .env.example provided
  ✓ Usage examples included

Next Steps:
1. Test deployment locally:
   - Install: uvx my-mcp-server
   - Configure Claude Desktop
   - Restart Claude Desktop
   - Test tools

2. (Optional) Publish package:
   Python: python -m build && twine upload dist/*
   Node.js: npm publish

3. (Optional) Deploy Docker:
   docker-compose up -d

Files created:
  - claude-desktop-config.example.json
  - Dockerfile
  - docker-compose.yml
  - .env.example
  - README.md
  - TROUBLESHOOTING.md
```

## Success Criteria

- [ ] Deployment configuration created
- [ ] Claude Desktop config example provided
- [ ] Package configuration verified
- [ ] Docker configuration created (if requested)
- [ ] Environment variables documented
- [ ] Comprehensive documentation created
- [ ] Installation tested
- [ ] User has clear deployment instructions

This command provides production-ready deployment configuration for MCP servers across multiple platforms.
