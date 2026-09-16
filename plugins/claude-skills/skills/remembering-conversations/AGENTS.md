# Remembering Conversations

## Purpose
Owns: indexing, archiving, and semantic search of past Claude Code conversation transcripts.
Does not own: the conversations themselves (~/.claude/projects/), the superpowers subagent dispatch pattern, or the search-agent prompt template behavior.

## Design Rationale

- **Problem solved**: Claude Code sessions are ephemeral. Past decisions, patterns, and debugging insights are lost between sessions. This tool makes them searchable.
- **Core insight**: Combine local embeddings (free, fast, private) with SQLite for a zero-cost semantic search over conversation history. AI-generated summaries (Claude Haiku) give quick context without reading full transcripts.
- **Constraints**: Must run in background after session ends (hook), must work offline for search (local embeddings), summarization needs Anthropic API key.

## Code Map

### Find It Fast
| Looking for... | Go to |
|----------------|-------|
| Database schema and migrations | `tool/src/db.ts` |
| Conversation JSONL parser | `tool/src/parser.ts` |
| Embedding generation (all-MiniLM-L6-v2) | `tool/src/embeddings.ts` |
| Main indexing logic | `tool/src/indexer.ts` |
| Search (vector + text modes) | `tool/src/search.ts` |
| AI summarization (Haiku/Sonnet) | `tool/src/summarizer.ts` |
| Path resolution (XDG, env overrides) | `tool/src/paths.ts` |
| Core types | `tool/src/types.ts` |
| CLI entry: indexing | `tool/src/index-cli.ts` |
| CLI entry: search | `tool/src/search-cli.ts` |
| sessionEnd hook script | `tool/hooks/sessionEnd` |
| Hook installer | `tool/install-hook` |
| Subagent search prompt | `tool/prompts/search-agent.md` |
| Deployment runbook | `DEPLOYMENT.md` |
| Indexing/recovery guide | `INDEXING.md` |
| Tests | `tool/src/db.test.ts`, `tool/src/verify.test.ts`, `tool/src/search-agent-template.test.ts` |
| E2E deployment tests | `tool/test-deployment.sh` |

### Key Relationships
```
CLI scripts (index-conversations, search-conversations)
  -> src/index-cli.ts, src/search-cli.ts
    -> src/indexer.ts (orchestrates parsing, summarizing, embedding, inserting)
      -> src/parser.ts (JSONL -> ConversationExchange[])
      -> src/summarizer.ts (exchanges -> AI summary text)
      -> src/embeddings.ts (text -> Float32[384])
      -> src/db.ts (SQLite + sqlite-vec storage)
        -> src/paths.ts (resolve ~/.config/superpowers/... paths)
```

## External Dependencies
| Service | Used For | Failure Mode |
|---------|----------|--------------|
| Anthropic API (Haiku) | AI conversation summaries | Summarization fails; indexing still works without summaries. Sonnet fallback attempted first |
| @xenova/transformers | Local embeddings (all-MiniLM-L6-v2, 384d) | First run downloads model to ~/.cache/transformers. Offline after that |
| sqlite-vec | Vector similarity search extension | Loaded as SQLite extension. If missing, search fails entirely |
| better-sqlite3 | SQLite driver | Native module; needs rebuild if Node version changes |

## Data Flow
```
Session ends -> hooks/sessionEnd
  -> index-conversations --session <uuid>
    -> find JSONL in ~/.claude/projects/<project>/
    -> copy to ~/.config/superpowers/conversation-archive/<project>/
    -> parse JSONL -> exchanges
    -> summarize via Claude Haiku -> write -summary.txt
    -> embed each exchange (local) -> insert into SQLite + vec_exchanges
```

```
Search query
  -> search-conversations "query"
    -> embed query (local)
    -> vec_exchanges MATCH (cosine distance)
    -> JOIN exchanges for metadata
    -> load -summary.txt if exists
    -> format results with project, date, similarity%, snippet
```

## Entry Points
| Task | Start Here |
|------|------------|
| Fix indexing issues | `tool/src/indexer.ts` |
| Fix search issues | `tool/src/search.ts` |
| Change database schema | `tool/src/db.ts` (add migration in `migrateSchema()`) |
| Change storage paths | `tool/src/paths.ts` |
| Change summary format | `tool/src/summarizer.ts` |
| Run tests | `cd tool && npm test` (vitest) |
| Install/debug hook | `tool/install-hook` and `tool/hooks/sessionEnd` |

## Contracts

- Database has two tables: `exchanges` (metadata + optional embedding blob) and `vec_exchanges` (virtual table, Float32[384] embeddings via sqlite-vec)
- Embeddings are 384-dimensional (all-MiniLM-L6-v2). Changing the model requires rebuilding the entire index
- Summary files are `{uuid}-summary.txt` alongside `{uuid}.jsonl` in the archive directory. The filename convention is hardcoded (`.replace('.jsonl', '-summary.txt')`)
- `vec_exchanges` doesn't support `INSERT OR REPLACE` -- code must DELETE then INSERT (see `db.ts:insertExchange`)
- Path resolution order: `PERSONAL_SUPERPOWERS_DIR` env > `XDG_CONFIG_HOME/superpowers` > `~/.config/superpowers`

## Boundaries

### Always
- Run `--verify` after schema changes or rebuilds
- Use `migrateSchema()` in `db.ts` for schema changes (not raw ALTER TABLE elsewhere)

### Never
- Change embedding dimensions without rebuilding the full index (`--rebuild`)
- Access `process.env` for paths directly; use `paths.ts` functions

### Ask First
- `--rebuild` deletes the entire database and re-indexes everything (expensive, requires API calls for summaries)

## Pitfalls

- `vec_exchanges` virtual table doesn't support `INSERT OR REPLACE`. The code works around this by doing DELETE + INSERT. If you try REPLACE, you get a silent failure
- The hook at `~/.claude/hooks/sessionEnd` is the installed copy, not `tool/hooks/sessionEnd` in this repo. Editing the repo version doesn't affect the installed hook until you re-run `install-hook`
- `tool/src/search.ts` builds SQL time filter clauses by string interpolation (`'${after}'`), not parameterized queries. This is safe because dates are validated first, but don't change the validation
- `EventEmitter.defaultMaxListeners` is set to 20 in `indexer.ts` globally. This is a side effect of importing the module
- Test files use env vars (`TEST_ARCHIVE_DIR`, `TEST_PROJECTS_DIR`) to override paths. If these aren't set, tests will touch real data
- The `package.json` has `"type": "module"` -- all imports use `.js` extensions even for `.ts` source files (ESM convention with tsx)
