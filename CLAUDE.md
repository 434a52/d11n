# CLAUDE.md — d11n

**d11n** — cross-repo documentation aggregator (numeronym for *documentation*). VitePress-rendered; the novel part is the cross-repo aggregation engine, not the renderer. **Private during development.** Standalone tool — codegen-independent, sibling to `i10s`; *consumed* by other repos (builds their docs sites), not housed in them.

## Instruction context

@llm/index.md

Corpus-driven methodology + collaboration/design-rigour payloads, synced from **i10s** (pinned in `llm.conf`). To update: bump the ref in `llm.conf` and follow `llm/sync.md`. Project-specific instructions go under `llm/local/`.

## Attribution

Credit Claude as co-contributor on all git activity:
- End commit messages with: `Co-Authored-By: Claude <noreply@anthropic.com>` (generic — not model-specific).
- Note Claude Code involvement in PR bodies.

## Conventions

- UK English. Terse, load-bearing docs; change logs newest-first.
- Design lives in `DESIGN.md`, authored here — design docs live with the code.
- Build/test commands added here as they land.
