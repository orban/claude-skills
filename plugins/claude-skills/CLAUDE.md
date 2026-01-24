# Claude Skills Plugin

Personal skills collection for Claude Code - thinking techniques, debugging patterns, and process workflows.

## Overview

This plugin provides skills that complement the core superpowers plugin with additional techniques for creative thinking, systematic debugging, and meta-cognitive processes.

## Skills

### Thinking Techniques
- **collision-zone-thinking** - Force unrelated concepts together to discover emergent properties
- **inversion-exercise** - Flip core assumptions to reveal hidden constraints
- **meta-pattern-recognition** - Spot patterns appearing across 3+ domains
- **preserving-productive-tensions** - Recognize when disagreements reveal valuable context
- **simplification-cascades** - Find insights that eliminate multiple components

### Debugging & Testing
- **root-cause-tracing** - Trace bugs backward through call stack
- **condition-based-waiting** - Replace arbitrary timeouts with condition polling
- **testing-anti-patterns** - Never test mock behavior, understand dependencies
- **scale-game** - Test at extremes to expose fundamental truths

### Meta & Process
- **when-stuck** - Dispatch to the right problem-solving technique
- **tracing-knowledge-lineages** - Understand how ideas evolved over time
- **remembering-conversations** - Search previous conversations for context
- **testing-skills-with-subagents** - RED-GREEN-REFACTOR for process documentation

### Architecture
- **defense-in-depth** - Validate at every layer data passes through

## Dependencies

This plugin depends on:
- `superpowers@superpowers-marketplace` - Core skills library
- `intent-layer@orban` - Intent Layer tools for AGENTS.md infrastructure

## Installation

```bash
# Add the marketplace
/plugin marketplace add orban/claude-skills

# Install the plugin
/plugin install claude-skills@orban
```
