# Tool 14 · AP Meeting Briefing + Roadmap Updater (NEW)

**Stage:** 4 (Account Partner / Decision Partner)
**Trigger:** Push (fires when AP attaches a Granola transcript or completes a meeting log form)
**Priority:** Phase 3.

---

## What it does

After every meeting an Account Partner has with a member, this tool:

1. Generates a **structured briefing doc** from the meeting transcript. **Relationship-first, not hiring-first** — the doc captures the founder's story, what energizes them, what drains them, business mechanics, team dynamics, and the roadmap. Tone matches the v2 onboarding call structure ([see structure doc](../ops/ap-onboarding-call-structure.md)).
2. **Updates the member's hiring roadmap** with anything new mentioned about future hires.
3. **Updates the member's AI agents roadmap** with anything new about planned AI builds.
4. **Posts the briefing into the Member Portal** so the member can see what was discussed.
5. **Surfaces new demand signals** to Tool 10 (which writes them into the Demand Signals table → flags Sourcing).
6. **For onboarding calls specifically:** generates the **24-hour commitment doc** the AP promised in Block 6 — the founder's "why" in their own words, the 90-day plan for hire #1, the strategic frame for hires #2 and #3.

## Why it matters

Today, every meeting's value lives in the AP's head. This tool makes that value durable: the member sees a written record, Sourcing gets early warning on incoming hires, Sagan builds an institutional memory of every account.

---

## Input

```
{
  "member_id": 17,
  "ap_id": 4,
  "meeting_date": "2026-05-14",
  "meeting_type": "Onboarding" | "Touchpoint" | "Quarterly Review",
  "transcript_url": "https://granola..."  // or transcript text
}
```

## Output

Three writes:

**1. Briefing doc** (Markdown or HTML, hosted in Member Portal):

```
# Briefing — Acme Corp × Sagan — May 14, 2026

## Discussed
- Q3 hiring plan: 2 SDRs, 1 controller
- Concerns about onboarding speed for last placement

## Decisions
- Move forward with Maria for the SDR role
- Pause the bookkeeper search for 30 days

## Open items
- Send updated salary bands by Friday

## Next steps
- AP follow-up call in 2 weeks

## Signals captured for Sourcing
- 2 SDRs needed by Q3 (Confidence: High)
- 1 Controller anticipated late Q3 (Confidence: Medium)
```

**2. Roadmap updates** to the Member record (`hiring_roadmap`, `ai_agents_roadmap` long-text fields, append with timestamp).

**3. Demand Signals** created (or queued for Tool 10 to formalize).

---

## MVP scope

- Form-input version first (AP fills a meeting form); Granola transcript in v2
- Generates briefing, writes it to a Member Portal URL
- Updates `hiring_roadmap` and `ai_agents_roadmap` (append, not overwrite)
- Creates Demand Signals records for forward-looking hires

## What it deliberately doesn't do

- Doesn't replace the AP. AP reviews the briefing before it's shared with the member.
- Doesn't auto-create HRs from signals. Signals are predictions; HRs are formal requests.

## Build estimate

- v1: 5–7 days
- v1 with Granola integration: +2–3 days

---

## Why this is the key tool for Stage 4

Stage 4 is the most freestyle stage. This tool gives it just enough structure to be useful without killing the relationship-driven nature of the work.
