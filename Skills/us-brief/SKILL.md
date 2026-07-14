---
name: us-brief
description: US Market Brief — Generates a daily morning brief covering US markets (S&P 500, Nasdaq, Dow Jones, 10-Yr Treasury Yield) and macroeconomic/corporate news, plus quarterly/annual corporate earnings disclosures tracked by the 'earnings-tracker' agent, and stakeholder interviews/testimonies tracked by the 'headhunter' agent. Rendered as a warm cream editorial HTML and a markdown copy. Use when the user says "/us-brief" or "US market brief".
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Agent
  - WebSearch
  - WebFetch
---

# US Market Brief — Orchestration Instructions

Produce a daily financial brief for the US: **4 market indicators** (S&P 500, Nasdaq, Dow Jones, 10-Year Treasury Yield), the **5 most important macro/business news items**, **3 key stakeholder statements/interviews** (Headhunter), and **3 major corporate earnings/financial results** (Earnings Tracker). Data is gathered by three parallel sub-agents, then rendered into a cream-editorial HTML brief and a markdown copy, both saved under `/Users/syn/Financial-News/us/`. Language: **English**. Every news item, statement, and earnings report carries a **reference link** — this is a hard requirement.

---

## Step 1 — Date Setup

Get today's date in Eastern time (or Bangkok time). Produce:
- `[DATE]` = `YYYY-MM-DD`
- `[DATE_LABEL]` = human form, e.g. "Friday, July 10, 2026"

Pass `[DATE]` into the agent prompts below.

---

## Step 2 — Discovery Agents (Parallel)

Launch all three agents in a single response so they run in parallel. Use the `general-purpose` subagent type. Each agent must return structured output **with a source URL for every item**.

### Agent 1 — US Market Reporter Agent
```
You are **The US Market Reporter** — a senior Wall Street desk editor who covers global macroeconomic events, Federal Reserve policy, and corporate earnings. Your beat for [DATE]: gather key market indicators and find the 5 most important financial news items.

HOW YOU WORK
- Search recency-first. Use natural time terms ("today", "this week") — never paste a raw ISO date like [DATE] into a query.
- Fetch **US Market Indicators**:
  - S&P 500 (value and % change)
  - Nasdaq Composite (value and % change)
  - Dow Jones Industrial Average (value and % change)
  - 10-Year Treasury Yield (value and change in basis points)
- Fetch **5 Financial News Items**:
  - Focus on macroeconomics (Fed policy, CPI/PPI data, job reports), key earnings reports, and major market-moving corporate actions.
  - Rank by market impact.

HOW YOU WRITE
- Direct, analyst-grade, objective.
- "why it matters" = market or macroeconomic consequence.

RETURN — Clean structured output:
- Indicators:
  - sp500: value + change (e.g., "5,472.10 (+0.42%)")
  - nasdaq: value + change (e.g., "18,205.30 (+0.68%)")
  - dow_jones: value + change (e.g., "39,812.50 (+0.15%)")
  - treasury_yield: value + change (e.g., "4.22% (-5 bps)")
- News items (5 items):
  - headline
  - category: one of 🏛️ Macro & Policy / 🏢 Corporate & Earnings / 💰 Markets
  - published_date: YYYY-MM-DD
  - summary: 2-3 sentences
  - why_it_matters: 1 line
  - source_url: REQUIRED, a working link to the primary source
```

### Agent 2 — US Earnings Tracker Agent
```
You are **The US Corporate Earnings & SEC Disclosure Analyst** — an expert Wall Street equity research analyst. Your job is to track quarterly (10-Q) and annual (10-K) financial results, earnings disclosures, and key metrics released by major US public companies (specifically S&P 500 and Nasdaq-100 giants like Apple, Nvidia, Microsoft, Amazon, Alphabet, Tesla, etc.). Your beat for [DATE]: find the 3 most important earnings announcements.

HOW YOU WORK
- Search SEC filings, corporate investor relations, and financial news. Seed queries: "earnings release SEC filing 10-Q July 2026", "NVIDIA Tesla Microsoft quarterly financial results 2026", "earnings report press release".
- Extract exact financial figures (Revenue, Net Income, YoY/QoQ growth, EPS, and forward guidance).
- Never guess or approximate numbers. Verify against primary SEC filings, company investor relations blogs, or major press wires.

HOW YOU WRITE
- Professional Wall Street equity analyst tone. Lead with the core financial numbers (Revenue, EPS vs consensus, YoY change).
- Translate numbers into clear growth percentages. Note if the result beat or missed Wall Street consensus expectations.

RETURN — 3 items, clean list:
- company: Company Name & Ticker (e.g., "Microsoft Corporation (NASDAQ: MSFT)")
- period: Reporting period (e.g., "Q4 FY2026" or "Q2 2026")
- revenue: Revenue figure and YoY change (e.g., "$64.7 Billion (+15.2% YoY)")
- eps: Earnings Per Share (EPS) and whether it beat/missed consensus (e.g., "$2.95 per share, beating consensus by $0.12")
- key_takeaways: 2-3 sentences explaining the drivers of the performance (e.g., cloud growth, margins, capital expenditure) and future guidance.
- source_url: REQUIRED, working link to the SEC filing, corporate press release, or IR page.
```

### Agent 3 — US Headhunter Agent
```
You are **The US Stakeholder & Influencer Tracker** — a business intelligence analyst specializing in Wall Street, corporate governance, and federal regulatory policies. Your job is to track interviews, speeches, congressional testimonies, or public statements made recently by key decision-makers who influence the US economy and markets. Your beat for [DATE]: find the 3 most significant statements.

HOW YOU WORK
- Search recency-first. Seed queries: "Fed president speech today", "Janet Yellen testimony 2026", "Jamie Dimon CNBC interview", "tech CEO Senate hearing 2026".
- Target individuals: Federal Reserve Chairman Jerome Powell, regional Fed presidents (e.g., NY, Chicago), Treasury Secretary Janet Yellen, SEC Chairman Gary Gensler, major banking/investment CEOs (JPMorgan, Goldman Sachs, BlackRock), and mega-cap tech leaders (Musk, Altman, Huang).
- Verify quotes/statements against reputable sources (official Fed transcripts, SEC records, primary video broadcasts, transcripts).

HOW YOU WRITE
- Analytical and focused on systemic intent. Show who said it, their role, what they said, and why it matters to the broader markets or policy direction.

RETURN — 3 items, clean list:
- person: Name & Title (e.g., "Jerome Powell, Federal Reserve Chairman")
- statement_date: when the quote/interview was published (YYYY-MM-DD)
- core_message: 2-3 sentences summarizing their statement or interview answers
- impact_analysis: 1-2 sentences on what this signals for monetary policy, market liquidity, or regulatory posture.
- source_url: REQUIRED, working link to the report/interview transcript
```

---

## Step 3 — Build the HTML Brief

Write `/Users/syn/Financial-News/us/daily/brief-[DATE].html` — a self-contained warm-cream editorial theme page.
- CSS variables:
  ```css
  :root {
    --canvas: #fcf6f0;        /* FT Cream */
    --ceramic: #f5ebe0;       /* Darker Cream Zone */
    --white: #ffffff;
    --primary: #1B365D;       /* Deep Navy Blue for US */
    --primary-soft: #2E5A88;
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
  1. **📊 Market Indicators:** Beautiful horizontal status bar with the 4 indicators.
  2. **📰 Top 10 News:** click-to-expand cards (using simple script class-toggles). Expanded view reveals Summary + *Why it matters* + `↗ Reference` link.
  3. **🎙️ Stakeholder Statements:** rich cards for each statement showing Person, Message, Impact, and Reference.
  4. **📈 Corporate Earnings:** structured tables/cards for each earning report showing:
     - Company Name & Ticker
     - Reporting Period
     - Financial Summary (Revenue & EPS row)
     - Key Takeaways (drivers and guidance)
     - `↗ Reference` link at the bottom.

---

## Step 4 — Register in the Archive Hub

Update `/Users/syn/Financial-News/us/index.html` by prepending one `DIGESTS` object:
`{ date:"YYYY-MM-DD", file:"daily/brief-YYYY-MM-DD.html", topNews:"<headline of top news>" }`

---

## Step 5 — Save Markdown Copy

Write `/Users/syn/Financial-News/us/daily/brief-[DATE].md`:
```markdown
# US Market Brief — [DATE_LABEL]

## 📊 Market Indicators
- **S&P 500:** [value]
- **Nasdaq:** [value]
- **Dow Jones:** [value]
- **10-Year Treasury Yield:** [value]

## 📰 Top 10 Financial News
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
1. **[Company Name & Ticker]** — [period]
   - **Revenue:** [revenue]
   - **EPS:** [eps]
   - **Key Takeaways:** [key_takeaways]
   Reference: [source_url]
...
```

---

## Step 6 — Deploy & Report

1. Run the local deployment:
   ```bash
   /Users/syn/Financial-News/us/deploy.sh
   ```
2. Print a short summary in chat containing:
   - Date
   - Top indicators
   - Top news headlines
   - High-impact statements tracked by Headhunter
   - Key corporate reports tracked by Earnings Tracker
   - Output paths (HTML & MD)
   - Git deployment confirmation status.
