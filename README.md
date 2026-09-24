# testRigor MCP Server

Connect AI assistants to [testRigor](https://testrigor.com) for managing test suites, running tests, viewing results, and automating QA workflows — all through natural language.

## Available Tools

The testRigor MCP server provides 49 tools organized into seven categories:

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
| `update_test_case_dataset` | Set (or change) which data set an existing test case uses | `testSuiteId`, `testCaseUuid`, `datasetId` |

### Global Variables

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_global_variables` | List global variables in a suite (paginated). Also called "Test Data" in the testRigor UI. Optionally filter to variables referenced by one test case | `testSuiteId`, `testCaseUuid`?, `page`?, `pageSize`? |
| `get_global_variable` | Get a single global variable by key | `testSuiteId`, `key` |
| `create_global_variable` | Create a new global variable in a suite | `testSuiteId`, `key`, `value`, `path`?, `type` |
| `update_global_variable_value` | Replace the value of an existing global variable, by key | `testSuiteId`, `key`, `value`, `path`? |
| `get_file_upload_url` | Get a one-time URL to upload a file to, for use as a FILE-type global variable or data set value | `testSuiteId`, `fileName` |
| `create_certificate_global_variable` | Create a CLIENT_CERTIFICATE-type global variable from a .pfx certificate's content, base64-encoded | `testSuiteId`, `key`, `fileName`, `certBase64`, `password` |
| `update_certificate_global_variable` | Replace an existing CLIENT_CERTIFICATE-type global variable's certificate | `testSuiteId`, `key`, `fileName`, `certBase64`, `password` |
| `rename_global_variable` | Rename a global variable, by key. Rejected by default if referenced by a test case or rule; pass `renameMode` to update every reference automatically or to rename anyway and break them | `testSuiteId`, `key`, `newKey`, `renameMode`? |
| `delete_global_variable` | Delete a global variable, by key. Rejected by default if still referenced by a test case or rule; pass `deletionMode` to delete anyway | `testSuiteId`, `key`, `deletionMode`? |

### Data Sets

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_data_sets` | List data sets in a suite (paginated summaries: schema and row count, no row values) | `testSuiteId`, `page`?, `pageSize`? |
| `get_data_set` | Get a data set's full schema and row values | `testSuiteId`, `datasetId` |
| `create_data_set` | Create a new data set with optional variables and rows | `testSuiteId`, `name`, `description`?, `variables`?, `rows`? |
| `add_data_set_rows` | Add new rows to an existing data set | `testSuiteId`, `datasetId`, `rows` |
| `update_data_set_rows` | Update existing rows in a data set, by row id | `testSuiteId`, `datasetId`, `rows` |
| `add_data_set_variable` | Add a new variable (column) to an existing data set, with a value for every existing row | `testSuiteId`, `datasetId`, `name`, `type`, `existingRowValues` |
| `update_data_set` | Rename a data set and/or update its description | `testSuiteId`, `datasetId`, `name`?, `description`? |
| `delete_data_set_rows` | Delete one or more rows from a data set, by row id | `testSuiteId`, `datasetId`, `rowIds` |
| `rename_data_set_variable` | Rename a variable (column) on a data set. Rejected by default if referenced by a test case or rule; pass `renameMode` to update every reference automatically or to rename anyway and break them | `testSuiteId`, `datasetId`, `name`, `newName`, `renameMode`? |
| `delete_data_set_variable` | Delete a variable (column) from a data set, along with its values on every row. Rejected by default if still referenced by a test case or rule; pass `deletionMode` to delete anyway | `testSuiteId`, `datasetId`, `name`, `deletionMode`? |
| `delete_data_set` | Delete a data set. Rejected by default if linked to any test case; pass `deletionMode` to unlink every linked test case and delete in one call | `testSuiteId`, `datasetId`, `deletionMode`? |
| `unlink_data_set_from_test_case` | Remove a data set's link from a test case without deleting the data set itself | `testSuiteId`, `testCaseUuid`, `datasetId`? |

### Reusable Rules

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_rules` | List reusable rules in a suite (paginated), or all rules used by a specific test case | `testSuiteId`, `testCaseUuid`?, `page`?, `pageSize`? |
| `get_rule` | Get a single reusable rule by name | `testSuiteId`, `name` |
| `create_rule` | Create a new reusable rule, optionally inside a folder | `testSuiteId`, `name`, `steps`, `labels`?, `precondition`?, `parentFolderId`? |
| `update_rule_steps` | Replace the steps of an existing reusable rule | `testSuiteId`, `name`, `steps` |
| `rename_rule` | Rename a reusable rule. Rejected by default if referenced by a test case or another rule; pass `renameMode` to update every reference automatically or to rename anyway and break them | `testSuiteId`, `name`, `newName`, `renameMode`? |
| `delete_rule` | Delete a reusable rule, by name. Rejected by default if referenced by a test case or another rule; pass `deletionMode` to delete anyway | `testSuiteId`, `name`, `deletionMode`? |
| `list_rule_folders` | List rule folders in a suite, optionally under a parent folder or filtered by name | `testSuiteId`, `parentFolderId`?, `nameFilter`? |
| `get_rule_folder` | Get a single rule folder's details, by folder id | `testSuiteId`, `folderId` |
| `create_rule_folder` | Create a new rule folder, optionally inside a parent folder | `testSuiteId`, `name`, `parentFolderId`? |
| `rename_rule_folder` | Rename an existing rule folder | `testSuiteId`, `folderId`, `newName` |
| `move_rule_folder` | Move a rule folder to a new parent folder (or to the root) | `testSuiteId`, `folderId`, `newParentFolderId`? |
| `delete_rule_folder` | Delete an empty rule folder | `testSuiteId`, `folderId` |
| `move_rule` | Move a reusable rule to a different folder (or to the root) | `testSuiteId`, `name`, `newFolderId`? |

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
5. You should see **testrigor** listed with a green indicator and 38 tools available

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

### Managing Global Variables and Data Sets

- *"What's the value of the `apiBaseUrl` global variable in this suite?"*
- *"Upload this image and save it as a FILE-type global variable called `logo`"*
- *"Create a simple data set with a couple of test values and link it to a test case so it pulls its input from there instead of hardcoded values"*

### Managing Reusable Rules

- *"Two test cases both start with the same setup step — extract it into a rule and update both test cases to call it instead of repeating the line"*
- *"Organize my rules into folders by feature area"*

## `server.json`

For MCP server registries and discovery:

```json
{
  "$schema": "https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json",
  "name": "com.testrigor/mcp-server",
  "description": "Manage test suites, run tests, view results, and automate QA workflows via AI with testRigor.",
  "version": "1.1.0",
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
