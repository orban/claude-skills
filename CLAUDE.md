# Claude Skills Marketplace

Personal marketplace distributing two Claude Code plugins: `claude-skills` (thinking/debugging skills) and `intent-layer` (AGENTS.md infrastructure, sourced from external repo).

## Intent Layer

> TL;DR: Plugin marketplace repo with 14 skills and a conversation search tool. Start at Entry Points, check Subsystems for deep dives.

### Subsystems

| Area | Location | Description |
|------|----------|-------------|
| claude-skills plugin | `plugins/claude-skills/AGENTS.md` | 14 skills: thinking techniques, debugging patterns, process workflows, plus a conversation search tool |
| intent-layer plugin | External (`orban/intent-layer` git repo) | Not in this repo; pulled via marketplace.json URL source |

### Entry Points

| Task | Start Here |
|------|------------|
| Add a new skill | `plugins/claude-skills/skills/` -- create a directory with SKILL.md |
| Edit skill content | `plugins/claude-skills/skills/{name}/SKILL.md` |
| Modify marketplace config | `.claude-plugin/marketplace.json` |
| Modify plugin config | `plugins/claude-skills/.claude-plugin/plugin.json` |
| Work on conversation search tool | `plugins/claude-skills/skills/remembering-conversations/AGENTS.md` |

### Contracts

- Each skill directory must contain a `SKILL.md` with YAML frontmatter (`name`, `description`, `when_to_use`, `version`)
- The `intent-layer` plugin lives in a separate GitHub repo (`orban/intent-layer`); don't add its source files here
- Plugin dependencies are declared in `plugins/claude-skills/.claude-plugin/plugin.json`, not at the marketplace root

### Boundaries

#### Never
- Put both CLAUDE.md and AGENTS.md at project root (pick one, symlink the other)
- Add node_modules or build artifacts to the repo (only `.gitignore` and `.DS_Store` are excluded currently)

### Downlinks

| Area | Node | What's There |
|------|------|--------------|
| claude-skills plugin | `plugins/claude-skills/AGENTS.md` | 14 skills, skill patterns, category map |
| conversation search | `plugins/claude-skills/skills/remembering-conversations/AGENTS.md` | TypeScript tool: SQLite + vector search |

### Pitfalls

- `plugins/claude-skills/CLAUDE.md` exists but is a plugin description file, not an Intent Layer root; the Intent Layer root is this file
- The marketplace has two `.claude-plugin/` directories: root-level (marketplace config) and `plugins/claude-skills/.claude-plugin/` (plugin config). They serve different purposes
- `remembering-conversations` is the only skill with actual runnable code (TypeScript tool with database, embeddings, hooks). All other skills are pure SKILL.md documentation
