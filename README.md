# LLM Cost Optimizer MCP Skill

This folder is safe to publish separately from the private app repo. It contains the portable Codex skill package plus MCP setup snippets for other clients.

> [!IMPORTANT]
> The `llm-cost-optimizer-mcp` folder is a Codex skill. Cursor, Claude, Antigravity, and most other MCP clients do not install Codex skills directly. They connect to the MCP server over HTTP instead.

## Contents

- `skill.json`: repo-level metadata for the public skill package
- `llm-cost-optimizer-mcp/`: the Codex skill folder that users install

## Runtime Requirement

The MCP endpoint is already fixed to the deployed production server. Users only need a bearer token.

- Required from user: `PROMPT_OPTIMIZER_MCP_API_KEY`
- Fixed server endpoint used by all examples: `https://llmcostoptimizer.com/api/mcp`

## Codex

Check whether Codex CLI is already installed:

### macOS / Linux

~~~bash
command -v codex
codex --help
~~~

### Windows PowerShell

~~~powershell
Get-Command codex -ErrorAction SilentlyContinue
codex --help
~~~

If `codex` is not found, install it first.

### Install Codex CLI on macOS / Linux

~~~bash
npm install -g @openai/codex
command -v codex
codex --help
~~~

### Install Codex CLI on Windows PowerShell

~~~powershell
npm install -g @openai/codex
Get-Command codex -ErrorAction SilentlyContinue
codex --help
~~~

Manual install on Windows PowerShell:

~~~powershell
$repoUrl = "https://github.com/<owner>/<repo>.git"
$tmpDir = Join-Path $env:TEMP "llm-cost-optimizer-mcp-skill"
if (Test-Path $tmpDir) { Remove-Item -LiteralPath $tmpDir -Recurse -Force }
git clone $repoUrl $tmpDir
New-Item -ItemType Directory -Force (Join-Path $HOME ".codex\skills") | Out-Null
Copy-Item -LiteralPath (Join-Path $tmpDir "llm-cost-optimizer-mcp") -Destination (Join-Path $HOME ".codex\skills\llm-cost-optimizer-mcp") -Recurse -Force
~~~

Register the MCP server in Codex:

### MacOS / Linux

~~~bash
export PROMPT_OPTIMIZER_MCP_API_KEY="your_bearer_token_here"
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
~~~

### Windows powerShell

~~~powershell
$env:PROMPT_OPTIMIZER_MCP_API_KEY = "your_bearer_token_here"
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
~~~

For new VS Code or Codex sessions on Windows, persist the token at user scope and restart the app after setting it:

~~~powershell
[Environment]::SetEnvironmentVariable("PROMPT_OPTIMIZER_MCP_API_KEY", "your_bearer_token_here", "User")
~~~

### Windows Command Prompt

~~~bat
set PROMPT_OPTIMIZER_MCP_API_KEY=your_bearer_token_here
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
~~~

If `npm install -g @openai/codex` succeeds but `codex` is still not found, restart the terminal or add your npm global bin directory to `PATH`.

~~~bash
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
~~~

## Cursor

Add this to `~/.cursor/mcp.json` or your project `.cursor/mcp.json`:

~~~json
{
  "mcpServers": {
    "llm-cost-optimizer": {
      "url": "https://llmcostoptimizer.com/api/mcp",
      "headers": {
        "Authorization": "Bearer ${env:PROMPT_OPTIMIZER_MCP_API_KEY}"
      }
    }
  }
}
~~~

Optional verification:

~~~bash
cursor-agent mcp list
~~~

## Claude Code

Register the remote MCP server:

~~~bash
claude mcp add --transport http --scope user --header "Authorization: Bearer ${PROMPT_OPTIMIZER_MCP_API_KEY}" llm-cost-optimizer https://llmcostoptimizer.com/api/mcp
~~~

Project-scoped alternative in `.mcp.json`:

~~~json
{
  "mcpServers": {
    "llm-cost-optimizer": {
      "type": "http",
      "url": "https://llmcostoptimizer.com/api/mcp",
      "headers": {
        "Authorization": "Bearer ${PROMPT_OPTIMIZER_MCP_API_KEY}"
      }
    }
  }
}
~~~

## Antigravity

Edit the raw MCP config file:

- macOS/Linux: `~/.gemini/antigravity/mcp_config.json`
- Windows: `C:\\Users\\<USERNAME>\\.gemini\\antigravity\\mcp_config.json`

~~~json
{
  "mcpServers": {
    "llm-cost-optimizer": {
      "serverUrl": "https://llmcostoptimizer.com/api/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_PROMPT_OPTIMIZER_MCP_API_KEY"
      }
    }
  }
}
~~~

## Other MCP Clients

Any client that supports remote streamable HTTP MCP can reuse the same production endpoint and bearer token. Configure:

- URL: `https://llmcostoptimizer.com/api/mcp`
- Header: `Authorization: Bearer <token>`
