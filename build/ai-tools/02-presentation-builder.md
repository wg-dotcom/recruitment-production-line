# Tool 02 · Presentation Page Builder

**Stage:** 3 (Recruitment / TAs)
**Trigger:** Pull (TA clicks "Build presentation page" on an HR after matching is done)
**Priority:** High. Build right after Matching.

---

## What it does

Given a Hiring Request with linked matched candidates, generates a polished HTML presentation page (Sagan Passport style) ready to share with the member. One page per HR. **The page mirrors the member's branding/colors** (logo, palette pulled from member record + NotebookLM context). **Includes a feedback widget per candidate** so the member can leave notes inline. **Detects 2nd, 3rd, Nth batches and renders them as additional tabs on the same page** rather than creating new URLs.

## Why it matters

Today TAs hand-craft these pages from a template, copy-pasting from resumes, writing narratives, adjusting CSS. It takes hours per HR. The proposal calls for "present in minutes" — this is the tool that delivers on that.

## Existing baseline

We already have ~40 candidate presentation pages live across [saganpassport.com](https://saganpassport.com)-style URLs. The format works. Members love it. This tool replicates that quality automatically.

---

## Input

From Database:

```
{
  "hr_id": 42,
  "member": "Acme Corp",
  "role_title": "Outbound B2B SDR",
  "candidates_presented": [
    {
      "candidate_id": 312,
      "full_name": "Maria Rodriguez",
      "location_city": "Buenos Aires",
      "primary_role": "SDR",
      "years_experience": 5,
      "salary_low_usd": 1600,
      "salary_high_usd": 1900,
      "resume_url": "...",
      "video_url": "...",
      "ai_summary_strengths": "...",
      "tech_stack": ["HubSpot", "Salesforce", "Apollo"]
    },
    ...
  ]
}
```

---

## Output

A complete HTML file written to a deployable location (GitHub Pages repo or Sagan-hosted bucket), plus a shareable URL written back to the HR record.

```
{
  "presentation_url": "https://saganpassport.com/acme-corp-sdr-batch1",
  "candidates_count": 4,
  "generated_at": "2026-05-14T14:32:00Z"
}
```

---

## MVP scope (build this)

- Reads HR + linked candidates + member record (for branding) + NotebookLM context (for tone/voice) from Database
- Generates HTML from a locked template (the existing Sagan Passport format)
- Per candidate: avatar (initial-based), location, salary range, tech stack tags, narrative paragraph (AI-generated from `ai_summary_strengths` + resume snippets + JD context), resume + video links
- **Member-branded:** pulls logo + accent color from member record, applies to header + accent areas
- **Feedback widget per candidate:** simple text area + thumbs up/down, posts back into Database on the linked HR record
- **Batch tab detection:** if the HR already has a presentation_url, the tool appends a new tab to the existing page instead of overwriting
- Writes to the Sagan member portal (see Member Portal note below), returns URL, writes it back to the HR

## v2 (skip for MVP)

- Custom layout per role family
- Embedded video player vs. external link
- Member analytics (who viewed which candidate, how long)
- Public/private toggle with password protection

## Member Portal integration

Output is published into the **revamped Sagan Member Portal**, not as a standalone URL. The portal is the member's home for all their HRs, briefings, and roadmaps. Each presentation page is a sub-view inside the portal, scoped to the member's account.

---

## Prompt design (v1)

The HTML structure is locked. Claude only fills in the narrative paragraphs per candidate.

System: Sagan recruiter writing a 2-3 sentence candidate narrative. Voice: confident, warm, specific. No corporate fluff. Use the candidate's actual experience and strengths. Reference the role they're being presented for. No em dashes as connectors.

User: `{candidate record}` + `{HR.role_title and JD highlights}` + `{Sagan voice guide excerpt}`.

Output: plain text narrative, ~50 words.

The HTML template is rendered by a deterministic templating layer (not the LLM). Only the narrative is LLM-generated.

---

## Model

**Claude Sonnet 4.7** for narrative generation. Lightweight task, fast turnaround matters more than reasoning depth.

---

## Integration points

- **Reads from:** `Hiring Requests` + linked `Candidates`
- **Writes to:** `Hiring Requests.presentation_url`, GitHub Pages repo
- **Triggered from:** Database UI button
- **Depends on:** Tool 01 having run successfully (candidates_presented must be populated)

---

## What this deliberately doesn't do

- Doesn't send the link to the member. TA + Decision Partner do that with context.
- Doesn't update candidate records. Read-only on safety stock.
- Doesn't choose which candidates appear. Matching tool already did that.

---

## Build estimate (rough)

- v1 working with locked template: 3–4 days
- v1 production with auto-deploy + URL writeback: 1 week
- v2 features: 1–2 weeks additional, as needed
