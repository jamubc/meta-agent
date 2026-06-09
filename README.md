# meta-agent

A read-only agent harness for administrative tasks in a local Documents folder.

## Compatibility

The `.agents/` directory with `agent.json` and `SKILL.md` files targets the Antigravity CLI plugin system. The `AGENTS.md` file is a cross-tool convention recognized by most AI coding CLIs.

To support all tools from a single source, this repo includes `scripts/sync.sh`. It reads the structured `.agents/` definitions and generates a flat `AGENTS.md` with symlinks for tool-specific filenames:

| Target file | Recognized by |
|-------------|---------------|
| `AGENTS.md` | Gemini CLI, Codex, Aider, OpenHands, and others |
| `CLAUDE.md` | Claude Code |
| `.cursorrules` | Cursor |
| `.github/copilot-instructions.md` | GitHub Copilot |

All four files contain the same content. Update the `.agents/` definitions, run `sync.sh`, and every tool picks up the changes.

## What this is

Five specialist agents that scan, analyze, and plan operations on your filesystem. All agents are read-only. They cannot write files, run commands, or modify anything. Destructive operations are proposed as shell commands, sanitized for safety, and executed only after your explicit approval.

## Agents

| Agent | Purpose |
|-------|---------|
| `file-organizer` | Proposes file sorting, renaming, deduplication, archival |
| `file-analyst` | Reports on disk usage, file distribution, inventory |
| `backup-manager` | Proposes backup commands, integrity checks, folder diffs |
| `directory-inspector` | Inspects any directory type: repos, media, configs, vaults |
| `agent-reviewer` | Audits agent/skill definitions for dangerous tool access |

## Safety model

All five agents share an identical, minimal toolset: `list_dir`, `view_file`, `grep_search`, `send_message`. No agent has `run_command` or `write_to_file`.

For CLIs with plugin systems (Antigravity), this is a structural constraint enforced by the framework. For CLIs that read flat instruction files (Claude Code, Cursor, Copilot), the generated `AGENTS.md` encodes the same safety rules as behavioral instructions. In both cases, the CLI's own command approval system gates execution.

The orchestrator sanitizes all agent-proposed commands against a blocklist before presenting them to you. Every agent includes prompt injection defense.

## Installation

Copy `.agents/` and `AGENTS.md` into the directory you want to manage:

```sh
cp -r .agents/ ~/Documents/.agents/
cp AGENTS.md ~/Documents/AGENTS.md
```

Update path references in agent system prompts if your target directory differs.

## Updating

Edit the canonical definitions in `.agents/plugins/admin-plugin/`, then regenerate all flat instruction files:

```sh
./scripts/sync.sh
```

## Structure

```
.agents/plugins/admin-plugin/    # Canonical source of truth
  plugin.json
  agents/
    file-organizer/agent.json
    file-analyst/agent.json
    backup-manager/agent.json
    directory-inspector/agent.json
    agent-reviewer/agent.json
  skills/
    admin-orchestrator/SKILL.md

scripts/
  sync.sh                        # Generates AGENTS.md + symlinks

AGENTS.md                         # Generated, cross-tool
CLAUDE.md -> AGENTS.md            # Symlink
.cursorrules -> AGENTS.md         # Symlink
.github/copilot-instructions.md   # Symlink
```

## License

MIT
