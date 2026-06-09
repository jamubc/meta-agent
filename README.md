# meta-agent

A meta-agent that audits other agents for concerning abilities. Read-only. Scans for agent/skill files and reviews purely for safety: tool access, isolation, prompt injection surface area.

## Compatibility

| Target file | Recognized by |
|-------------|---------------|
| `AGENTS.md` | Gemini CLI, Codex, Aider, OpenHands, and others |
| `CLAUDE.md` | Claude Code |
| `.cursorrules` | Cursor |
| `.github/copilot-instructions.md` | GitHub Copilot |
| `.agents/plugins/` | Antigravity CLI (structured agent definitions) |

All flat files contain the same content, generated from the canonical `.agents/` definitions by `scripts/sync.sh`.

## What it does

Scans a target directory for `agent.json`, `SKILL.md`, `AGENTS.md`, and similar AI configuration files. Reviews them against five criteria:

1. **Dangerous tool access** -- flags `run_command`, `write_to_file`, `unsandboxed`, and similar tools
2. **Isolation of concerns** -- tools an agent has but does not need for its stated role
3. **Prompt injection surface** -- the critical combination of reading untrusted files and having write/exec tools
4. **Missing safety guardrails** -- no scope restriction, no two-phase execution, no error handling
5. **Skill trigger safety** -- overly broad descriptions that could conflict or misfire

Does not evaluate what the project does. Only whether the agent definitions are safe.

## Safety

The agent-reviewer is itself read-only. Its only tools are `list_dir`, `view_file`, `grep_search`, and `send_message`. It cannot write files, run commands, or modify anything. It includes prompt injection defense.

## Installation

Copy `.agents/` and `AGENTS.md` into your project:

```sh
cp -r .agents/ /path/to/your/project/.agents/
cp AGENTS.md /path/to/your/project/AGENTS.md
```

Or copy it into a parent directory that covers multiple projects.

## Updating

Edit the canonical definition in `.agents/plugins/meta-agent-plugin/agents/agent-reviewer/agent.json`, then regenerate all flat files:

```sh
./scripts/sync.sh
```

## Structure

```
.agents/plugins/meta-agent-plugin/
  plugin.json
  agents/
    agent-reviewer/agent.json      # Canonical definition

scripts/
  sync.sh                          # Generates AGENTS.md + symlinks

AGENTS.md                           # Generated, cross-tool
CLAUDE.md -> AGENTS.md              # Symlink
.cursorrules -> AGENTS.md           # Symlink
.github/copilot-instructions.md     # Symlink
```

## Usage

In any AI coding CLI session:

- "Review agents in this project"
- "Audit agent safety"
- "Check for dangerous tool access in .agents/"
- "Scan for agent security issues"

The agent scans, analyzes, and reports. It produces a findings table and a tool access matrix.

## License

MIT
