# Coree Codex Plugin

[Coree](https://github.com/coree-ai/coree) provides persistent memory and code intelligence for AI agents. This plugin integrates Coree into the OpenAI Codex CLI as an MCP server.

## Features

- **Persistent Memory**: Stores decisions, architectural discoveries, and gotchas across sessions.
- **Code Intelligence**: Hybrid search over source code and git history.

## Installation

Register the marketplace source and install the plugin:

```bash
codex plugin marketplace add github:coree-ai/codex
codex plugin install coree
```

## Sandbox configuration

Codex sandboxes MCP server processes. Coree needs network access (for first-run
model download and remote sync) and filesystem write access (for its database,
model cache, and npx cache). Add this to `~/.codex/config.toml`, substituting
your username and paths:

```toml
[sandbox_workspace_write]
network_access = true
writable_roots = [
  "/home/you/.cache/coree",
  "/home/you/.local/share/coree",
  "/home/you/.npm/_npx",
  "/home/you/.npm/_cacache"
]
```

Use absolute paths - `~` expansion is not reliable in TOML. If your npm cache
lives elsewhere (e.g. `$XDG_CACHE_HOME` / `$XDG_DATA_HOME`), adjust accordingly.

## Environment variables

If you use coree's remote sync (Turso), the following env vars must be set in
your shell so Codex forwards them to the MCP process:

- `COREE__MEMORY__REMOTE_AUTH_TOKEN`
- `COREE__MEMORY__REMOTE_URL`

The plugin's `.mcp.json` already lists these in `env_vars` so Codex will forward
them if they are present in your shell environment.

## Hooks

> **Note**: Codex hooks (`[hooks.*]` in `config.toml`) could push coree context
> into the model at session start and before each prompt, but the schema is not
> yet verified against a live Codex host. Until verified, the agent-pull approach
> (steering doc instructs the agent to call `session_context()` / `search()`)
> is the supported model. See [AGENTS.md](./AGENTS.md).

## Verify

After installation, start a session and run:

```
call the diagnose tool
```

The `diagnose` MCP tool reports server state, database status, and any
initialisation errors.

## Usage

Once installed, Coree provides MCP tools. Ask Codex to search your codebase or memories:

```
search for how the indexing works
```

See [AGENTS.md](./AGENTS.md) for detailed usage guidelines.
