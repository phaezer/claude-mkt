---
description: Initialize new MCP server or client project
argument-hint: Project type (server/client) and language (python/typescript)
---

# Initialize MCP Project

Initialize a new MCP server or client project with proper structure, dependencies, and configuration.

## Usage

```
/mcp-init-project [type] [language]
```

**Arguments:**
- `type`: Project type - either "server" or "client"
- `language`: Programming language - either "python" or "typescript"

**Examples:**
```
/mcp-init-project server python
/mcp-init-project client typescript
/mcp-init-project server typescript
/mcp-init-project client python
```

## What This Command Does

This command creates a complete project structure for an MCP server or client with:
- Proper directory structure
- Configuration files (pyproject.toml, package.json, tsconfig.json)
- Example implementations
- Testing setup
- Development tools configuration
- Documentation templates

## Workflow Steps

### Step 1: Gather Project Requirements

Ask the user for:
1. **Project name**: What should this project be called?
2. **Project description**: Brief description of what it does
3. **Author information**: Name and email
4. **License**: MIT, Apache-2.0, etc. (default: MIT)
5. **Additional features**:
   - For servers: What tools/resources/prompts to include?
   - For clients: Which servers to connect to?

### Step 2: Create Project Structure

Based on project type and language, create the appropriate directory structure:

**Python Server:**
```
project-name/
├── src/
│   └── project_name/
│       ├── __init__.py
│       ├── __main__.py
│       ├── server.py
│       ├── config.py
│       └── tools/
│           └── __init__.py
├── tests/
│   ├── __init__.py
│   └── test_tools.py
├── pyproject.toml
├── README.md
├── LICENSE
├── .env.example
├── .gitignore
└── Dockerfile
```

**TypeScript Server:**
```
project-name/
├── src/
│   ├── index.ts
│   ├── server.ts
│   ├── config.ts
│   └── tools/
│       └── index.ts
├── tests/
│   └── tools.test.ts
├── package.json
├── tsconfig.json
├── jest.config.js
├── README.md
├── LICENSE
├── .env.example
├── .gitignore
└── Dockerfile
```

### Step 3: Generate Configuration Files

Create all necessary configuration files:

**Python (pyproject.toml):**
- Use hatchling build system
- Set Python >= 3.11 requirement
- Include fastmcp or mcp SDK dependency
- Add dev dependencies (pytest, black, ruff)
- Configure entry point script

**TypeScript (package.json + tsconfig.json):**
- Set CommonJS module system
- Use @modelcontextprotocol/sdk
- Include zod for validation
- Add dev dependencies (typescript, jest, ts-jest)
- Configure build scripts

### Step 4: Create Example Implementation

**For Servers:**
- Create example tool (e.g., echo, create_file)
- Create example resource (e.g., file:// resource)
- Create example prompt template
- Set up proper error handling
- Add input validation

**For Clients:**
- Create connection setup code
- Add tool listing/calling examples
- Add resource reading examples
- Implement error handling
- Add retry logic

### Step 5: Set Up Testing

**Python:**
- Create pytest configuration
- Add conftest.py with shared fixtures
- Create example unit tests for tools
- Add integration test template

**TypeScript:**
- Create jest.config.js
- Add example unit tests
- Set up test helpers
- Add integration test template

### Step 6: Create Documentation

Create comprehensive README.md with:
- Project description
- Features list
- Installation instructions
- Configuration guide
- Usage examples
- Development setup
- Testing instructions

### Step 7: Add Development Tools

**Create files:**
- `.gitignore` (exclude node_modules, build, .env, etc.)
- `.env.example` (template for environment variables)
- `Dockerfile` (for containerization)
- `LICENSE` (with chosen license text)

**Python additional:**
- `.python-version` or `pyproject.toml` tool config for black, ruff

**TypeScript additional:**
- `.prettierrc` (code formatting)
- `.eslintrc.json` (linting)

### Step 8: Initialize Git Repository

```bash
git init
git add .
git commit -m "Initial commit: Set up [project-name] MCP [server/client]"
```

### Step 9: Provide Next Steps

Give the user clear instructions on:

1. **Install dependencies:**
   ```bash
   # Python
   pip install -e .[dev]

   # TypeScript
   npm install
   ```

2. **Configure environment:**
   ```bash
   cp .env.example .env
   # Edit .env with your API keys
   ```

3. **Run tests:**
   ```bash
   # Python
   pytest

   # TypeScript
   npm test
   ```

4. **Run development server:**
   ```bash
   # Python
   python -m project_name

   # TypeScript
   npm run dev
   ```

5. **For servers, test with MCP Inspector:**
   ```bash
   # Python
   mcp-inspector python -m project_name

   # TypeScript
   mcp-inspector node build/index.js
   ```

6. **For servers, add to Claude Desktop:**
   - Edit `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Add server configuration
   - Restart Claude Desktop

## Example Output

```
Created MCP Server project: my-github-tools

✓ Created project structure
✓ Generated pyproject.toml
✓ Created example tools (create_issue, list_repos)
✓ Set up pytest testing
✓ Created README.md
✓ Added .gitignore and .env.example
✓ Created Dockerfile
✓ Initialized git repository

Next steps:
1. cd my-github-tools
2. pip install -e .[dev]
3. cp .env.example .env
4. Edit .env and add your GITHUB_TOKEN
5. pytest  # Run tests
6. python -m my_github_tools  # Start server

To test with MCP Inspector:
mcp-inspector python -m my_github_tools

To add to Claude Desktop, edit:
~/Library/Application Support/Claude/claude_desktop_config.json

Add:
{
  "mcpServers": {
    "github-tools": {
      "command": "uvx",
      "args": ["my-github-tools"],
      "env": {
        "GITHUB_TOKEN": "your-token-here"
      }
    }
  }
}
```

## Template Files

The command should create these template files with proper content:

### Example Python Server Tool

```python
from fastmcp import FastMCP

mcp = FastMCP("example-server")

@mcp.tool()
def echo(message: str) -> dict:
    """Echoes back the input message.

    Args:
        message: Message to echo

    Returns:
        dict with echoed message
    """
    return {
        "success": True,
        "message": message
    }
```

### Example TypeScript Server Tool

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { Tool } from "@modelcontextprotocol/sdk/types.js";

const echoTool: Tool = {
  name: "echo",
  description: "Echoes back the input message",
  inputSchema: {
    type: "object",
    properties: {
      message: {
        type: "string",
        description: "Message to echo"
      }
    },
    required: ["message"]
  }
};
```

## Success Criteria

- [ ] Project structure created
- [ ] All configuration files generated
- [ ] Example implementations added
- [ ] Tests set up and passing
- [ ] Documentation complete
- [ ] Git repository initialized
- [ ] User has clear next steps

This command sets up a production-ready MCP project foundation that follows best practices and is ready for development.
