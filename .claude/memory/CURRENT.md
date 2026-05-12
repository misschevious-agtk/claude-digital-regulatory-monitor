# CURRENT — most recent session's work

_Refresh this file after each significant session so the next Claude knows what just happened._

## Last session: 12 May 2026 — full-topic re-ingest, fresh items live

Klimentina (or a cron on her machine) ran `keyword_scanner.py` at 09:47 — generated **139 candidates** but the file ended up in `brain/feedback/processed/` without anything re-ingesting it into the HTML dashboards. Diagnosed and fixed.

Move that mattered: copied the candidate file back to `inbox/`, re-ran `python3 pipeline.py` — both dashboards jumped.

- `dashboard.html`: 124 → **238 items** (139 ingested, 25 sifter-rejected, 0 registry-rejected)
- `competition-law-monitor.html`: 59 → **139 items** (16 sifter-rejected)
- **Items dated 12 May 2026 now live: 5 on digital, 2 on competition.** Hong Kong Canvas hack (72k affected, scmp.com), AI disinfo Singapore (scmp.com), OpenAI Daybreak vuln-detection (thehackernews.com), iOS 26.5 default E2EE RCS (thehackernews.com), Pluralistic essay.
- 15 unit tests green. `deploy/` mirror matches. Log: `logs/pipeline_run_20260512_095907.json`.
- Sifter-rejected items (legit-looking but low-score): FCA reports, mergers undertakings register. Acceptable.
- **Recurring failure mode to watch:** scanner runs locally, candidate file lands in inbox OR gets prematurely moved to processed, pipeline doesn't re-run → user sees stale data. Fix is to ALWAYS run `python3 pipeline.py` immediately after `keyword_scanner.py`, or chain them. `run_scan.bat` already does the scan + pipeline together; the issue happens when a cron or other path runs only the scanner.

## Last session: 12 May 2026 — empty-dashboard fix + scan-limit honesty

Klimentina opened the dashboards and got: Digital Regulatory blank, Competition Law showing yesterday's items. Three things landed:

1. **Confirmed sandbox cannot scan.** `feedparser` is not installed and `urllib` returns 403 from the proxy. So `python3 pipeline.py --scan` simply will not run from a Claude session. Updated `CLAUDE.md` to make this explicit and tell future sessions to say so rather than silently re-rendering stale data.
2. **Patched `renderArticles()` in both `dashboard.html` and `competition-law-monitor.html`** to auto-widen the date filter when it returns zero. The ladder is `today → 24h → week → 30d → all`. Recursion is bounded by ladder length, and the existing topic-only auto-broaden still runs after the range step. Root cause of the empty Digital monitor: a stale `filter.range='today'` saved in Klimentina's localStorage.
3. **Re-ran the no-scan pipeline + 15 unit tests** — both green. Log: `logs/pipeline_run_20260512_093417.json`. Latest items are still dated 11 May 2026 (53 on digital, 40 on competition). Anything dated 12 May will only appear after she runs `python3 pipeline.py --scan --commit` on her own machine.

### Last session: 12 May 2026 — dashboard refresh

- Ran `python3 pipeline.py` (no `--scan`, no `--commit`) on existing inbox/items.
- Both dashboards re-verified, re-sifted, re-ranked, re-rendered.
  - `dashboard.html` — 124 in / 124 published (0 registry- or sifter-rejected).
  - `competition-law-monitor.html` — 59 in / 59 published.
- Enrichment passes that re-ran: case_tracker (30 cases ≥2 items), citations (26 legal cites, 91 tier-0 primaries), analytics (74 fines, €262,504m total, 16 jurisdictions), digital_analytics (38 AI fines, 37 privacy actions, 7 cyber incidents, 30 DMA/DSA, 35 frontier model releases), network_graph (183 nodes / 413 edges), ICS feed (20 VEVENTs).
- 15 unit tests green (`python3 -m unittest tests.test_pipeline`).
- `deploy/` mirrors both refreshed HTML files (diff clean).
- Pipeline log → `logs/pipeline_run_20260512_092847.json`.
- Still push-button — not committed/pushed to GitHub; Netlify won't rebuild until Klimentina pushes from her machine.

## Previous session: 11 May 2026

### What landed (most recent first)

1. **Persistent memory wired** (this file + `CLAUDE.md` + 4 sibling memory files in `.claude/memory/`). Future Claude sessions auto-load project context.
2. **Multi-tenant + Obsidian docs** — `brain/tenants/prosus/` (live), `brain/tenants/template/` (starter), `tenant_init.py` (bootstrap), `docs/` folder with 8 Obsidian-friendly notes using wikilinks, `SCALE_GUIDE.md` at root.
3. **AI-specific sources expansion** (+71 hosts): specialist AI news, frontier labs, governance/policy orgs, evaluation/benchmark, coding-AI, AI hardware. 17 Chrome-verified.
4. **Privacy + data-protection expansion** (+64 hosts): EU member-state DPAs, MENA + LatAm + African DPAs, US state privacy regulators, privacy press, compliance vendors, civil-society orgs. 15 Chrome-verified.
5. **Connection-network widget**: `network_graph.py` produces `logs/analytics/network.json` with 162 nodes + 250 edges. Every single-source (yellow) dot is linked via case/entity/agency/slug/fallback edges. Renders as inline SVG below the metrics row on both monitors.
6. **Digital-monitor analytics tailoring**: `digital_analytics.py` produces 6 new JSON snapshots (AI fines, privacy enforcement by DPA, cyber incidents, DMA/DSA progression, AI Act phases, frontier model releases) — rendered as 6 new color-coded cards in the metrics row on dashboard.html.
7. **Registry audit + dedup**: `_audit_registry.py` found and auto-cleaned 1 duplicate + 1 malformed host. Audit log in `logs/audit/`.
8. **Chrome verification + fixes**: across rounds 1-3, dropped/renamed 4 hallucinated hosts (`aisi.gov.sg`, `inai.org.mx`, `import.ai`, `oag.colorado.gov`); stamped `_verified_at` on 102 hosts.

### Registry state at session end

- **907 trusted hosts** (was 469 at session start → 575 → 678 → 775 → 836 → 907)
- 244 tier-0 / 164 tier-1 / 367 tier-2
- 102 Chrome-verified with `_verified_at` stamps
- 4 hallucinations dropped/fixed
- 2 chrome-blocked (real but unverifiable), 3 SPA/cloudflare flagged

### Tasks 87-93 from this session

All marked completed. Notable ones:
- #87 Chrome-driven registry sanitization
- #88 digital_analytics + case_tracker tailored to digital monitor
- #89 Connection-network widget (yellow dots all connected)
- #90 Privacy/data sources verified
- #91 AI sources verified
- #92 Multi-tenant + Obsidian docs structure
- #93 Persistent memory (this file + CLAUDE.md)

### Confirmation from Klimentina (11 May 2026)

The Prosus competition team = exactly these 7 people, treated as one unified audience: **Anne-Claire, Barbara, Josephine, Monica, Alex, Jeremi, Klimentina**. Not separate audiences — one team spread across Prosus HQ + iFood + Just Eat Takeaway + Despegar. The monitor exists to serve all seven together.

### What's NOT done / open

- Pipeline doesn't yet read `TENANT=` env var to switch configs. Currently still reads legacy `brain/personas/team.json` and `brain/entities.json`. **Tier-8 work** to cut over fully.
- Netlify Identity not enabled in Netlify dashboard yet. Widget is in HTML; one-click to activate.
- Translation backend defaults to "none". User needs to set `TRANSLATE_BACKEND=anthropic` + `ANTHROPIC_API_KEY` in `.env` to activate.
- Slack/Teams/email digest config in `digest.json` is templates with empty URLs/credentials. User fills in.
- Pipeline not on a scheduled cron yet — push-button only. **Tier-8 priority**: GitHub Actions workflow.
- Two flagged tier-0 hosts (`sdaia.gov.sa`, `uaedataoffice.gov.ae`) need manual verification.

### State of running services

- Two HTML files synced to `deploy/`. Ready to commit + push.
- All 8 unit tests pass.
- `logs/analytics/*.json` regenerated.
- `logs/audit/2026-05.jsonl` has fresh entries.
- `brain/cases.json` regenerated.
- `deploy/prosus-monitor.ics` regenerated.

### How the user has been working

- Pushes hard for completeness ("WAY more" sources, "do all of them not a batch")
- Cares deeply about verification — confronted me on hallucinations early, drove the citation cross-verifier
- Wants the system to scale to other legal teams (started this session's Tier-7 work)
- Runs Obsidian locally with this repo as a vault — likes wikilinks
- Time zone: Amsterdam
- Strong design taste — Prosus brand colors (Lithium purple, Argon pink, Chloride teal) used consistently

### What to do next session (if she asks)

Suggested order:
1. **Wire `TENANT=` env var into the engine** (the last bit of Tier-7) — `pipeline.py`, `persona_rank.py`, `case_tracker.py`, `daily_digest.py` should all prefer `brain/tenants/$TENANT/` over legacy paths.
2. **Set up GitHub Actions cron** so the pipeline runs every 4 hours without push-button.
3. **Manual verification of sdaia.gov.sa + uaedataoffice.gov.ae** — if she can confirm they're real, remove the `chrome_blocked_at_load` flag.
4. **Commit + push to GitHub** so Netlify rebuilds and the live site catches up.

### Style she likes in my responses

- Concise summary at top
- Tables for multi-row data
- Honest tradeoffs called out
- Concrete next-step recommendations
- No formatting fluff (no "Certainly!" or "Great question!")
