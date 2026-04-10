# LLM Cost Optimizer MCP

Use MCP tools for prompt-heavy tasks: analyze prompt waste first, then optimize, estimate cost, or compare models. Treat degraded results as suggest-only output.

This export is meant for a separate public repository. It contains the portable Codex skill plus repo-level metadata for installers and automation.

## Contents

- `skill.json`: repo-level metadata for the public skill package
- `llm-cost-optimizer-mcp/`: the Codex skill folder that users install

## Publish

1. Create a public GitHub repository for this exported folder.
2. Copy these files into that repository root.
3. Replace any placeholder repository URLs if you did not pass `-PublicRepoUrl`.
4. Push the repository.

## Runtime Requirement

The MCP endpoint is already fixed to the deployed production server. Users only need a bearer token.

- Required env var: `PROMPT_OPTIMIZER_MCP_API_KEY`
- Remote MCP endpoint: `https://llmcostoptimizer.com/api/mcp`

## Codex

Check whether Codex CLI is available in your terminal before installing the skill.

### macOS

```bash
command -v codex
which codex
codex --help
```

### Linux

```bash
command -v codex
which codex
codex --help
```

### Windows PowerShell

```powershell
Get-Command codex -ErrorAction SilentlyContinue
codex --help
```

### Windows Command Prompt

```bat
where codex
codex --help
```

If these commands do not find `codex`, install the Codex CLI first or add its install location to your `PATH`.

Install the skill with Codex skill installer:

```text
Use `$skill-installer to install https://github.com/MindcrackerInc/llm-cost-optimizer-codex-skill/tree/main/llm-cost-optimizer-mcp
```

Manual install on Windows PowerShell:

```powershell
$repoUrl = "https://github.com/MindcrackerInc/llm-cost-optimizer-codex-skill.git"
$tmpDir = Join-Path $env:TEMP "llm-cost-optimizer-mcp-skill"
if (Test-Path $tmpDir) { Remove-Item -LiteralPath $tmpDir -Recurse -Force }
git clone $repoUrl $tmpDir
New-Item -ItemType Directory -Force (Join-Path $HOME ".codex\skills") | Out-Null
Copy-Item -LiteralPath (Join-Path $tmpDir "llm-cost-optimizer-mcp") -Destination (Join-Path $HOME ".codex\skills\llm-cost-optimizer-mcp") -Recurse -Force
```

Register the MCP server in Codex:

### macOS / Linux

```bash
export PROMPT_OPTIMIZER_MCP_API_KEY="your_bearer_token_here"
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
```

### Windows PowerShell

```powershell
$env:PROMPT_OPTIMIZER_MCP_API_KEY = "your_bearer_token_here"
codex mcp add llm-cost-optimizer --url https://llmcostoptimizer.com/api/mcp --bearer-token-env-var PROMPT_OPTIMIZER_MCP_API_KEY
```

## Cursor

Add this to `~/.cursor/mcp.json` or your project `.cursor/mcp.json`:

```json
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
```

## Claude Code

Register the remote MCP server:

```bash
claude mcp add --transport http --scope user --header "Authorization: Bearer ${PROMPT_OPTIMIZER_MCP_API_KEY}" llm-cost-optimizer https://llmcostoptimizer.com/api/mcp
```

Project-scoped alternative in `.mcp.json`:

```json
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
```

## Antigravity

Edit the raw MCP config file:

- macOS/Linux: `~/.gemini/antigravity/mcp_config.json`
- Windows: `C:\Users\<USERNAME>\.gemini\antigravity\mcp_config.json`

```json
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
```

## Other MCP Clients

Any client that supports remote streamable HTTP MCP can reuse the same production endpoint and bearer token.

- URL: `https://llmcostoptimizer.com/api/mcp`
- Header: `Authorization: Bearer <token>`

