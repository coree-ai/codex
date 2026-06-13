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
model download and remote sync) and filesystem write access (for its database and
model cache). Add this to `~/.codex/config.toml`, substituting your username:

```toml
[sandbox_workspace_write]
network_access = true
writable_roots = [
  "/home/you/.cache/coree",
  "/home/you/.local/share/coree"
]
```

Use absolute paths - `~` expansion is not reliable in TOML.

## Environment variables

If you use coree's remote sync (Turso), the following env vars must be set in
your shell so Codex forwards them to the MCP process:

- `COREE__MEMORY__REMOTE_AUTH_TOKEN`
- `COREE__MEMORY__REMOTE_URL`

The plugin's `.mcp.json` already lists these in `env_vars` so Codex will forward
them if they are present in your shell environment.

## Hooks

> **Note**: Codex does not yet support installing hooks from plugins. The session
> context injection hooks must be added manually.

Add the following to your `~/.codex/config.toml` to enable session injection:

```toml
[hooks.SessionStart]
command = "npx --yes @coree-ai/coree@0.13.0 inject --type session"

[hooks.UserPromptSubmit]
command = "npx --yes @coree-ai/coree@0.13.0 inject --type prompt"
```

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
