# pi-subagentx

Sandbox-by-default subagent extension for [pi](https://github.com/badlogic/pi-mono).

Based on the [upstream subagent example](https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent/examples/extensions/subagent), with one key change: **subagents run in a fully sandboxed subprocess by default**. No built-in tools, no auto-discovered extensions, no skills, no prompt templates leak into the subagent unless explicitly declared in the agent definition.

## Why

The upstream subagent extension passes `--tools <list>` but doesn't isolate extensions, skills, or prompt templates. This means a subagent intended to only search the web could still access globally installed extensions. For security-sensitive use cases (e.g., sandboxing web search to prevent prompt injection), this is insufficient.

## Changes from upstream

**`agents.ts`:**
- New `extensions` frontmatter field: comma-separated extension package specs
- New `sandbox` frontmatter field: boolean, default `true`. Set `false` to opt out.

**`index.ts`:**
- Sandbox by default: subagents start with `--no-tools --no-extensions --no-skills --no-prompt-templates`
- `tools:` in agent frontmatter adds `--tools <list>` for built-in tools
- `extensions:` in agent frontmatter adds `-e <spec>` for each extension
- `sandbox: false` restores the upstream permissive behavior

## Installation

```bash
pi install git:github.com/niklaas/pi-subagentx
```

## Agent definitions

Create `.md` files in `~/.pi/agent/agents/` or `.pi/agents/`:

```markdown
---
name: my-agent
description: What this agent does
tools: read, grep, find, ls
extensions: npm:@some/extension
model: claude-haiku-4-5
---

System prompt for the agent goes here.
```

Frontmatter fields:
- `tools:` - built-in tools to enable (e.g., `read, grep, find, ls`)
- `extensions:` - extension packages to load (e.g., `npm:@some/extension`)
- `sandbox: false` - opt out of sandboxing (not recommended)

## Upstream tracking

A GitHub workflow checks weekly for changes to the upstream files and opens a PR when they diverge. The `.upstream` reference files track what the upstream looks like, separate from our modifications.
