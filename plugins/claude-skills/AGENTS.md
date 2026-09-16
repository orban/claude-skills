# claude-skills Plugin

## Purpose
Owns: collection of 14 skills that teach AI agents thinking techniques, debugging patterns, and process workflows.
Does not own: intent-layer plugin (separate repo), marketplace-level config (root `.claude-plugin/`).

## Design Rationale

- **Problem solved**: Claude Code agents lack structured approaches for creative problem-solving, systematic debugging, and meta-cognitive processes. These skills fill that gap.
- **Core insight**: Skills are documentation-as-instruction. A well-written SKILL.md changes agent behavior without any code. Only `remembering-conversations` needed actual implementation.
- **Constraints**: Skills must be self-contained (one directory each), loadable on demand via slash commands, and follow the superpowers plugin skill format.

## Code Map

### Find It Fast
| Looking for... | Go to |
|----------------|-------|
| Skill frontmatter format | Any `skills/*/SKILL.md` -- YAML block at top |
| Plugin metadata | `.claude-plugin/plugin.json` |
| Skill with runnable code | `skills/remembering-conversations/` (only one) |
| Skill with example code | `skills/condition-based-waiting/example.ts` |
| Skill with shell script | `skills/root-cause-tracing/find-polluter.sh` |
| Subagent testing examples | `skills/testing-skills-with-subagents/examples/` |
| Dispatch table (which skill for what) | `skills/when-stuck/SKILL.md` |

### Key Relationships
- `when-stuck` dispatches to other skills by symptom type (hub skill)
- `root-cause-tracing` recommends `defense-in-depth` as follow-up
- `testing-anti-patterns` references TDD cycle from `testing-skills-with-subagents`
- `remembering-conversations` depends on `superpowers` plugin for the subagent search workflow

### Skill Categories
| Category | Skills |
|----------|--------|
| Thinking | collision-zone-thinking, inversion-exercise, meta-pattern-recognition, preserving-productive-tensions, simplification-cascades |
| Debugging/Testing | root-cause-tracing, condition-based-waiting, testing-anti-patterns, scale-game, defense-in-depth |
| Meta/Process | when-stuck, tracing-knowledge-lineages, remembering-conversations, testing-skills-with-subagents |

## Entry Points
| Task | Start Here |
|------|------------|
| Add a new skill | `skills/` -- create directory, add SKILL.md |
| Edit a skill | `skills/{name}/SKILL.md` |
| Find which skill to use | `skills/when-stuck/SKILL.md` (dispatch table) |
| Change plugin dependencies | `.claude-plugin/plugin.json` |
| Work on conversation search | `skills/remembering-conversations/AGENTS.md` |

## Boundaries

### Never
- Add runnable code to a skill without good reason (skills are documentation; `remembering-conversations` is the sole exception)
- Use absolute paths in SKILL.md (skills get installed to different locations)

### Ask First
- Adding a new plugin dependency in `.claude-plugin/plugin.json`

## Contracts

- Every skill directory must have a `SKILL.md` with YAML frontmatter: `name`, `description`, `when_to_use`, `version` (all required)
- Skills are pure documentation except `remembering-conversations` which has a `tool/` subdirectory with TypeScript source
- Plugin declares dependencies on `superpowers@superpowers-marketplace` and `intent-layer@orban` in `.claude-plugin/plugin.json`

## Patterns

### Adding a new skill
1. Create `skills/{skill-name}/` directory (kebab-case)
2. Create `SKILL.md` with YAML frontmatter block (`name`, `description`, `when_to_use`, `version`)
3. Add skill to the appropriate category in this file's Skill Categories table
4. If the skill dispatches from `when-stuck`, add an entry to `skills/when-stuck/SKILL.md`
5. Update `CLAUDE.md` at plugin root (the existing description file) if it lists skills

### SKILL.md frontmatter format
```yaml
---
name: Human-Readable Name
description: One-line description of what it does
when_to_use: when [specific trigger condition]
version: 1.1.0
languages: all  # optional, only if language-specific
---
```

## Pitfalls

- The `CLAUDE.md` in this directory is a plugin description file, not an Intent Layer node. Don't confuse it with `AGENTS.md` (this file)
- `remembering-conversations` references paths like `~/.claude/skills/collaboration/remembering-conversations/tool/` which is where superpowers installs it, not the path in this repo. The repo path is `skills/remembering-conversations/tool/`
- Some skills reference other skills by paths like `skills/problem-solving/simplification-cascades` or `skills/debugging/systematic-debugging` -- these are superpowers-relative paths, not paths within this repo

## Downlinks
| Area | Node | What's There |
|------|------|--------------|
| Conversation Search | `skills/remembering-conversations/AGENTS.md` | TypeScript tool: SQLite + vector search, sessionEnd hook, indexer, summarizer |
