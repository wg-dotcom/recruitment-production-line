# Tool 02 · Presentation Page Builder

**Stage:** 3 (Recruitment / TAs)
**Trigger:** Pull (TA clicks "Build presentation page" on an HR after matching is done)
**Priority:** High. Build right after Matching.

---

## What it does

Given a Hiring Request with linked matched candidates, generates a polished HTML presentation page (Sagan Passport style) ready to share with the member. One page per HR, up to 10 candidates per page.

## Why it matters

Today TAs hand-craft these pages from a template, copy-pasting from resumes, writing narratives, adjusting CSS. It takes hours per HR. The proposal calls for "present in minutes" — this is the tool that delivers on that.

## Existing baseline

We already have ~40 candidate presentation pages live across [saganpassport.com](https://saganpassport.com)-style URLs. The format works. Members love it. This tool replicates that quality automatically.

---

## Input

From Airtable:

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

- Reads HR + linked candidates from Airtable
- Generates HTML from a locked template (the existing Sagan Passport format)
- Per candidate: avatar (initial-based), location, salary range, tech stack tags, narrative paragraph (AI-generated from `ai_summary_strengths` + resume snippets), resume + video links
- Writes the file to the `wg-dotcom` GitHub org as a new repo or subfolder, auto-enables Pages
- Returns the URL, writes it back to the HR

## v2 (skip for MVP)

- Member-branded variant (member's logo, colors)
- Custom layout per role family
- Embedded video player vs. external link
- Member analytics (who viewed which candidate, how long)
- Public/private toggle with password protection

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
- **Triggered from:** Airtable Interface button
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
