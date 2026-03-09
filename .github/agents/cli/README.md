# CLI-Adapted Agents

This directory contains versions of the agents in `.github/agents/` that have been adapted for use with the **GitHub Copilot CLI**. The originals in the parent directory target VS Code Copilot and reference VS Code-specific tools that are unavailable in the CLI environment.

## What Changed

| VS Code Tool | CLI Replacement |
|---|---|
| `editFiles`, `edit/editFiles` | `edit` |
| `edit/createFile` | `create` |
| `edit/createDirectory` | `bash` (`mkdir`) |
| `runCommands`, `runTasks`, `execute/createAndRunTask` | `bash` |
| `codebase`, `search`, `searchResults`, `usages` | `grep`, `glob` |
| `read`, `findTestFiles` | `view`, `glob` |
| `fetch`, `web/fetch`, `fetch_webpage` | `web_fetch` |
| `githubRepo`, `web/githubRepo`, `github/*` | `github-mcp-server-*` tools |
| `agent`, `runSubagent` | `task` (explore / general-purpose) |
| `todo` | `sql` (built-in `todos` table) |
| `Azure MCP/search`, `youtube/*` | `web_fetch` |
| `changes`, `search/changes` | `bash` (`git diff`) |
| `problems`, `read/problems`, `get_errors` | `bash` (linter / compiler) |
| `testFailure` | `bash` (test runner) |
| Memory instruction file | `store_memory` tool |

## Removed (No CLI Equivalent)

- `extensions` / `vscodeAPI` — VS Code extension API, not applicable in CLI
- `openSimpleBrowser` — VS Code embedded browser
- `terminalSelection` / `terminalLastCommand` — VS Code terminal integration
- `runNotebooks` — Jupyter notebook runner in VS Code
- `handoffs` — VS Code Copilot Chat handoff UI feature

## Agents

| File | Description |
|---|---|
| `beastmode3.1.agent.md` | Autonomous problem-solving agent with deep research and iterative fixing |
| `Function-Map.agent.md` | Generates Mermaid flowcharts of function call relationships |
| `MD-Journal-Mode.agent.md` | Creates and manages markdown journal entries |
| `research.agent.md` | In-depth research with comprehensive source analysis and citation |
