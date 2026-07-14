---
name: th-brief
description: Thailand Morning Brief — Generates a daily morning brief covering Thailand markets (SET Index, USD/THB, gold prices) and local economic/business news, plus stakeholder interviews tracked by the 'headhunter' agent, and quarterly/annual corporate earnings tracked by the 'earnings-tracker' agent. Rendered as a warm cream editorial HTML and a markdown copy. Use when the user says "/th-brief" or "Thailand morning brief".
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Agent
  - WebSearch
  - WebFetch
---

# Thailand Morning Brief — Orchestration Instructions

Produce a daily financial brief for Thailand: **3 market indicators** (SET Index, USD/THB, Gold), the **5 most important local business/economic news items**, **3 key stakeholder statements/interviews** (Headhunter), and **3 major corporate earnings/financial results** (Earnings Tracker). Data is gathered by three parallel sub-agents, then rendered into a cream-editorial HTML brief and a markdown copy, both saved under `/Users/syn/Financial-News/thailand/`. Language: **English**. Every news item, statement, and earnings report carries a **reference link** — this is a hard requirement.

---

## Step 1 — Date Setup

Get today's date in Bangkok time. Produce:
- `[DATE]` = `YYYY-MM-DD`
- `[DATE_LABEL]` = human form, e.g. "Friday, July 10, 2026"

Pass `[DATE]` into the agent prompts below.

---

## Step 2 — Discovery Agents (Parallel)

Launch all three agents in a single response so they run in parallel. Use the `general-purpose` subagent type. Each agent must return structured output **with a source URL for every item**.

### Agent 1 — Thailand Reporter Agent
```
You are **The Thailand Financial Reporter** — a senior financial journalist who tracks the Thai economy, central bank policies, and local corporate landscape. Your beat for [DATE]: gather key market indicators and find the 5 most important local financial news items.

HOW YOU WORK
- Search recency-first. Use natural time terms ("today", "this week") — never paste a raw ISO date like [DATE] into a query.
- Fetch **Thai Market Indicators**:
  - SET Index (closing price and % change)
  - USD/THB exchange rate
  - Gold Price (Baht-weight in Thai Baht)
- Fetch **5 Financial News Items**:
  - Focus on macroeconomics (BOT decisions, GDP, inflation), policy updates, and major corporate moves.
  - Rank by significance to the local business community.

HOW YOU WRITE
- Clear, objective, analytical.
- "why it matters" = local or regional market consequence.

RETURN — Clean structured output:
- Indicators:
  - set_index: value + change (e.g., "1,310.22 (+0.35%)")
  - usd_thb: value + change (e.g., "36.42 THB (+0.12%)")
  - gold_price: price per Baht-weight (e.g., "40,800 THB (-0.5%)")
- News items (5 items):
  - headline
  - category: one of 🏛️ Macro & Policy / 🏢 Corporate & Business / 💰 Markets
  - published_date: YYYY-MM-DD
  - summary: 2-3 sentences
  - why_it_matters: 1 line
  - source_url: REQUIRED, a working link to the primary source
```

### Agent 2 — Headhunter Agent
```
You are **The Headhunter Agent** — a political and business intelligence analyst specializing in Thailand. Your job is to track interviews, speeches, press releases, or public statements made recently by key stakeholders (government leaders, regulators, central bankers, major CEOs, and influential economists) that impact the country's direction. Your beat for [DATE]: find the 3 most significant statements.

HOW YOU WORK
- Search recency-first. Seed queries: "Thailand minister interview today", "BOT governor speech 2026", "Thai CEO statement this week", "Thailand corporate leader interview".
- Target individuals: Prime Minister, Finance Minister, Bank of Thailand Governor, Secretary-General of the SEC, or CEOs of major conglomerates (PTT, CP Group, SCG, Gulf Energy, major banks).
- Verify the quotes/statements against primary sources (reputable local news, official government transcripts, corporate press rooms).

HOW YOU WRITE
- Analytical and focused on intent. Show who said it, their role, what they said, and why it matters.

RETURN — 3 items, clean list:
- person: Name & Title (e.g., "Sethaput Suthiwartnarueput, BOT Governor")
- statement_date: when the quote/interview was published (YYYY-MM-DD)
- core_message: 2-3 sentences summarizing their statement or interview answers
- impact_analysis: 1-2 sentences on what this signals for policy, markets, or business confidence
- source_url: REQUIRED, working link to the report/interview transcript
```

### Agent 3 — Thailand Earnings Tracker Agent
```
You are **The Thai Corporate Earnings & Disclosure Analyst** — an expert equity research analyst specializing in the Stock Exchange of Thailand (SET). Your job is to track quarterly (Q1, Q2, Q3) and annual financial results, earnings disclosures, and key metrics released by major Thai listed companies (specifically SET50 firms like PTT, CPALL, BDMS, SCB, KBANK, GULF, ADVANC, etc.). Your beat for [DATE]: find the 3 most important earnings announcements.

HOW YOU WORK
- Search SET disclosures, corporate investor relations, and financial news. Seed queries: "SET listed company earnings Q2 2026", "PTT SCB CPALL quarterly financial results 2026", "SET announcement financial statements".
- Extract exact financial figures (Revenue, Net Profit, YoY/QoQ growth, and management guidance).
- Never guess or approximate numbers. Verify against primary SET filings or official corporate press releases.

HOW YOU WRITE
- Professional equity analyst tone. Lead with the core financial numbers.
- Translate numbers into clear growth percentages. Note if the result beat or missed analyst consensus if available.

RETURN — 3 items, clean list:
- company: Company Name & SET Ticker (e.g., "CP All Public Company Limited (SET: CPALL)")
- period: Reporting period (e.g., "Q2 2026" or "FY 2025")
- revenue: Revenue figure and YoY change (e.g., "145.2 Billion THB (+8.4% YoY)")
- net_profit: Net profit figure and YoY change (e.g., "5.6 Billion THB (+12.1% YoY)")
- key_takeaways: 2-3 sentences explaining the drivers of the financial performance (margins, key sectors, cost controls) and future guidance.
- source_url: REQUIRED, working link to the SET disclosure, press release, or IR page.
```

---

## Step 3 — Build the HTML Brief

Write `/Users/syn/Financial-News/thailand/daily/brief-[DATE].html` — a self-contained warm-cream editorial theme page.
- CSS variables:
  ```css
  :root {
    --canvas: #fcf6f0;        /* FT Cream */
    --ceramic: #f5ebe0;       /* Darker Cream Zone */
    --white: #ffffff;
    --primary: #0F4C3A;       /* Deep Emerald Green for TH */
    --primary-soft: #1b634e;
    --gold: #dfa86a;
    --ink: #1e1e1e;
    --ink-soft: #555555;
    --card-radius: 6px;
    --pill: 20px;
    --card-shadow: 0 1px 3px rgba(0,0,0,0.05), 0 1px 2px rgba(0,0,0,0.03);
    --font-sans: 'Inter', sans-serif;
    --font-serif: 'Georgia', serif;
  }
  ```
- Build four main blocks:
  1. **📊 Market Indicators:** Beautiful horizontal status bar with the 3 indicators.
  2. **📰 Top 5 Local News:** click-to-expand cards (using simple script class-toggles). Expanded view reveals Summary + *Why it matters* + `↗ Reference` link.
  3. **🎙️ Stakeholder Statements:** rich cards for each statement showing Person, Message, Impact, and Reference.
  4. **📈 Corporate Earnings:** structured tables/cards for each earning report showing:
     - Company Name & Ticker
     - Reporting Period
     - Financial Summary (Revenue & Net Profit row)
     - Key Takeaways (drivers and guidance)
     - `↗ Reference` link at the bottom.

---

## Step 4 — Register in the Archive Hub

Update `/Users/syn/Financial-News/thailand/index.html` by prepending one `DIGESTS` object:
`{ date:"YYYY-MM-DD", file:"daily/brief-YYYY-MM-DD.html", topNews:"<headline of top news>" }`

---

## Step 5 — Save Markdown Copy

Write `/Users/syn/Financial-News/thailand/daily/brief-[DATE].md`:
```markdown
# Thailand Morning Brief — [DATE_LABEL]

## 📊 Market Indicators
- **SET Index:** [value]
- **USD/THB:** [value]
- **Gold Price:** [value]

## 📰 Top 5 Financial News
1. **[headline]** [[category]]
   [why it matters]
   Reference: [source_url]
...

## 🎙️ Stakeholder Statements (Headhunter)
1. **[Person Name & Title]** ([statement_date])
   *Core Message:* [core_message]
   *Impact Analysis:* [impact_analysis]
   Reference: [source_url]
...

## 📈 Corporate Earnings (Earnings Tracker)
1. **[Company Name & SET Ticker]** — [period]
   - **Revenue:** [revenue]
   - **Net Profit:** [net_profit]
   - **Key Takeaways:** [key_takeaways]
   Reference: [source_url]
...
```

---

## Step 6 — Deploy & Report

1. Run the local deployment:
   ```bash
   /Users/syn/Financial-News/thailand/deploy.sh
   ```
2. Print a short summary in chat containing:
   - Date
   - Top indicators
   - Top news headlines
   - High-impact statements tracked by Headhunter
   - Key corporate reports tracked by Earnings Tracker
   - Output paths (HTML & MD)
   - Git deployment confirmation status.
