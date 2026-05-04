# GTM Analytics Agent

A two-layer GTM analytics prototype built to demonstrate why narrow, scoped AI agents beat general-purpose tools for business analytics use cases.

**Live demo:** https://gtm-analytics-agent-app.vercel.app

---

## What it does

**Layer 1 — Pipeline health dashboard**
Visualizes key B2B SaaS sales pipeline metrics across 1,000 synthetic deals:
- Win rate by segment
- Stage conversion rates with bottleneck flagging
- Pipeline coverage ratio vs target
- Weighted forecast (probability-adjusted by stage)
- Deal velocity (avg days in stage)
- Rep leaderboard by deal quality
- Avg deal size by industry

**Layer 2 — Conversational pipeline agent**
A narrow AI agent scoped to the pipeline dataset that answers "why" questions behind the numbers. Ask it why win rate dropped, which stage is causing slippage, or which segment is underperforming — it responds with specific numbers and one actionable recommendation per finding.

---

## Architecture

```
User question
     │
     ▼
index.html (frontend)
     │  POST /api/chat
     ▼
api/chat.js (Vercel edge function — proxy)
     │  Anthropic API call with pipeline context
     ▼
Claude Sonnet (narrow agent, scoped system prompt)
     │
     ▼
Answer with cited metrics
```

The key design decision: the agent's system prompt contains pre-computed pipeline metrics, not raw data. This makes responses fast, precise, and hallucination-resistant. The agent can only answer what's in its context — no making things up about data that isn't there.

---

## The thesis

Narrow agents scoped to a specific dataset and predefined metrics outperform general-purpose analytics tools for business users because:

1. **Speed** — no query writing, no waiting for compute
2. **Precision** — answers cite exact numbers from the dataset
3. **Guardrails** — agent refuses to speculate beyond the data
4. **Accessibility** — any business user can ask "why" questions in plain English

This prototype was built partly as an evaluation of [Do.ml](https://do.ml), a workflow builder for AI agents. The experiment surfaced real product feedback: no CSV upload support, manual approval gates on datasources, and write access not self-serve. The same two-layer architecture was rebuilt from scratch in a single session using Vercel serverless functions and the Anthropic API.

---

## Stack

- Frontend: vanilla HTML/CSS/JS, Chart.js, DM Sans + DM Serif Display
- Backend: Vercel edge function (Node.js)
- AI: Anthropic Claude Sonnet via REST API
- Data: synthetic B2B SaaS pipeline dataset (1,000 deals, generated via Mockaroo)
- Hosting: Vercel

---

## Dataset

Synthetic pipeline data generated with Mockaroo. Fields: opportunity_id, account_name, rep_name, industry, segment, stage, deal_size, days_in_stage, close_date, created_date, win_loss.

Stage distribution weighted to reflect realistic pipeline drop-off: Prospecting (26%) through Closed Won (7%) and Closed Lost (5%).

Raw dataset: [pipeline_clean.csv](https://raw.githubusercontent.com/darialevina90-model/gtm-analytics-agent/refs/heads/main/pipeline_clean.csv)

---

## Setup

1. Clone the repo
2. Deploy to Vercel
3. Add `ANTHROPIC_API_KEY` as an environment variable in Vercel project settings
4. Done — no build step, no dependencies to install
