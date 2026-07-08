# d11n

**d11n** (numeronym for *documentation*, d·ocumentatio·n) — a **cross-repo documentation aggregator**. It pulls the cross-linked, multi-level README/design docs written across many repos into one shared, navigable **website**, so the docs aren't trapped per-repo. `docs.conf` selects what to include; a build step renders it (**VitePress** — Mermaid, front-matter nav, theming). The novel part is the **cross-repo aggregation engine**, not the renderer.

Standalone tool — codegen-independent, sibling to `i10s`. It's *consumed* by other repos (e.g. it builds a repo's public docs site in CI), not housed in them.

Design: `./DESIGN.md`. Status: design.
