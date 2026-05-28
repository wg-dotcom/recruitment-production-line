# Tool 16 · Member Intelligence Enricher (NEW)

**Stage:** 4 (Account Partner — pre-onboarding)
**Trigger:** Push (fires when a new Member record is created OR when an onboarding call is scheduled within 24h)
**Priority:** Phase 3. High-value for first impressions.

---

## What it does

Before the AP's first call with a new member, this tool produces a **deep Member Brief** the AP uses to walk in prepared.

**Critical framing:** the brief is built for a **relationship-first onboarding call** (see [`../ops/ap-onboarding-call-structure.md`](../ops/ap-onboarding-call-structure.md)). The AP's first 30 minutes are about bonding, founder story, business, and team — **not hiring**. The brief has to load up on personal hooks and founder context, not just role intel.

Pulls signal from three sources:

1. **The internet** — company website, recent news, LinkedIn (company + key leaders), industry context, hiring signals, founder's public footprint (podcasts, press, social, marathons, side projects, anything personal that gives the AP a real opener).
2. **Sales call transcripts** — every Granola transcript from the sales process (discovery, demo, close). Personal mentions matter as much as business mentions.
3. **Internal Sagan context** — patterns from similar members in our base for the roadmap hypothesis.

Output: a structured brief that turns the AP into a near-expert on the **person**, not just the company, before the call starts.

## Why it matters

The onboarding call is the **first impression** of the partnership. Today, APs walk in with whatever's in their head + a quick scan of the member's website. That's not enough. This tool gives them a deep, structured brief so the call feels like Sagan has *already* invested in the relationship. **Trust starts at minute one with a specific, personal reference — not a generic warm-up.**

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

## Personal hooks for Block 1 (Real Opening)
Pick one. Don't list them — pick the one that lands best.

- **Sarah ran the NYC marathon in November** (mentioned briefly in sales discovery). 3:47 finish.
- **Tom is a Cowboys fan** — saw a Cowboys background on his Zoom in the demo call.
- **Acme was featured in TechCrunch last Thursday** about their Series A. Big deal for them.
- **Sarah's son started kindergarten this fall** — mentioned in sales close call as the reason she wanted to wrap quickly.

## Founder story signal (Block 2 prep)
- **Sarah Chen, COO** — joined Acme as 4th hire, founder Mark Aldea kept her from her Salesforce days.
- Prior: 6 years at Salesforce in ops, MIT '15 (operations research), did a stint at a YC pre-seed that failed in 2020.
- Mark Aldea (CEO) is on the call but Sarah was the one Sagan sold to — Mark trusts her on hiring decisions.
- Public clue: Sarah's most recent LinkedIn post talked about "the difference between a great hire and a perfect resume." That's a hint about how she thinks.

## The business (Block 3 prep)
- B2B SaaS, sales engagement platform (Salesloft / Outreach competitor, smaller niche)
- Series A, $14M raised April 2026, 50 employees, HQ Austin
- Founded 2021, growing 80% YoY
- They're growing fastest in mid-market healthcare verticals (specific niche)
- Unfair advantage according to sales transcripts: "we're the only platform built for regulated industries"

## Team today (Block 4 prep)
- LinkedIn shows ~50 people. Recent hires: new VP Sales (Mark Stein, ex-Outreach), 6 SDRs in last 30 days
- Sarah's likely pain point: she can hire AEs but SDRs are killing her bandwidth
- Tom Riley (Head of Ops) joined 8 months ago — ex-McKinsey — likely owns vendor selection

## Roadmap hypothesis (Block 5 prep — surface if founder doesn't)
Based on Acme's stage + similar Sagan members:
- **Hire #1 (now):** SDR (LATAM, Spanish + English, healthcare context a plus)
- **Hire #2 (Q3):** CSR / onboarding specialist (their last 3 customers churned at onboarding per sales transcript)
- **Hire #3 (Q4):** Sales Ops / RevOps to scale the SDR team
- **AI agent build (possible):** prospect research agent — they mentioned manually building lists, this is gold for an AI build

## Sales call insights (verbatim quotes worth referencing)
- **Pain points:** "Our SDR onboarding is 6 weeks too slow" / "We can't find Spanish-speakers fast enough" / "We're spending too much on agencies"
- **Budget signal:** mentioned "$2k/month range" for SDRs in demo
- **Decision criteria:** "Quality over quantity. We don't want resume floods."
- **The locker line:** "If you can show me 3 great candidates in a week, we'll trial it" — but the v2 call goal is to make her trust the relationship, not just hit this transactional bar

## Industry context (for credibility moments)
- Sales engagement space is consolidating (Salesloft + Drift, Outreach IPO delayed)
- LATAM talent: SDR salaries up 12% YoY, healthcare-vertical familiarity is rare
- The "regulated industries" angle Acme owns is genuinely unique

## What this AP needs to bring personally
- One thing about you the founder doesn't already know. Be specific. Be human.
- A 24-hour commitment doc plan ready to mention in Block 6
- A roadmap hypothesis ready if Sarah doesn't have one

## What to NOT do
- Don't lead with the SDR hire. Block 5 is where that happens, minute 20+.
- Don't promise "3 candidates in a week" early — let Sarah ask, then commit confidently.
- Don't reference the sales transcripts directly ("you said X in the sales call"). Use the signals; don't show your homework.
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
