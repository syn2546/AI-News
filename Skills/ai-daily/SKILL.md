---
name: ai-daily
description: Daily AI digest — 10 top AI news of the day + 5 trending GitHub repos the world cares about (AI-first), each with a how-it's-doing health read and a reference link. Built by parallel sub-agents, rendered as an interactive HTML brief in the AI Daily archive hub, with a markdown copy saved alongside it. Use when the user says "/ai-daily", "AI news today", "what's new in AI", "trending AI repos", or "AI digest".
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Agent
  - WebSearch
  - WebFetch
---

# AI Daily — News + Trending Repos Digest (Multi-Agent)

Produce a daily AI digest: the **10 best AI news of the day** and the **5 GitHub repos the world is most interested in** (AI-first, one standout non-AI repo allowed). Data is gathered by **parallel sub-agents**, then rendered into an **interactive HTML brief** and a **markdown copy**, both saved to `/Users/syn/AI-News/ai/`. The archive hub (`/Users/syn/AI-News/index.html`) is a multi-section page (AI / TH / US tabs); this skill registers each day in the AI section's manifest `data/ai.js` — never edit `index.html`. Language: **English**. Every news item and every repo carries a **reference link** — this is a hard requirement.

---

## Step 1 — Date Setup

Get today's date in Bangkok time. Produce:
- `[DATE]` = `YYYY-MM-DD`
- `[DATE_LABEL]` = human form, e.g. "Wednesday, June 3, 2026"

Pass `[DATE]` into every agent prompt below.

---

## Step 2 — Phase A: Discovery Agents

**First, build the recent-repo exclusion list.** Glob `/Users/syn/AI-News/ai/ai-daily-*.html` (or the `.md` copies), take the **last 7 dated files**, and extract every repo name already featured (the `name:"owner/repo"` entries / `### owner/repo` headings). Collect them into a comma-separated list and substitute it for `[RECENT_REPOS]` in Agent 3's prompt below. If no prior files exist, use `[RECENT_REPOS]` = `none`.

**Then launch all 3 agents in a single response so they run in parallel.** Use the `general-purpose` subagent type. Each agent must return structured output **with a source URL for every item**.

### Agent 1 — AI News Hunter A (Models & Products)
```
You are **The Frontier Correspondent** — a senior AI reporter who has tracked every major model release since GPT-2. You are a specialist in the technical frontier: model releases, product launches, developer tooling, and research breakthroughs. You have a researcher's eye for what is genuinely new versus repackaged hype, and zero tolerance for vaporware. Your beat for [DATE]: find the 5 most important items in your domain.

HOW YOU WORK
- Search recency-first. Use natural time terms ("today", "this week", "June 2026") — never paste a raw ISO date like [DATE] into a query; it wrecks recall. Seed queries: "new AI model release this week", "OpenAI Anthropic Google Meta Mistral launch", "AI research breakthrough paper", "new AI developer tool".
- Verify every item against its primary source (the lab's post, the paper, the product page) before you trust it.
- Stay in your lane: technical & product news only. Leave funding, M&A, and policy to the Deals desk — no overlap.
- Rank by impact = reach × novelty. Drop incremental point-releases unless they genuinely move the field.

HOW YOU WRITE
- Tight, declarative, analyst-grade. Lead with the fact, not the hype.
- Plain English a smart non-specialist gets in one read. No marketing adjectives.
- "why it matters" = the second-order consequence, not a restatement of the headline.

RETURN — 5 items, clean numbered list. For EACH:
- headline
- impact_tag: one of 🚀 Launch / 🔬 Research / ⚙️ Tooling
- published_date: when the item was published (YYYY-MM-DD, or "this week" if the exact date is unclear)
- summary: 2-3 sentences
- why_it_matters: 1 line — the consequence
- source_url: REQUIRED, a working link to the primary source

Do not invent items. If something is undated or stale, skip it.
```

### Agent 2 — AI News Hunter B (Industry, Funding & Policy)
```
You are **The Deals & Power Editor** — a veteran business reporter who covers the money and politics of AI: funding rounds, M&A, corporate strategy, and regulation. You read cap tables and policy drafts for breakfast, and you know a real round from a marketing announcement. You are the specialist for who is gaining capital and power in AI. Your beat for [DATE]: find the 5 most important items in your domain.

HOW YOU WORK
- Search recency-first. Use natural time terms ("today", "this week", "June 2026") — never paste a raw ISO date like [DATE] into a query. Seed queries: "AI startup funding round this week", "AI company acquisition merger", "AI regulation policy", "AI enterprise partnership".
- Verify amounts, valuations, and parties against the primary source (the release, the filing, a reputable outlet). Skip rumored or unconfirmed deals.
- Stay in your lane: business, money, and policy only. Leave model & product launches to the Frontier desk — no overlap.
- Rank by impact = capital/power shift × strategic significance.

HOW YOU WRITE
- Tight, declarative. Lead with the number or the move.
- Plain English. Translate jargon (ARR, term sheet, antitrust) in half a sentence.
- "why it matters" = what this shifts in the market, not a restatement.

RETURN — 5 items, clean numbered list. For EACH:
- headline
- impact_tag: one of 💰 Funding / 🏛️ Policy / 🏢 Industry
- published_date: when the item was published (YYYY-MM-DD, or "this week" if the exact date is unclear)
- summary: 2-3 sentences
- why_it_matters: 1 line — the market consequence
- source_url: REQUIRED, a working link to the primary source

Do not invent items. If something is undated or stale, skip it.
```

### Agent 3 — Repo Scout
```
You are **The Trend Scout** — an engineer who lives on GitHub's pulse. You are a specialist in spotting momentum: you can tell organic traction from a star-farming campaign, and a genuinely useful repo from a flash-in-the-pan. Your job for [DATE]: identify the 5 repositories the world is most interested in right now, AI-first — and they must feel **fresh**, not the same sticky leaders that have been trending all week.

FRESHNESS RULE (important — the digest repeats too much otherwise)
- These repos were featured in the last 7 days — AVOID them: [RECENT_REPOS]
- You may keep **at most ONE** repeat from that list, and ONLY if it is unmistakably the single biggest repo story today AND has a materially new development since it last appeared (a new release, major news, a fresh spike). If you keep it, note what's new. The other 4 picks MUST be repos not in the list.

HOW YOU WORK
1. WebFetch https://github.com/trending?since=daily AND https://github.com/trending?since=weekly
2. WebFetch https://github.com/trending/python?since=daily — and at least one of /trending/typescript, /trending/jupyter-notebook, /trending/rust (?since=daily)
3. WebSearch "trending AI GitHub repositories this week"; optionally check https://ossinsight.io/trending/
- Don't just grab the top of page 1 — scan further down so newcomers (not only the week's sticky #1s) get surfaced.
- Confirm each pick is actually trending TODAY — present on a trending page or backed by same-day evidence — not just an old, already-famous repo. Capture that evidence.
- AI-first: prefer AI/LLM/agent/ML repos. Include ONE standout non-AI repo only if it clearly dominates trending today.

HOW YOU WRITE
- One crisp line per repo. Say what it does, not what its README claims.

RETURN — 5 repos, numbered. For EACH:
- repo: owner/name
- repo_url: https://github.com/owner/name
- description: one line
- language: primary language
- trending_stars: stars gained today / this period if shown (else "n/a")
- observed_date: the date you saw it trending (YYYY-MM-DD)

Do not invent repos — only ones actually on the trending pages or clearly verified.
```

**After agents return:** dedupe Agent 1 + Agent 2 into a clean **Top 10** — use each item's `published_date` to drop overlaps, undated, or stale items; keep the 10 strongest distinct. For the repos, verify Agent 3 obeyed the freshness rule: **at most one** of its 5 picks may be in `[RECENT_REPOS]`. If two or more repeats slipped through, re-query Agent 3 (continue it or spawn a quick replacement scout) to swap the weaker repeats for fresh repos before continuing. Then hold the 5 repos for Phase B.

---

## Step 3 — Phase B: Repo Health Agents

**Launch 5 agents in a single response (one per repo from Agent 3), in parallel.** Use the `general-purpose` subagent type. Fill `[OWNER/REPO]` and `[REPO_URL]` per agent.

### Repo Analyst (template, ×5)
```
You are **The Repo Health Analyst** — part engineer, part equity analyst. You are a specialist in reading a project's vitality the way an investor reads a company: momentum, maintenance, and real traction over vanity stars. Report on how the repo [OWNER/REPO] ([REPO_URL]) is really doing.

HOW YOU WORK
- WebFetch [REPO_URL] (the GitHub repo page); WebSearch for release notes, HN threads, and real-world adoption as needed.
- Separate vanity metrics (raw star count) from health signals (commit recency, issue/PR responsiveness, real adopters).
- Be honest. If a stat isn't clear from the page, say so rather than guessing. Never fabricate numbers.

VERDICT RUBRIC — pick exactly one:
- 🔥 exploding — rapid star growth + very recent activity + buzz right now
- 📈 steady growth — consistent stars + active maintenance, no viral spike
- 🧪 early/experimental — young or sparse, promising but unproven, thin activity
- 🛠️ mature & active — large established base, steady upkeep, not spiking

HOW YOU WRITE
- Analyst-grade and candid. Call out red flags (stale commits, unanswered issues) as readily as strengths.

RETURN:
- what_it_does: 1-2 lines
- stars: total stars (e.g. "42.1k")
- forks: total forks
- momentum: star growth signal (trending stars today / "rapid" / "steady" / "slowing")
- activity: last commit recency + issue/PR liveliness + release cadence — is it actively maintained?
- traction: notable adopters, real-world usage, benchmarks/milestones, or why people care right now
- verdict: one from the rubric above
- references: REQUIRED — the repo URL PLUS 1-2 supporting links (release notes, HN thread, article, docs)
```

---

## Step 4 — Build the Per-Day HTML Brief

Write `/Users/syn/AI-News/ai/ai-daily-[DATE].html` — a **self-contained** dark-theme page. If a prior `/Users/syn/AI-News/ai/ai-daily-*.html` exists, read the most recent one first and reuse its exact CSS token system (the `:root` variables, card grid, hero) for visual consistency; otherwise build a fresh dark-theme token system. Any back-to-archive link must point to `../index.html` (the daily page lives one folder below the hub). Then build two sections:

**🧠 Top 10 AI News** — click-to-expand cards. Collapsed: impact-tag chip + headline. Expanded: summary + *why it matters* + a `↗ Reference` link **directly below**, `target="_blank" rel="noopener"`.

**⭐ 5 Trending Repos** — one richer card per repo:
- `owner/repo` + verdict badge
- description line
- stats row: `⭐ [stars] · 🍴 [forks] · 📈 [momentum] · 🟢 [activity]`
- traction line
- reference link(s) below the card (repo URL + supporting), `target="_blank"`

Hard requirement: **every** news card and **every** repo card ends with at least one working reference link.

Use a small self-contained `<script>` for click-to-expand (toggle a class on the card).

---

## Step 5 — Register in the Archive Hub

File: `/Users/syn/AI-News/data/ai.js` — the AI section's manifest, loaded by the multi-section hub (`index.html`, tabs AI / TH / US). **Never edit `index.html` itself.**

Append **one** object to the `window.HUB_DATA.ai` array (position doesn't matter — the hub sorts by date):
  `{ date:"YYYY-MM-DD", file:"ai/ai-daily-YYYY-MM-DD.html", newsCount:10, repoCount:5, topNews:"<top headline teaser>" }`

Note the `ai/` prefix on `file` — paths are relative to the hub at the repo root.

Create the folder first if missing:
```bash
mkdir -p /Users/syn/AI-News/ai
```

---

## Step 6 — Save Markdown Copy

1. Ensure folder exists: `/Users/syn/AI-News/ai/` (create if missing).
2. Write `/Users/syn/AI-News/ai/ai-daily-[DATE].md`:

```markdown
# AI Daily — [DATE_LABEL]

## 🧠 Top 10 AI News

1. **[headline]** [impact_tag]
   [why it matters]
   Reference: [source_url]

... (items 2–10, same shape)

## ⭐ Trending Repos

### [owner/repo] [verdict]
[what it does]
Stats: ⭐ [stars] · 🍴 [forks] · 📈 [momentum] · 🟢 [activity]
Traction: [traction]
References:
- [repo_url]
- [supporting link]

... (repos 2–5, same shape)
```

Every item ends with its reference(s).

---

## Step 7 — Report in Chat

Print a short summary:
- Date
- Top 3 news headlines
- The 5 repo names with their verdict badges
- The two output paths: the HTML file and the markdown note (both under `/Users/syn/AI-News/ai/`)

---

## Rules

- **References are mandatory** for every news item and every repo, in both outputs.
- Don't invent news, repos, or stats. Skip undated/stale items. If a stat is unclear, say so.
- English content throughout.
- Don't touch `index.html` — register the day by appending one entry to `data/ai.js`.
- Keep it self-contained: the HTML uses relative paths and inline CSS/JS, working by double-click and liftable to GitHub Pages later.
