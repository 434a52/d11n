# d11n — design

**d11n** — numeronym for *documentation* (d·ocumentatio·n), in the `c5n`/`f8n`/`l10n`/`i10s` family. A **cross-repo documentation aggregator**: it pulls the design/README docs written across a set of repos into one navigable **site**, rendered by VitePress, with selection and nav driven entirely by a single conf. Personal open-source (destined). Standalone tool, codegen-independent.

> **Deciding context: a small personal portfolio.** d11n's use here is a private, curated site over a handful of the author's own repos. This context is the deciding one — it *inverts* the defaults a large enterprise aggregator would choose (see **Portfolio scale inverts enterprise defaults**).

## Standalone — no shared substrate with i10s
They look similar (conf-driven, cross-repo) but share nothing that matters:
- **Direction & consumer** — i10s pushes instructions *down* into each repo's agent dir (consumer: the machine); d11n pulls docs *up* into a site (consumer: humans reading).
- **Trust model** — i10s's safety is pinned-ref + reviewable-diff *into your own repo*; d11n *reads and republishes*.
- **Complexity** — i10s is deliberately code-free (a vendored instruction driving git). d11n has a real build (VitePress + an aggregation layer). A shared substrate would drag i10s toward the complexity it was designed to avoid.

Each keeps its own conf and mechanism.

## Portfolio scale inverts enterprise defaults
The context is a **handful of own, living repos — curated, private, Claude-maintained**. A large enterprise aggregator (many repos, many teams, thousands of docs, self-service onboarding) optimises the opposite way and would legitimately choose *push + per-file frontmatter + glob selection*. The portfolio flips every constraint, so the optimum flips too — to **pull + conf-hosted metadata + explicit selection**. The decisions below all follow from that inversion.

## Sources — pull + local (a d11n build, not subscriber steps)
**One build — d11n's own — resolves every source.** No repo runs a step; nothing is pushed. A keyed **`sources`** section defines each source once, and is where the fetch mechanism lives:
- **local** (`path:`) — a checkout d11n reads in place (e.g. the repo its build runs in).
- **pull** (`git:` + pinned `ref:`) — fetched at a pinned ref (i10s's trust model: reproducible, reviewable). d11n owns all portfolio repos, so read access is trivial.

*(No dead/unmaintained repos in the portfolio → no "pull-for-dead" special case; no enterprise push sources — both are deferred rooms.)*

## Selection, metadata & nav — all in the conf, explicit
- **Explicit list, no globs.** Nothing appears unless deliberately listed — **curation by construction**, and the **public-safe gate**: an unlisted doc is simply absent (fail-closed), never accidentally published (the fail-open risk globs carry). The only cost, maintaining the list, is absorbed by Claude.
- **Metadata in the conf, not per-file frontmatter.** d11n stays **read-only over its sources** — a source `DESIGN.md` is a design record, not to be burdened with the site's presentation metadata. VitePress still gets frontmatter: d11n *injects* it into the `content/` copy from the conf at build time. Title defaults to the doc's **H1**; a conf `title:` overrides (for sidebar labels).
- **Nav defined in the conf, not derived from file layout.** The source repos' directory structure must not shape the site's nav (that would bury projects under their repo, or leak plumbing). The conf holds an explicit **nav tree** (nested sections → entries); VitePress's sidebar is generated from it.

### Conf sketch (shape, not final field names)
```yaml
sources:
  forge: { path: ../forge }
  i10s:  { git: https://github.com/434a52/i10s, ref: v1.0.0 }
  d11n:  { path: . }
nav:
  - section: Codegen
    items:
      - { repo: forge, path: c5n/DESIGN.md, title: c5n }
      - { repo: forge, path: c5n/README.md }
  - section: Tooling
    items:
      - { repo: i10s, path: docs/collaborative-agents.md }
      - { repo: d11n, path: DESIGN.md, title: d11n }
```
The conf is simultaneously **selection** (what's in), **metadata** (how it's shown), and **gate** (nothing unlisted ships) — the single source of truth for the site.

## Storage vs presentation — two decoupled layers
Because the nav is conf-driven, the on-disk cache need not be presentation-shaped. Key it by repo — **`content/{repo}/{path}`** — unique, collision-free, purely internal. Presentation is the conf's nav tree; the reader never sees the file layout.

## Renderer & engine
**Renderer = VitePress** (Vue + Vite) — it already solves markdown, front-matter, nav, Mermaid, search, theming. **The novel value is the aggregation layer** (keyed sources + conf-driven nav + the content cache), not the renderer — don't rebuild VitePress.

## Language & runtime — TypeScript on Node
Dictated by the renderer: VitePress is a Node tool with a TS config, so Node is in the toolchain regardless, and d11n's logic (resolve sources, generate the sidebar) runs in that same process — much of it *inside* the VitePress config, plus a small pre-step to git-fetch pull sources. The `c5n` "Go static binary" rationale (cross-ecosystem, zero runtime added to consumers) does **not** apply here — d11n is a personal build tool living in the Node/VitePress ecosystem, not a cross-ecosystem engine. **Bun deferred** — a stack-wide runtime call, not a d11n-local one, and no lock-in (TS + VitePress are runtime-agnostic).

## Build pipeline
1. Read the conf.
2. Resolve each source — local read, or git-fetch at the pinned ref — into the `content/{repo}/` cache.
3. Copy the listed docs, injecting frontmatter (from the conf) into each copy.
4. Generate the VitePress config (sidebar) from the nav tree.
5. VitePress renders → a **private** static site.

## Output & audience
**Private by default** — the generated site is not published; a public output is out of scope for now and a deliberate, separate decision. **Visibility tiers deferred:** should a public output ever be wanted, a per-entry `visibility` field can split one `content/` into a curated public view and a fuller internal one, without touching sources. Not needed while there's a single (private) audience; the conf is the author's own file, so adding the field later is non-breaking.

## Deferred (rooms)
- **Visibility tiers** — a curated-public vs fuller-internal split, if/when a public output is wanted.
- **Enterprise scale** — push/subscriber sources, pull-for-dead-repos: the many-repo variant, not the portfolio's need.

## Decisions log
- **Standalone** — no shared substrate with i10s.
- **Portfolio-scale** (small, curated, private, own repos) — inverts enterprise defaults.
- **Sources: pull + local**, keyed `sources` block (fetch mechanism + pinned refs); a d11n build, not subscriber push.
- **Selection: explicit list, no globs** (curation-by-construction / public-safe gate; Claude-maintained).
- **Metadata: conf-hosted, not frontmatter** (read-only over sources; injected into the cache copy); title = H1 default + conf override.
- **Nav: explicit conf tree**, not derived from file layout.
- **Cache: `content/{repo}/{path}`** (internal, collision-free); presentation via the conf nav.
- **Renderer: VitePress** (don't rebuild it); novel value = the aggregation layer.
- **Language: TypeScript on Node** (VitePress-dictated); Bun deferred (stack-wide, no lock-in).
- **Conf format: YAML** (family consistency; TS-config considered, deferred).

## Change log
- 2026-07-08: **design substantially settled** (supersedes the scaffold agenda). Standalone; portfolio-scale context (small/curated/private/own repos; inverts enterprise defaults); pull + local keyed sources (a d11n build, not subscriber push); explicit conf-driven selection + metadata + nav (no globs, no per-file frontmatter); `content/{repo}/` internal cache; VitePress renderer with the aggregation layer as the novel value; TypeScript on Node; YAML conf. Deferred: visibility tiers (public-flip), enterprise push/dead-repo sources.
- 2026-07-08: created — scaffolded in its own repo; seed frame + open agenda.
