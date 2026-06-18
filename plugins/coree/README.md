# Coree Codex Plugin

[Coree](https://github.com/coree-ai/coree) provides persistent memory and code
intelligence for AI agents. This plugin integrates Coree into Codex.

## Features

- **MCP Server**: Registers the coree MCP server in Codex.
- **Automatic context injection**: Injects session context at startup, prompt
  suggestions before each user prompt, and fresh session context after
  compaction through Codex's compact session-start trigger.
- **Persistent Memory**: Stores decisions, architectural discoveries, and gotchas across sessions.
- **Code Intelligence**: Hybrid search over source code and git history.
- **Stop hook checkpoint**: Runs the end-of-turn coree checkpoint before the
  session ends.

## Installation

Register the marketplace source and install the plugin:

```bash
codex plugin marketplace add coree-ai/codex --ref main
codex plugin add coree@coree
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

## Data storage

By default, coree stores its memory database, code index, model cache, and logs
locally under coree's configured data directories. If remote sync environment
variables are configured, coree can sync memory and index data to those remote
databases.

## Hooks

The plugin bundles `hooks/hooks.json`. Codex loads these hooks from enabled
plugins:

- `SessionStart`: runs `coree inject --type session`
- `UserPromptSubmit`: runs `coree inject --type prompt`

The hook commands wrap coree's raw text output in Codex's
`hookSpecificOutput.additionalContext` JSON envelope with the required
`hookEventName` field. `Stop` uses Codex's Stop-specific continuation contract:
if coree emits checkpoint text, the hook returns `decision: "block"` with that
text as `reason`; otherwise it returns `{}`. Codex uses the reason as a
continuation prompt.

Codex requires hook trust review before non-managed command hooks run. Use
`/hooks` in Codex to review and trust the coree hooks after installing or
updating the plugin.

## Verify

After installation, start a session and run:

```
call the diagnose tool
```

The `diagnose` MCP tool reports server state, database status, and any
initialisation errors.

## Usage

Once installed, Coree automatically injects relevant context. It also provides
MCP tools. Ask Codex to search your codebase or memories:

```
search for how the indexing works
```

See [AGENTS.md](./AGENTS.md) for detailed usage guidelines.
