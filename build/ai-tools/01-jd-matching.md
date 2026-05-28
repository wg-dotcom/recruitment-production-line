# Tool 01 · JD ↔ Database Matching

**Stage:** 3 (Recruitment / TAs)
**Trigger:** Pull (TA clicks "Match" on a new Hiring Request)
**Priority:** Highest. Build first.

---

## What it does

Given a Hiring Request **enriched with the kickoff transcript** (via Tool 13 · Briefing Generator), returns the top 20 candidates from the Ready safety stock that match the JD, ranked by fit. Each result includes a match rationale explaining why.

## Why it matters

Without this, Stage 3 has to manually scan the Candidates table for every JD. The whole "present in minutes" promise of the production line depends on this tool working well.

---

## Input

From Airtable `Hiring Requests` table, one record:

```
{
  "hr_id": 42,
  "role_title": "Outbound B2B Sales Representative",
  "role_family": "SDR",
  "jd_full": "Full JD text...",
  "required_tech_stack": ["HubSpot", "Salesforce"],
  "salary_budget_low": 1500,
  "salary_budget_high": 2200,
  "priority": "Standard"
}
```

Plus access to the full `Candidates` table filtered to `lifecycle_status = "Ready"`.

---

## Output

A ranked list, written back to the `Hiring Request` record's `candidates_presented` and `match_output_raw` fields:

```
{
  "matches": [
    {
      "candidate_id": 312,
      "rank": 1,
      "match_score": 0.92,
      "rationale": "5 years SDR experience at SaaS companies. HubSpot + Salesforce daily. Salary 1800 mid-range fits budget. English C2.",
      "concerns": "Last refresh 18 days ago — verify availability before presenting."
    },
    ...
  ],
  "no_match_reason": null
}
```

If fewer than 3 strong matches exist:

```
{
  "matches": [...],
  "no_match_reason": "Only 2 strong matches in Ready pool. Recommend escalating to Assault Team for sourcing."
}
```

---

## MVP scope (build this)

- Reads HR + filtered Candidates table via Airtable MCP
- Sends one structured prompt to Claude with both
- Returns ranked list, writes back to HR record
- Surfaces low-match alerts to TA
- Manual trigger only (TA clicks)

## v2 (skip for MVP)

- Embedding-based pre-filter to reduce token cost on large Ready pools (>500 records)
- Auto-match on HR creation (push trigger)
- Multi-pass refinement
- Member-specific learned preferences

---

## Prompt design (v1)

System: Sagan recruiting matching expert. You receive a JD and a candidate pool. Return strict JSON: ranked top 10 with rationale and concerns. Penalize stale records (>30 days since refresh). Penalize candidates outside salary band by more than 15%. Reward language fit, tech stack overlap, and seniority alignment.

User: `{HR record}` + `{Candidate pool, filtered to Ready, max 200 records}`.

Output schema: enforced JSON.

---

## Model

**Claude Sonnet 4.7** for v1. Reasoning is the bottleneck, not speed. Sonnet handles structured reasoning well enough; Opus only if Sonnet's match quality is visibly weaker in testing.

---

## Integration points

- **Reads from:** `Candidates` (filtered Ready), `Hiring Requests` (one record)
- **Writes to:** `Hiring Requests.candidates_presented`, `Hiring Requests.match_output_raw`
- **Triggered from:** Airtable Interface button or internal Sagan CLI
- **Logs to:** `tool_run_log` (TBD)

---

## What this deliberately doesn't do

- Doesn't auto-present to the member. TA still reviews and decides.
- Doesn't write to candidate records. Read-only on the safety stock.
- Doesn't source new candidates. If pool is thin, escalates to Assault Team manually.
- Doesn't replace the TA's judgment. It produces a shortlist for review, not a final answer.

---

## Build estimate (rough, for Zaki to validate)

- v1 working: 2–3 days
- v1 production-ready (error handling, observability, UI button): 1 week
