# Claude Skills Marketplace

Personal marketplace for Claude Code plugins and skills.

## Plugins

| Plugin | Description |
|--------|-------------|
| **claude-skills** | Personal skills - thinking techniques, debugging patterns, process workflows |
| **intent-layer** | Intent Layer plugin - AGENTS.md infrastructure for AI navigation |
| **mx3-gym-mcp** | MCP server for MX3 Fitness gym booking - schedules, reservations, credits |

## Installation

```bash
# Add this marketplace
/plugin marketplace add orban/claude-skills

# List available plugins
/plugin list orban

# Install a plugin
/plugin install claude-skills@orban
/plugin install intent-layer@orban
/plugin install mx3-gym-mcp@orban
```

## claude-skills Plugin

A collection of skills that complement superpowers:

- **Thinking techniques**: collision-zone-thinking, inversion-exercise, meta-pattern-recognition, preserving-productive-tensions, simplification-cascades
- **Debugging/testing**: root-cause-tracing, condition-based-waiting, testing-anti-patterns, scale-game
- **Meta/process**: when-stuck, tracing-knowledge-lineages, remembering-conversations, testing-skills-with-subagents
- **Architecture**: defense-in-depth

## intent-layer Plugin

Tools for creating and maintaining Intent Layer infrastructure (hierarchical AGENTS.md/CLAUDE.md files that help AI agents navigate codebases).

Skills: `/intent-layer`, `/intent-layer-maintenance`, `/intent-layer-onboarding`, `/intent-layer-query`

See [orban/intent-layer](https://github.com/orban/intent-layer) for full documentation.

## mx3-gym-mcp Plugin

MCP server for [MX3 Fitness](https://mx3fitness.com) gym booking at Noe Valley. Provides 4 tools:

- `get_schedule` — Check slot availability for a date
- `book_slot` — Reserve a station
- `cancel_booking` — Cancel a reservation
- `get_my_bookings` — View upcoming reservations and credits

Requires `MX3_USERNAME` and `MX3_PASSWORD` environment variables.

See [orban/mx3-gym-mcp](https://github.com/orban/mx3-gym-mcp) for full documentation.
