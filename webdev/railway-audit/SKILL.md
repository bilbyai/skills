---
name: railway-audit
description: Use when asked to audit Railway usage/costs, explain a Railway bill, or find savings. Produces a single presentable HTML report.
---

Audit the Railway workspace's spend for the last billing period (and the one before it) and write one self-contained HTML report. Keep it short and actionable: only the last 1–2 periods matter. Data commands are in `collect.md`; the report skeleton is `template.html` (copy it, edit only the JSON in `<script id="data">`).

## 1. Check access first (stop and tell me if any fail)

- **Railway CLI**: `railway whoami` and `railway usage --period previous --json`. Non-interactive runs need `--workspace <id>` (`railway whoami --json` / `railway list --json` show it).
- **Repos via gh**: `gh auth status`, then `gh api orgs/bilbyai/repos --paginate -q '.[].name'`. Use it to map services to repos and read their config (Dockerfiles, `railway/*.toml`, worker concurrency, DB clients).
- **Observability** (OpenObserve/Grafana/etc.): not set up yet. If a telemetry source appears later, use it for real memory/CPU/latency; until then fall back to `railway metrics`. Don't invent it.
- Read-only everywhere. Never print API keys or env values. Don't change anything in Railway/Qdrant/DBs; recommend instead.

## 2. Know what was reported before

Before writing, look for the previous report, in this order: a path I gave you, `./railway-audit/` in the cwd. Every report embeds its data as JSON in `<script id="data">`, so the old HTML is the snapshot: parse that block. If nothing is found, ask me where reports live (the location isn't settled), and say "no previous report" in the output.

Use it to: compare totals, mark old recommendations done/open/worse in the "Since the last report" section, and not repeat settled findings. Save new reports as `railway-audit/YYYY-MM.html` (period end month) unless told otherwise.

## 3. Report sections (in this order, via template)

1. **Cards**: total usage (+ receipt total if known), delta vs previous, memory share, biggest project.
2. **Charts**: donut by line item (memory/CPU/disk/network), donut by project.
3. **Focus**: answer the question that triggered the audit (e.g. "why is memory $112") + top consumers + a time-series of the worst offender with its limit.
4. **Projects & services**: ranked projects, expandable per-service table (avg GB, memory, other, total, previous, Δ, tags: sleeps/public/tcp). Collapse tiny projects.
5. **What changed** vs previous period (biggest movers, and _why_).
6. **How to improve**: each with a concrete action, where (file/service/CLI command), effort, and a **$ saving marked as estimate**.
7. **What to watch**: risks (OOM near limit, no spend limit, public endpoints, drift, new projects).
8. **Since the last report** (only if one exists).

## 4. Rules that make it useful

- Billing/usage API is the source of truth for cost. `railway metrics` is not: services with App Sleep show flat, forward-filled memory that isn't billed. Say so when you use metrics.
- Reconcile: per-service sum must match `railway usage` within ~$0.01; note receipt vs usage differences (plan fee, $20 credit, rounding).
- Memory ≈ $10 per GB-month (see `collect.md` for exact rates). Always give _average GB_ next to dollars; it's what people can act on.
- Check live limits (`limitOverride`) vs peaks: big gaps between baseline and peak mean bursts you pay for and an OOM risk.
- Explain _why_ a number is big by looking at the repo/config (concurrency, always-on workers, no sleep, unquantized vectors), not just the number.
- Flag cleanup: deleted-service remnants, unused Redis/Postgres, empty projects, TCP proxies/public domains on databases.
- Suggest a workspace limit if none: `railway usage limit set --workspace <id> --target workspace --soft <n> --hard <n>`.
- Savings are ranges with caveats; don't promise exact numbers. Write to be read in 2 minutes.

## 5. Styling (already in template; keep when editing)

Cards first, inline SVG icons, charts (donuts + line) in plain SVG, no libraries. Fonts: Plus Jakarta Sans + IBM Plex Mono. Don't switch to Instrument Serif, Bricolage Grotesque, Inter or Geist. Open the file when done (`open <file>`) and give me the path.
