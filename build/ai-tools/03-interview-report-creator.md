# Tool 03 · Interview Report Creator + Auto-Enrichment

**Stage:** 3 → DB (the feedback loop)
**Trigger:** Push (fires when TA submits raw interview notes via form or Granola transcript URL)
**Priority:** High. Build third. This is what makes the database compound.

---

## What it does

Takes raw interview notes (form input or Granola transcript) and produces:

1. A structured Interview Report record (Strengths, Red Flags, Communication Style, Role Fit Signal, AI Summary).
2. **Auto-enriches the linked Candidate's profile** in the Ready database — strengths, red flags, communication style accumulate over time.
3. **Sends a feedback email to the candidate** — "you did great in X, focus on Y next time." Tone: encouraging, specific, actionable.

## Why it matters

This is the tool that turns the database from a static catalog into a living asset. Every interview makes future matches smarter. **This is the answer to the "Open question" on the database section of the proposal.**

---

## Input

Two possible sources:

**Source A — TA submits form:**

```
{
  "candidate_id": 312,
  "hr_id": 42,
  "interviewer_id": 7,
  "interview_round": "1st",
  "interview_type": "Screening",
  "raw_notes": "She was confident, talked about her HubSpot pipeline at length, mentioned she ghosted on her last team for 2 weeks..."
}
```

**Source B — Granola transcript URL:**

```
{
  "candidate_id": 312,
  "hr_id": 42,
  "interviewer_id": 7,
  "granola_url": "https://...",
  "interview_round": "Member-led",
  "interview_type": "Behavioral"
}
```

---

## Output

Two things:

**1. A new `Interview Reports` record:**

```
{
  "report_id": auto,
  "date": today,
  "candidate": 312,
  "hiring_request": 42,
  "interviewer": 7,
  "interview_round": "1st",
  "interview_type": "Screening",
  "strengths_observed": "Strong CRM fluency (HubSpot, Salesforce). Comfortable narrating pipeline. Communicates outcomes in numbers.",
  "red_flags_observed": "Mentioned unannounced 2-week absence at previous role — verify references on reliability.",
  "communication_style_rating": "Direct",
  "role_fit_signal": "Strong fit",
  "ai_summary": "Confident SDR with solid CRM stack. Worth a strong reference check on reliability.",
  "auto_enriched": false  // flips to true after step 2
}
```

**2. Auto-enrichment write-back to Candidate:**

The linked Candidate's `ai_summary_strengths`, `ai_summary_red_flags`, and `ai_communication_style` fields get **merged** (not overwritten). The enrichment logic:

- `ai_summary_strengths`: append the new strengths, deduplicate semantically (LLM call).
- `ai_summary_red_flags`: append, flag if a new flag matches an existing pattern (signal worth investigating).
- `ai_communication_style`: if the new rating matches existing, reinforce. If different, store both with dates.

After write-back, the Interview Report's `auto_enriched` flag flips to `true`.

---

## MVP scope (build this)

- Accepts form input only (skip Granola integration for v1)
- Generates the structured report fields from raw notes via single LLM call
- Writes the report record
- Auto-enriches the linked Candidate (append + dedupe, no semantic deduping in v1 — just append with timestamps)
- **Generates and sends candidate feedback email** with strengths + improvement areas (TA reviews before send in v1; auto-send in v2)

## v2 (skip for MVP)

- Granola transcript ingestion
- Semantic deduplication of strengths/red flags (avoid noise)
- Confidence scoring per insight
- Conflict resolution when new insight contradicts old (e.g. "communication style" changed)
- Alert when a candidate accumulates a pattern of red flags

---

## Prompt design (v1)

System: Sagan TA writing a structured interview report. Read raw notes. Extract: strengths, red flags, communication style, role fit signal. Be specific. Use the candidate's own examples. No corporate language. No em dashes as connectors.

Output: enforced JSON matching the report record schema.

For enrichment merge:

System: You're updating a candidate's profile with new interview data. Existing profile + new interview report → merged profile. Append unique insights with dates. Don't remove existing data. If new contradicts old, keep both with `[note: differs from earlier observation]`.

Output: merged `ai_summary_strengths` (long text), `ai_summary_red_flags` (long text), `ai_communication_style` (single select with confidence).

---

## Model

**Claude Opus 4.7** for the merge step (reasoning depth matters when reconciling multiple interview reports). Sonnet 4.7 for the initial extraction (fast, structured task).

---

## Integration points

- **Reads from:** Raw form input (or Granola URL in v2)
- **Writes to:** New `Interview Reports` record + linked `Candidates` enrichment fields
- **Triggered from:** Database UI form on TA's view
- **Logs to:** `tool_run_log` (TBD)

---

## What this deliberately doesn't do

- Doesn't change the candidate's lifecycle status. A red flag from an interview doesn't auto-pause the candidate. Candidate Ops still owns that decision.
- Doesn't decide if the candidate moves to next round. TA + Decision Partner do.
- Doesn't make recommendations. Surfaces signals.

---

## Build estimate (rough)

- v1 working (form input only, append-not-merge): 4–5 days
- v1 production with semantic merge: 1.5–2 weeks
- v2 with Granola ingestion: 2–3 days on top

---

## Why this is the killer feature

Tools 01 and 02 make the line work *today*. Tool 03 makes the line **smarter every day**. Six months in, every Ready candidate has a richer profile than what's on their resume. That's defensibility no competitor can copy without running the same volume.
