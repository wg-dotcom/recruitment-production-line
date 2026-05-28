# Tool 16 · Member Intelligence Enricher (NEW)

**Stage:** 4 (Account Partner — pre-onboarding)
**Trigger:** Push (fires when a new Member record is created OR when an onboarding call is scheduled within 24h)
**Priority:** Phase 3. High-value for first impressions.

---

## What it does

Before the AP's first call with a new member, this tool produces a **deep Member Brief** the AP uses to walk in prepared. Pulls signal from three sources:

1. **The internet** — company website, recent news, LinkedIn (company + key leaders), industry context, hiring signals (job posts on their site / LinkedIn).
2. **Sales call transcripts** — every Granola transcript from the sales process (discovery, demo, close).
3. **Internal Sagan context** — patterns from similar members in our base.

Output: a structured brief that turns the AP into a near-expert on the member before the call starts.

## Why it matters

The onboarding call is the **first impression** of the partnership. Today, APs walk in with whatever's in their head + a quick scan of the member's website. That's not enough. This tool gives them a deep, structured brief so the call feels like Sagan has *already* invested in the relationship. Trust starts at minute one.

---

## Input

```
{
  "member_id": 17,
  "member_name": "Acme Corp",
  "member_website": "https://acme.com",
  "sales_call_transcripts": [
    {"date": "2026-04-22", "type": "Discovery", "granola_url": "..."},
    {"date": "2026-04-29", "type": "Demo", "granola_url": "..."},
    {"date": "2026-05-06", "type": "Close", "granola_url": "..."}
  ],
  "key_contacts": [
    {"name": "Sarah Chen", "title": "COO", "linkedin": "..."},
    {"name": "Tom Riley", "title": "Head of Ops", "linkedin": "..."}
  ],
  "call_scheduled_for": "2026-05-15T16:00:00Z"
}
```

## Output

A structured Member Brief, written to a document (Markdown → published in the Member Portal as an internal-only doc + sent to the AP).

```
# Member Brief — Acme Corp — for onboarding call May 15

## Snapshot
- B2B SaaS, sales engagement platform
- Series A, $14M raised, 50 employees, HQ Austin
- Founded 2021, growing 80% YoY

## Recent signals (last 90 days)
- Hired new VP Sales (Mark Stein) from Outreach in April
- Posted 6 SDR roles on LinkedIn last 30 days
- Launched Acme AI (LLM-powered sales coach) in March
- Likely scaling team to support product expansion

## Leadership on the call
- **Sarah Chen, COO** — ex-Salesforce ops, MIT '15. Speaks fast, data-driven. LinkedIn shows strong "outcomes" orientation. Likely the decision-maker on operational hires.
- **Tom Riley, Head of Ops** — joined 8 months ago, ex-McKinsey. Will likely focus on process and tools questions.

## Sales call insights
- **Pain points raised:** "Our SDR onboarding is 6 weeks too slow" / "We can't find Spanish-speakers fast enough" / "We're spending too much on agencies"
- **Budget hints:** mentioned "$2k/month range" for SDRs in demo
- **Decision criteria:** "Quality over quantity. We don't want resume floods."
- **Quotes worth remembering:** "If you can show me 3 great candidates in a week, we'll trial it"

## Industry context
- Sales engagement space is consolidating (Salesloft + Drift, Outreach IPO delayed)
- Competitors hiring aggressively for senior SDRs
- LATAM talent is increasingly attractive for English-fluency + cost balance
- Talent market: SDR salaries in LATAM up 12% YoY

## Anticipated needs (based on similar Sagan members)
- 4–8 SDRs over next 6 months
- 1 CSR / onboarding specialist
- Possibly a sales ops / RevOps role mid-year
- AI agents: prospect research, email personalization

## Recommended opening
"Sarah, congrats on the Mark Stein hire — sales engagement is a hot space right now. Before we get into your roadmap, walk me through what's working and what's not with your current SDR motion."

## Gaps to confirm in the call
- [ ] Confirm budget range (sales call hint vs. actual)
- [ ] Confirm time zone preferences (LATAM vs. US hours)
- [ ] Confirm decision-making process (who signs off on hires)
- [ ] Understand the Acme AI roadmap and whether AI agents are in scope
- [ ] Spanish-speaker requirement — hard requirement or nice-to-have?

## Questions worth asking
1. "What does success look like at the 6-month mark of working together?"
2. "Tell me about the last great hire you made. What did you love about how it went?"
3. "What's the one thing you're worried we'll get wrong?"
```

---

## MVP scope

- Pulls company website + LinkedIn profiles (via Perplexity API or similar) + Granola transcripts
- Single LLM call (Claude Opus 4.7 — reasoning depth matters here)
- Generates the structured Brief as Markdown
- Writes to a doc in Member Portal (AP-only visibility)
- Emails AP 24h before call with brief attached

## v2 (skip for MVP)

- Real-time refresh (re-pull signals 2h before call)
- Competitor analysis (deeper)
- Member-side share (the "we did our homework" angle)
- Auto-detect tone preference from sales transcripts
- Pre-populate `Members.notes` and `Members.hiring_roadmap` with educated guesses for AP to confirm

## Model

**Claude Opus 4.7.** Reasoning quality is the entire point of this tool. Don't optimize for speed — optimize for the AP walking into the call thinking *"holy shit, Sagan did their work."*

## Integration points

- **Reads from:** member record (Database), Granola transcripts, web (via Perplexity or similar search), LinkedIn (via official API or scraping where allowed)
- **Writes to:** new doc in Member Portal (internal-only), email to AP
- **Pairs with:** [`ap-onboarding-call-structure.md`](../ops/ap-onboarding-call-structure.md) — the brief is the prep for that call

## What this deliberately doesn't do

- Doesn't replace the call. It prepares the AP for it.
- Doesn't auto-populate the Member record. The AP confirms in the call before anything is committed.
- Doesn't share with the member (in v1). Internal-only.

## Build estimate

- v1: 1–1.5 weeks (the search integrations are the unknown)
- v2 with auto-refresh + member share: +1 week

---

## Why this is a high-leverage tool

Onboarding calls set the trajectory of the entire account. A great brief means a great call means a great first 90 days means a member who stays for years. The leverage is huge.
