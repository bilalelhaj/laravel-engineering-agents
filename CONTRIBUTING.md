# Contributing

Thanks for your interest. This repo is a set of Claude Code subagents — markdown files with frontmatter that ship as a plugin. Contributions usually fall into four shapes:

## 1. Bug in an existing agent's prompt

Examples: an agent gives wrong advice, mistakes a Laravel/Filament version, contradicts a sibling, leaks scope into another agent's lane.

- Open an issue first if you're not sure it's a bug. Cite the file path and line.
- For fixes: keep the change scoped to the affected agent's prompt. Avoid unrelated reformatting in the same PR — the diff should be readable.
- If the fix encodes a non-obvious convention, add a footnote citing the source (Laravel docs, Filament upgrade guide, Pest docs).

## 2. New agent

The bar is high — the existing 12 cover most of the Laravel surface area. Before opening a PR:

- Open an issue describing **what specific gap** the agent fills that no current agent covers
- Show that the work doesn't fit into a slight expansion of an existing agent
- Have a real or constructed example of the gap (a feature the current pipeline mishandles)

If accepted, follow the structure of the closest existing agent. New agents must:

- Have a clear lane that does not overlap with any existing agent
- Declare their dependencies (which sibling agents they read, what file they output)
- Be referenced by `laravel-orchestrator` if they belong in the standard pipeline; or marked **on-demand** if they don't
- Pass the CI checks (frontmatter, README mention, link resolution)

## 3. Improving the README, real-run example, or coding philosophy

Welcome. Same scoping rule — one PR per topic. If you're proposing a stylistic change ("KISS isn't pragmatic enough", "we should default to Repository pattern"), open an issue first; the agents are opinionated by design.

## 4. Bumping a supported version

Examples: Laravel 14 ships, Filament 6 ships, Pest 5 ships.

- Update the README's "Stack support" table
- Update affected agents' "version-aware" sections
- Add a footnote to the official upgrade guide
- Update `laravel-migrator` with the new mechanical pass

## Local development

There's no install or build step — the agents are markdown.

```bash
# Validate the same way CI does
jq empty .claude-plugin/plugin.json
# Check each agent has name + description
for a in .claude/agents/*.md; do
  awk '/^---$/{f++; next} f==1' "$a" | grep -E '^(name|description):' || echo "✗ $a"
done
```

To use the agents on a real project while iterating:

```bash
# From this repo
cp .claude/agents/*.md ~/your-laravel-project/.claude/agents/
cd ~/your-laravel-project
claude  # restart Claude Code to pick them up
```

## CI

Three workflows run on every push and PR:

**`Plugin manifest`** — `.github/workflows/plugin.yml`
- `plugin.json` is valid JSON
- All required fields present (`name`, `version`, `description`, `author`, `license`, `agents`)

**`Agents`** — `.github/workflows/agents.yml`
- Every agent has frontmatter with `name` + `description` (≥ 50 chars)
- Frontmatter `name` matches filename
- Every standard-pipeline agent is reachable from the orchestrator's `Agent(...)` whitelist

**`Repository`** — `.github/workflows/repo.yml`
- Every agent is referenced in README **or** `docs/AGENTS.md`
- README internal links to `docs/` and `examples/` resolve
- `CONTRIBUTING.md` present

All three must be green to merge. Each shows up as a separate status check on the PR.

## License

By contributing, you agree your work ships under the [MIT license](LICENSE).
