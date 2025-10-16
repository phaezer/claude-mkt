---
description: Test MCP server or client functionality
argument-hint: Path to MCP project to test
---

# Test MCP Server or Client

Run comprehensive tests on an MCP server or client implementation.

## Usage

```
/test-mcp [project-path]
```

**Arguments:**
- `project-path`: Path to MCP project directory (optional, defaults to current directory)

**Examples:**
```
/test-mcp
/test-mcp ./my-mcp-server
/test-mcp ../github-mcp-client
```

## What This Command Does

This command runs a comprehensive test suite on an MCP implementation including:
- Unit tests for tools/resources/prompts
- Integration tests with MCP Inspector
- Protocol compliance validation
- Error scenario testing
- Performance testing
- Coverage analysis

## Workflow Steps

### Step 1: Detect Project Type

Analyze the project to determine:
1. **Type**: Server or Client?
2. **Language**: Python or TypeScript?
3. **Test Framework**: pytest, jest, vitest?
4. **Existing Tests**: Are tests already present?

**Detection Logic:**
- Check for `pyproject.toml`, `package.json`
- Look for test directories (`tests/`, `test/`, `__tests__/`)
- Check for test configuration files (`pytest.ini`, `jest.config.js`)
- Identify server vs client by inspecting imports/code

### Step 2: Check Test Environment

Verify testing prerequisites:

**Python:**
- [ ] pytest installed
- [ ] pytest-asyncio installed (if async tests)
- [ ] pytest-cov installed (for coverage)
- [ ] Mock dependencies available
- [ ] Virtual environment activated

**TypeScript:**
- [ ] jest or vitest installed
- [ ] @types packages installed
- [ ] ts-jest configured (if using jest)
- [ ] Mock libraries available

**Both:**
- [ ] MCP Inspector available (`npx @modelcontextprotocol/inspector`)
- [ ] Environment variables configured
- [ ] External dependencies available or mocked

### Step 3: Run Unit Tests

**Python:**
```bash
# Run all unit tests
pytest tests/unit -v

# With coverage
pytest tests/unit --cov=src --cov-report=term-missing --cov-report=html
```

**TypeScript:**
```bash
# Run all unit tests
npm test -- tests/unit

# With coverage
npm test -- --coverage
```

**Report Results:**
- Total tests run
- Passed / Failed / Skipped
- Test execution time
- Any failures with details

### Step 4: Run Integration Tests

**Python:**
```bash
# Run integration tests
pytest tests/integration -v --timeout=30
```

**TypeScript:**
```bash
# Run integration tests
npm test -- tests/integration --testTimeout=30000
```

**Report Results:**
- Integration test count
- Pass/fail status
- Connection tests results
- External API test results (if any)

### Step 5: Test with MCP Inspector

**For Servers:**
```bash
# Python server
npx @modelcontextprotocol/inspector python -m project_name

# TypeScript server
npx @modelcontextprotocol/inspector node build/index.js
```

**Validate:**
- [ ] Server starts successfully
- [ ] Protocol handshake completes
- [ ] Tools are discoverable
- [ ] Resources are discoverable
- [ ] Prompts are discoverable
- [ ] Tool calls execute correctly
- [ ] Resource reads work
- [ ] Prompt generation works
- [ ] Error handling is appropriate

**For Clients:**
```bash
# Start mock MCP server
python tests/mock_server.py &  # or node tests/mockServer.js &

# Test client against mock
pytest tests/integration/test_inspector.py
```

### Step 6: Test Error Scenarios

Run tests specifically for error handling:

**Common Error Scenarios:**
```python
# Python example
@pytest.mark.error_scenarios
class TestErrorHandling:
    def test_invalid_tool_parameters(self):
        """Test tool with invalid parameters"""

    def test_missing_required_fields(self):
        """Test missing required fields"""

    def test_type_validation_errors(self):
        """Test type validation"""

    def test_connection_timeout(self):
        """Test connection timeout handling"""

    def test_server_unavailable(self):
        """Test server unavailable scenario"""

    def test_rate_limit_exceeded(self):
        """Test rate limiting"""
```

**Run Error Tests:**
```bash
pytest tests/ -m error_scenarios -v
```

### Step 7: Performance Testing

Test performance characteristics:

**For Servers:**
```python
import time

def test_tool_response_time():
    """Ensure tools respond within acceptable time"""
    start = time.time()
    result = mcp.call_tool("expensive_operation", params)
    duration = time.time() - start

    assert duration < 5.0, f"Tool too slow: {duration}s"
    assert result["success"]

def test_concurrent_requests():
    """Test handling concurrent requests"""
    import asyncio

    async def call_tool():
        return await mcp.call_tool("test_tool", {})

    tasks = [call_tool() for _ in range(10)]
    results = await asyncio.gather(*tasks)

    assert all(r["success"] for r in results)
```

**Run Performance Tests:**
```bash
pytest tests/ -m performance -v
```

### Step 8: Generate Coverage Report

**Python:**
```bash
# Generate HTML coverage report
pytest --cov=src --cov-report=html --cov-report=term-missing

# View report
open htmlcov/index.html
```

**TypeScript:**
```bash
# Generate coverage report
npm test -- --coverage

# View report
open coverage/lcov-report/index.html
```

**Analyze Coverage:**
- Overall coverage percentage
- Uncovered lines/functions
- Coverage by module
- Critical paths coverage

### Step 9: Check Protocol Compliance

Verify MCP protocol compliance:

**Checklist:**
- [ ] Correct protocol version advertised
- [ ] Proper capability negotiation
- [ ] Valid tool schemas (JSON Schema compliant)
- [ ] Valid resource URI patterns
- [ ] Proper error response format
- [ ] Correct message types used
- [ ] Proper request/response correlation

**Run Compliance Tests:**
```bash
# Using MCP Inspector
npx @modelcontextprotocol/inspector --validate python -m project_name
```

### Step 10: Generate Test Report

Create comprehensive test report:

```markdown
# Test Report: [Project Name]

**Date**: [date]
**Version**: [version]
**Test Duration**: [duration]

## Summary

- **Total Tests**: [count]
- **Passed**: [count] ✓
- **Failed**: [count] ✗
- **Skipped**: [count] ⊘
- **Coverage**: [percentage]%

## Unit Tests

### Tools
- Total: [count]
- Passed: [count]
- Failed: [count]
- Coverage: [percentage]%

### Resources
- Total: [count]
- Passed: [count]
- Failed: [count]
- Coverage: [percentage]%

### Prompts
- Total: [count]
- Passed: [count]
- Failed: [count]
- Coverage: [percentage]%

## Integration Tests

- **MCP Inspector**: [PASS/FAIL]
- **Protocol Compliance**: [PASS/FAIL]
- **Tool Execution**: [PASS/FAIL]
- **Resource Access**: [PASS/FAIL]
- **Error Handling**: [PASS/FAIL]

## Performance Tests

- **Average Tool Response**: [time]ms
- **Concurrent Requests**: [PASS/FAIL]
- **Memory Usage**: [usage]MB
- **Connection Stability**: [PASS/FAIL]

## Failed Tests

[If any tests failed, list them with details]

### test_create_file_invalid_path
**Location**: tests/unit/test_tools.py:45
**Error**: AssertionError: Expected error message not returned
**Details**: [detailed error message]

## Coverage Analysis

### Overall Coverage: [percentage]%

| Module | Coverage |
|--------|----------|
| tools.py | [percentage]% |
| resources.py | [percentage]% |
| prompts.py | [percentage]% |
| config.py | [percentage]% |

### Uncovered Code

Lines not covered by tests:
- src/tools.py:123-145 (error handling branch)
- src/resources.py:67 (cache invalidation)

## Recommendations

1. [recommendation 1]
2. [recommendation 2]
3. [recommendation 3]

## Next Steps

- [ ] Fix failed tests
- [ ] Increase coverage for [module]
- [ ] Add performance benchmarks
- [ ] Add more error scenario tests
```

### Step 11: Provide Test Summary

Give the user a clear summary:

```
✓ MCP Testing Complete: [project-name]

Test Results:
  Unit Tests: 45/45 passed ✓
  Integration Tests: 8/8 passed ✓
  MCP Inspector: PASS ✓
  Protocol Compliance: PASS ✓
  Performance: PASS ✓

Coverage: 87%
  - Tools: 92%
  - Resources: 85%
  - Prompts: 90%
  - Config: 75%

Duration: 12.3 seconds

[Issues Found: None]
or
[Issues Found: 2]
  1. test_database_timeout (HIGH) - Timeout not handled
  2. test_large_file_resource (MEDIUM) - Memory issue

Recommendations:
  ✓ Coverage is good (>80%)
  ⚠ Add timeout handling for database operations
  ⚠ Implement streaming for large resources

View detailed report: ./test-report.html
View coverage report: ./htmlcov/index.html
```

## Automated Testing in CI/CD

If `.github/workflows` exists, offer to add/update test workflow:

```yaml
name: Test MCP Server

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python/Node
        uses: actions/setup-python@v4  # or setup-node@v3
        with:
          python-version: '3.11'  # or node-version: '18'

      - name: Install dependencies
        run: |
          pip install -e .[dev]  # or npm install

      - name: Run tests
        run: |
          pytest --cov=src --cov-report=xml  # or npm test -- --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
```

## Success Criteria

- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] MCP Inspector validation passed
- [ ] Protocol compliance verified
- [ ] Coverage >= 80%
- [ ] No critical issues found
- [ ] Performance within acceptable limits
- [ ] Test report generated

This command provides comprehensive testing to ensure MCP implementations are reliable, compliant, and production-ready.
