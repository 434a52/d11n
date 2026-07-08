# d11n — design

**d11n** — numeronym for *documentation* (d·ocumentatio·n), in the `c5n`/`f8n`/`l10n`/`i10s` family. A **cross-repo documentation aggregator**: it pulls the cross-linked, multi-level README/design docs that get written throughout a repo into one shared, navigable **website**, so the docs aren't trapped per-repo. Personal open-source (destined). Design-stage — this doc is the starting frame; the agenda below is open.

> **Standalone tool.** d11n is **codegen-independent** — it does not use `c5n`. It's a sibling to `i10s`: *consumed* by other repos (e.g. it builds a repo's public docs site in CI) rather than housed in them. Its own repo, not part of the codegen monorepo.

## What it is
- **Input** — the cross-linked README/design docs already written across a repo (multi-level: repo, per-project, per-module). README-authoring is the *existing working practice*, **not** part of d11n.
- **Selection** — a **`docs.conf`** chooses which docs to include (default: every README).
- **Render** — a build step (code + **VitePress**, i.e. Vue + Vite) renders them into a site. **Front matter** on each doc sets its title and nav placement; **Mermaid** is first-class; theming/search/nav come from VitePress.
- **Output** — one navigable site, shareable across teams — everyone's docs in one place, including the overview a large estate of repos otherwise lacks.

## Scope boundary — the engine, not the renderer
The project is the **cross-repo aggregation/portal engine** — the missing *overview across many repos* (some half-dead), pulling each repo's docs into one shared place. **Renderer = VitePress** (it already solves markdown / front-matter / nav / Mermaid / theming). **The novel value is the aggregation layer on top — do not rebuild VitePress.**

## North star (to firm up)
1. **Cross-repo aggregation** — one site over *many* repos, not one-repo-one-site (that's just VitePress on its own).
2. **Conf-driven, deterministic** — `docs.conf` selects; output is reproducible and diffable.
3. **Zero authoring burden** — consumes the READMEs that already exist; introduces no new doc-writing workflow.
4. **Shared / navigable** — the overview a big repo estate lacks.

## Design agenda (open)
- **d11n vs `i10s` — confirm no shared substrate.** Both are conf-driven cross-repo aggregators — `i10s` pushes instructions *down* into agent dirs; d11n pulls docs *up* into a site. `i10s` already resolved *standalone, no shared substrate, idea dropped*. d11n should confirm the same (its own conf + fetch) unless a genuinely shared core is worth extracting. **First fork to settle.**
- **Cross-repo fetch mechanism** — how does d11n gather docs from N repos? git-based pull at pinned refs (the `i10s` sync shape), submodules, or a per-repo CI push to an aggregator? Trust/supply-chain: it reads many repos — apply the same pinned-ref + reviewable-diff discipline.
- **`docs.conf` shape** — selection globs, per-repo config, nav ordering, the front-matter contract.
- **Build & publish model** — where it runs (a repo's own CI building its site; a central aggregator for many repos?), the public output (gh-pages / static host), and the private-source → public-site path.
- **Renderer boundary** — exactly what d11n generates vs what VitePress config/theme owns.
- **Corpus-family seam** — d11n is the *render-and-share* half of the corpus story; where it meets the authoring/methodology side.

## Change log
- 2026-07-08: created — scaffolded in its own repo (standalone, codegen-independent, sibling to `i10s`; not in the codegen monorepo). Seeded from prior roadmap notes: cross-repo docs aggregator, VitePress renderer, novel value = the aggregation engine. Design agenda opened (first fork: confirm no shared substrate with `i10s`).
