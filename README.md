# testRigor MCP Server

Connect AI assistants to [testRigor](https://testrigor.com) for managing test suites, running tests, viewing results, and automating QA workflows — all through natural language.

## Available Tools

The testRigor MCP server provides 14 tools organized into four categories:

### Test Suite Management

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_test_suites` | List all test suites accessible by the authenticated user | *(none)* |
| `get_test_suite` | Get metadata for a single test suite (no passwords or API tokens exposed) | `testSuiteId` |

### Test Case Management

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_test_cases` | List test cases in a suite (paginated). Supports filtering by label or searching by description | `testSuiteId`, `label`?, `search`?, `page`?, `pageSize`? |
| `get_test_case` | Get a single test case with its full steps | `testSuiteId`, `testCaseUuid` |
| `list_test_case_runs` | List recent run history for a specific test case (most recent first). Returns status, timing, and `flowUuid` for each run | `testSuiteId`, `testCaseUuid`, `page`?, `pageSize`? |

### Test Execution

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `run_test_cases` | Start a test run for specific test cases by UUID (max 100) | `testSuiteId`, `testCaseUuids` |
| `run_green_regression` | Re-run all previously passed (green) test cases from the baseline run | `testSuiteId` |
| `execute_supplied_test_steps` | Execute ad-hoc test steps as a new scenario without saving a test case | `testSuiteId`, `steps`, `scenarioName`? |
| `cancel_task` | Cancel a running task | `testSuiteId`, `taskId` |

### Run Results & Analysis

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_runs` | List test runs for a suite (paginated). Can filter by status | `testSuiteId`, `status`?, `page`?, `pageSize`? |
| `get_run` | Get summary and status for a single test run | `testSuiteId`, `taskId` |
| `list_run_test_cases` | List all test case results in a run with status and execution details. Use the returned `flowUuid` to drill into step-level details | `testSuiteId`, `taskId`, `page`?, `pageSize`? |
| `list_run_failures` | List only the failed and failed-to-start test cases in a run | `testSuiteId`, `taskId`, `page`?, `pageSize`? |
| `get_execution_details` | Get per-step execution results with optional screenshot URLs for a test case flow | `testSuiteId`, `taskId`, `testCaseUuid`, `flowUuid`, `includeScreenshots` |

> Parameters marked with `?` are optional.

### Common Workflows

A typical investigation flow looks like this:

```
list_test_suites → list_runs → list_run_failures → get_execution_details
```

To drill into a specific test case:

```
list_test_cases → get_test_case → list_test_case_runs → get_execution_details
```

To run tests and monitor results:

```
run_test_cases → get_run (poll for status) → list_run_test_cases → get_execution_details
```

## Prerequisites

You need a **Personal Access Token (PAT)** to authenticate with the MCP server.

### Generating a PAT

1. Log in to [testRigor](https://app.testrigor.com)
2. Click your name (top-right) → **API Tokens**
3. Click **Generate New Token**
4. Enter a **Token Name** (e.g. `MCP.TOKEN`)
5. Set **Expiration Days** (e.g. `180 days`)
6. Click **Generate Token**
7. Copy the token immediately — you will not be able to view it again after closing the dialog
8. Store it securely (e.g., in a password manager or environment variable)

## Setup

### Cursor

1. Open Cursor Settings → **MCP**
2. Click **Add new global MCP server**
3. Paste the following into the `mcp.json` file:

```json
{
  "mcpServers": {
    "testrigor": {
      "url": "https://api2.testrigor.com/api/v1/mcp",
      "headers": {
        "personal-access-token": "YOUR_PAT_HERE"
      }
    }
  }
}
```

4. Save — Cursor will automatically connect to the server
5. You should see **testrigor** listed with a green indicator and 14 tools available

### Claude Code (CLI)

```bash
claude mcp add --transport http testrigor https://api2.testrigor.com/api/v1/mcp \
  --header "personal-access-token: YOUR_PAT_HERE"
```

### Claude Desktop

Claude Desktop supports remote MCP servers through the **Connectors** UI:

1. Go to **Settings → Connectors**
2. Click **Add Integration**
3. Enter the MCP server URL: `https://api2.testrigor.com/api/v1/mcp`

> **Note:** Claude Desktop connectors have limited support for custom header authentication. If you encounter issues, use the `npx mcp-remote` bridge as a workaround:

```json
{
  "mcpServers": {
    "testrigor": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://api2.testrigor.com/api/v1/mcp",
        "--header",
        "personal-access-token: YOUR_PAT_HERE"
      ]
    }
  }
}
```

Config file locations:
- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

Restart Claude Desktop after saving.

### Windsurf

Edit your MCP config file:

- **macOS/Linux:** `~/.codeium/windsurf/mcp_config.json`
- **Windows:** `%USERPROFILE%\.codeium\windsurf\mcp_config.json`

```json
{
  "mcpServers": {
    "testrigor": {
      "serverUrl": "https://api2.testrigor.com/api/v1/mcp",
      "headers": {
        "personal-access-token": "YOUR_PAT_HERE"
      }
    }
  }
}
```

> **Note:** Windsurf uses `serverUrl` instead of `url` for remote servers.

## Usage Examples

Once connected, you can interact with testRigor using natural language. Your AI assistant will automatically choose the right tool.

### Browsing Test Suites and Cases

- *"List all my test suites"*
- *"Show me the test cases in the Login suite"*
- *"Get the details of the checkout test case"*

### Running Tests

- *"Run the 'login validation' test case in the Smoke Tests suite"*
- *"Run green regression for the Payment suite"*
- *"Execute these steps in the Checkout suite: go to url 'https://example.com', click 'Sign In', check that page contains 'Welcome'"*

### Investigating Results

- *"Show me the runs for the Login suite"*
- *"Are there any failures in the latest run?"*
- *"Show me the step-by-step execution details for the login test case from the latest run"*

### Managing Runs

- *"Cancel the running task"*
- *"What's the status of the current run?"*

## `server.json`

For MCP server registries and discovery:

```json
{
  "$schema": "https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json",
  "name": "com.testrigor/mcp-server",
  "description": "Manage test suites, run tests, view results, and automate QA workflows via AI with testRigor.",
  "version": "1.0.0",
  "remotes": [
    {
      "type": "streamable-http",
      "url": "https://api2.testrigor.com/api/v1/mcp",
      "headers": [
        {
          "name": "personal-access-token",
          "description": "Your testRigor Personal Access Token (PAT). Generate one at https://app.testrigor.com under Settings > Personal Access Tokens.",
          "isSecret": true,
          "isRequired": true
        }
      ]
    }
  ]
}
```

## Support

- Email: [support@testrigor.com](mailto:support@testrigor.com)
- Website: [testrigor.com](https://testrigor.com)
- Documentation: [testrigor.com/docs](https://testrigor.com/docs)
