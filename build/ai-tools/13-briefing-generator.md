# Tool 13 · Briefing Generator (NEW)

**Stage:** 3 (Recruitment / TAs — intake step)
**Trigger:** Push (fires when a Hiring Request lands with kickoff transcript attached)
**Priority:** Phase 1. Precursor to Matching (Tool 01).

---

## What it does

Takes a Hiring Request's **JD + kickoff meeting transcript** (Granola URL) and produces a structured **internal briefing** the Matching tool can use. The briefing captures the things JDs always leave out — culture, tooling specifics, personality fit, deal-breakers — that come up in the kickoff conversation.

## Why it matters

Today, TAs read the JD, listen to the kickoff in their head, and search manually. The briefing makes that knowledge structured and AI-readable. Match quality goes up significantly because the matcher sees what the member *actually* said, not just what they wrote.

---

## Input

```
{
  "hr_id": 42,
  "jd_full": "Full JD text",
  "kickoff_transcript_url": "https://granola..."  // or transcript text
}
```

## Output

A structured briefing written back to the HR record as a new long-text field (`briefing_full`):

```
{
  "must_haves": ["5+ years SDR", "HubSpot daily", "English C2"],
  "nice_to_haves": ["Bilingual Spanish", "SaaS background"],
  "deal_breakers": ["No prior remote", "Won't take below 1500"],
  "culture_signals": "Member emphasized 'low-ego, ownership-driven' twice in kickoff",
  "tooling_specifics": "Apollo + Salesforce + Slack + Notion stack",
  "personality_fit": "Energetic, direct communicator preferred",
  "ambiguities_to_flag": "Salary band unclear — JD says 1500-2200 but in kickoff they mentioned flexibility upward"
}
```

This becomes the input to Tool 01 (Matching) alongside the raw JD.

---

## MVP scope

- Accepts JD text + Granola transcript URL or text
- Single LLM call (Claude Sonnet 4.7)
- Writes structured briefing to HR
- Manual trigger; auto-trigger on HR creation in v2

## What it deliberately doesn't do

- Doesn't decide who to match. That's Tool 01's job.
- Doesn't email the member for clarification on ambiguities — surfaces them to the TA.

## Build estimate

- v1: 2–3 days
- v1 with Granola integration: +2 days
