# External task-source integrations

The `@laravel-tasks` agent reads `TODO.md` by default. To pull tasks from a project-management tool instead, set up the corresponding MCP server in Claude Code, then point the agent at it.

The MCP setup is one-time per machine. After it's done, you invoke the agent like:

```
@laravel-tasks run my Linear sprint
@laravel-tasks pick the bugs from my ClickUp list "Sprint 12"
@laravel-tasks read GitHub issues labeled 'todo' in this repo
```

---

## ClickUp (official MCP server)

ClickUp ships an official remote MCP server[^clickup-docs]. **OAuth-only** — no API keys.

### Add to Claude Code

```bash
claude mcp add --transport http clickup https://mcp.clickup.com/mcp
```

### First use

Run any Claude Code session and trigger an MCP tool — Claude prompts you through the OAuth login in the browser. After that, the token is cached.

```
/mcp                 # in Claude Code, manually triggers OAuth setup if needed
```

### Rate limits

- Free Forever: 50 calls / 24h
- Unlimited+: 300 calls / 24h
- Add the "Everything AI" add-on for higher limits

### Invoke from the task runner

```
@laravel-tasks pick the bugs from my ClickUp list "Sprint 12"
@laravel-tasks run the unstarted tasks in my ClickUp list "Backend Sprint"
```

The agent uses the MCP tools (typically `mcp__clickup__get_tasks`, `mcp__clickup__create_comment`, `mcp__clickup__update_task_status`) to read, comment, and transition status.

---

## Linear (official MCP server)

Linear was one of the first to ship an official MCP server, built with Cloudflare and Anthropic[^linear-docs]. **OAuth 2.1**, 25+ tools.

### Add to Claude Code

```bash
claude mcp add --transport http linear https://mcp.linear.app/mcp
```

### First use

```
/mcp                 # triggers OAuth flow
```

### Available tools

Read, create, update issues / projects / cycles / initiatives / milestones / comments. The Feb 2026 update added initiatives and project updates.

### Invoke from the task runner

```
@laravel-tasks run my Linear sprint
@laravel-tasks pick the open issues assigned to me in Linear
@laravel-tasks read Linear cycle "ENG-Q2-2026" and run them
```

---

## Trello

Trello does **not** ship an official MCP server (as of mid-2026). Use one of the community implementations (e.g. [`mcpservers.org`](https://mcpservers.org) directory) — verify maintenance and source before installing.

### Pattern

```bash
# Example community server — verify before using
claude mcp add --transport stdio trello "npx" "-y" "@some-author/trello-mcp"
```

You'll typically need a Trello API key + token (Trello does not yet support OAuth for MCP). Set them as environment variables in your Claude Code `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "trello": {
      "command": "npx",
      "args": ["-y", "@some-author/trello-mcp"],
      "env": {
        "TRELLO_API_KEY": "...",
        "TRELLO_TOKEN": "..."
      }
    }
  }
}
```

### Invoke from the task runner

```
@laravel-tasks run my Trello board "Backlog"
```

---

## GitHub Issues (no MCP needed)

GitHub Issues works through the `gh` CLI — already in `laravel-tasks`'s tool list as Bash. No MCP setup required.

### Prerequisites

```bash
gh auth login          # authenticate the CLI once
```

### Invoke from the task runner

```
@laravel-tasks read GitHub issues labeled 'todo' and run them
@laravel-tasks pick issues assigned to me in this repo and run them
```

The agent runs `gh issue list --label todo --state open --json number,title,body,labels` to read and `gh issue comment` / `gh issue close` to update.

---

## Verifying the MCP setup

After adding any MCP server, list what Claude Code sees:

```bash
claude mcp list
```

Then in a session, type `/mcp` to confirm the server is connected and OAuth is complete (where applicable).

---

## How `@laravel-tasks` decides which source to use

In **this priority order**:

1. **Explicit instruction in the prompt** — *"@laravel-tasks run my **Linear** sprint"* always wins
2. **Configured MCP servers + verb hint** — if you say "run my sprint", and only Linear is configured, the agent picks Linear
3. **`TODO.md`** at the repo root — fallback when nothing external is named

The agent never auto-discovers external sources without you naming them — that would be an unwanted reach into a different system.

---

## Round-trip: what the agent does on completion

For each task it completes:

| Source | What gets updated |
| :--- | :--- |
| `TODO.md` | `[ ]` flipped to `[x]`, sub-bullet appended with one-liner result + commit hash |
| Linear | Comment posted with result, status moved (e.g. "In Review" or "Done") |
| ClickUp | Comment posted, status transitioned |
| Trello | Card moved to a destination list (configurable per board) |
| GitHub Issues | Comment posted, optionally `--state closed` |

For external sources, the agent only writes back **if you said so explicitly** (*"… and update Linear"*). Otherwise it reads, runs, and reports — your task tool stays untouched.

---

[^clickup-docs]: [ClickUp — Connect an AI assistant to ClickUp's MCP server](https://developer.clickup.com/docs/connect-an-ai-assistant-to-clickups-mcp-server). Server URL: `https://mcp.clickup.com/mcp`. OAuth-only authentication.
[^linear-docs]: [Linear — MCP server](https://linear.app/docs/mcp), [Linear changelog: MCP server](https://linear.app/changelog/2025-05-01-mcp). Server URL: `https://mcp.linear.app/mcp`. 25+ tools. Built with Cloudflare and Anthropic.
