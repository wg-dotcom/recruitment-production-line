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

## Output (v2 format — revised after Feli's feedback on the Molloy brief)

A **one-page** Member Brief. Under 3 minutes to read. The brief is a primer, not a script. It should leave the AP feeling prepared without feeling rehearsed.

**Six sections, in order:**

1. **Who is who** — one line per person on the call. Plain language. No jargon.
2. **What's the deal** — one paragraph max. The business + operator's situation, "explain it like I'm five." No industry buzzwords.
3. **Already covered in sales** — short bullet list of what NOT to re-ask
4. **Where to focus** — ONE focus for this call. Not a list. The single thing that matters.
5. **Questions worth asking** — the part the AP actually uses. Keep prominent. Group lightly by call block.
6. **What NOT to do** — short, sharp, no more than 5 bullets.

**Hard cuts from v1:**
- Hypothetical multi-hire roadmap (it made the AP worry the call would feel rehearsed — she didn't use it)
- Long industry context section
- Long quote lists from sales transcripts
- Multiple personal hooks (pick ONE and recommend it, don't list 4)
- "What the AP needs to bring personally" (too prescriptive)
- "Why this matters" closing section

**Optional appendix (one click away, not in the main brief):**
- Sources used (so AP can verify a fact mid-call if needed)
- Full sales transcript link

---

## Example brief (v2 format)

```
# Member Brief — Acme Corp

Onboarding call: May 15 · 2pm ET
Strategist: Feli · Sales: Johno


WHO IS WHO

• Sarah — COO. The person Sagan sold to. She decides hiring.
• Tom — Head of Ops. Joined 8 months ago. Cares about tools + process.
• Mark — CEO. Will be on the call. Trusts Sarah on hires.


WHAT'S THE DEAL

Sarah and Tom run sales engagement software for healthcare companies.
They have 50 people and they're growing fast. They hire a lot of SDRs and
they can't keep up. They want help, but they want quality — not a flood
of resumes. They specifically want LATAM Spanish-speakers.


ALREADY COVERED IN SALES — DON'T RE-ASK

• Their company stage, headcount, what they sell
• Their general budget ($2k/mo for SDRs)
• How Sagan pricing works
• They want quality over quantity


WHERE TO FOCUS

Understand WHY their last 3 SDR hires didn't work. That's the real story.


QUESTIONS WORTH ASKING

Block 1 (opening):
• "Congrats on the Mark Stein hire — how's the team adjusting?"

Block 2 (founder story):
• "What were you doing before Acme? What made you go all-in?"

Block 4 (the team):
• "Who's the person you couldn't run the business without — outside yourself?"

Block 5 (roadmap):
• "You're hiring SDRs now. What's the next seat after that?"


DON'T DO THIS

• Don't quote the $2k budget back to her — she said it once already.
• Don't pitch the Sagan platform — Johno covered it.
• Don't promise "3 candidates in a week" — Sarah said it but the goal is the relationship.
• Don't make Tom feel like a sidekick. Ask him directly about tools.
```

That's the entire brief. One page. Three minutes. Done.

---

## What changed from v1 → v2 (Feli's feedback)

Feli ran the v1 brief on the Molloy onboarding. Her feedback (June 2026):
- **Worked:** the questions to ask, the "what NOT to do" guardrails
- **Didn't work:** too long, too much info, hypothetical roadmap felt rehearsed and she didn't use it
- **Wanted:** "explain it like I'm 5," plain language, one focus, what shouldn't be repeated

v2 is the response. Full feedback note: [`../briefs/v1-feedback-feli.md`](../briefs/v1-feedback-feli.md). v1 example brief: [`../briefs/molloy-roofing-onboarding-brief.html`](../briefs/molloy-roofing-onboarding-brief.html) (kept as historical reference).

---

## Post-call: capture what the AP learned

The brief is the input. The AP also needs a **structured place to capture what they actually heard on the call** — names, who's been at the company a long time, who has the relationships, who pushes back. That's the raw material for the *next* touchpoint's brief.

v1 didn't have this capture layer. v2 pairs with **Tool 14 (AP Meeting Briefing)**, which now produces a `key_people_at_company` long-text field on the Member record that grows over time. Every call enriches it. By call #3, the next brief Tool 16 generates includes those names automatically.

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
