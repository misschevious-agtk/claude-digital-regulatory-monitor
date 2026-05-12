# Claude — start here every time

This file is your boot script for the **Prosus Digital & Competition Monitor**. Read it first. Then check `.claude/memory/CURRENT.md` for what's most recent.

## What this project is

A live, hallucination-free regulatory monitor for the Prosus competition team. Two HTML dashboards (`dashboard.html` = Digital & Regulatory, `competition-law-monitor.html` = Competition Law), backed by a Python pipeline that ingests RSS → verifies → sifts → ranks → renders → deploys to Netlify.

**Built for Prosus first; designed to scale to other legal teams** via the tenant system (`brain/tenants/`).

Owner: Klimentina Maleevska, Amsterdam.

## What you should know on every session

- **907 trusted source hosts** (`brain/sources/registry.json`) across tier-0/1/2. 102 Chrome-verified. Do not invent URLs — the verifier will reject items whose host isn't in the registry.
- **Three-layer hallucination defense**: URL registry → 6-dim sifter → Chrome citation cross-verifier. Don't bypass.
- **Tenant separation is real**: Prosus's configs live in `brain/tenants/prosus/`. Template for new tenants in `brain/tenants/template/`. Don't touch one team's configs to fix another's problem.
- **Pipeline is push-button** (`python3 pipeline.py`). Sandbox can't reach GitHub or open internet — RSS scans + git pushes must run on the user's machine or a cloud cron.
- **You can NOT fetch fresh items from the sandbox.** `python3 pipeline.py --scan` requires `feedparser` plus outbound HTTPS — both blocked here (`urllib` returns 403 via the proxy, `feedparser` not installed). When Klimentina asks for "today's articles" / "fresh items" / "scan the sources," do **not** silently re-run the no-scan pipeline — that just re-renders yesterday's data and looks like nothing changed. Say plainly: "Scanning has to run on your machine — `python3 pipeline.py --scan --commit`." Then offer to re-render once she's staged candidates in `brain/feedback/inbox/`.
- **The dashboards auto-widen the date filter on empty.** As of 12 May 2026, `renderArticles()` in both HTML files steps the date range up the ladder (`today → 24h → week → 30d → all`) before showing "Nothing in this lane." If you ever rewrite that function, preserve this guard.
- **Klimentina runs Obsidian** with this repo as a vault. The `docs/` folder uses `[[wikilinks]]` so notes are navigable.

## File-organization conventions

- **One-shot scripts** that mutate registry / HTML / configs: prefix with `_` (e.g. `_add_digital_v4.py`). After running, MOVE them to `archive/old_scripts/`.
- **Long-lived modules** that the pipeline imports: no underscore (e.g. `analytics.py`, `network_graph.py`).
- **Tests** under `tests/` (`tests/test_pipeline.py`).
- **Helper docs for the user** in `docs/` (Obsidian-friendly).
- **Memory for Claude** in `.claude/memory/`.
- **Audit / logs** in `logs/`.
- **Brain (data)** in `brain/`.

## When to do what

| User asks | Look at first |
|---|---|
| "add a source" | `.claude/memory/SOURCES.md` |
| "add a persona" | `.claude/memory/CONVENTIONS.md` § Personas |
| "fork for new team" | `SCALE_GUIDE.md` + `docs/20 - Forking for a New Team.md` |
| "the pipeline broke" | `docs/80 - Troubleshooting.md` + `logs/pipeline_run_*.json` |
| "what was just done" | `.claude/memory/CURRENT.md` |
| "what's the architecture" | `docs/90 - Architecture Deep Dive.md` |

## Hard rules (don't break)

1. **Never invent sources.** Hosts must be real and Chrome-verifiable.
2. **Never bypass the registry.** Items linking outside it get dropped, full stop.
3. **Never amend old git commits.** Always create new ones.
4. **Never edit `brain/tenants/<other-team>/`** when fixing for a different team.
5. **Never modify the legacy `brain/personas/team.json`** — write to `brain/tenants/prosus/personas.json` instead. Legacy is kept for backward compat only.
6. **Always run the 8 unit tests** before deploying (`python3 -m unittest tests.test_pipeline`).
7. **Always Chrome-verify new tier-0 hosts** before claiming they're trusted.

## Memory files (deeper context)

- **`.claude/memory/PROJECT.md`** — full state: what's built, what's deployed, what's pending
- **`.claude/memory/CONVENTIONS.md`** — coding + file conventions, schema details
- **`.claude/memory/SOURCES.md`** — registry conventions, tier definitions, how to add
- **`.claude/memory/DECISIONS.md`** — ADRs: why we picked tier system, why Chrome verifier, why tenant split
- **`.claude/memory/CURRENT.md`** — most recent session's work (refresh every session)

## Right before responding

Skim `.claude/memory/CURRENT.md` to know what the user just did, then act.

---

_Last loaded: 11 May 2026 · 907 trusted hosts · 102 verified · two monitors live · 7 personas · 93 tasks completed_
