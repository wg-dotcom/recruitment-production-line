# Tool 12 · Video Quality Verifier (NEW)

**Stage:** 2 (Candidate Ops)
**Trigger:** Push (fires when a candidate uploads a video) + Cron (re-check Ready DB videos monthly)
**Priority:** Phase 2.

---

## What it does

Watches a candidate's intro video, scores it on quality dimensions, and if it's below standard, **auto-emails the candidate with specific recording suggestions** so they can re-record.

## Why it matters

Today, Candidate Ops watches every video manually. At scale this is the bottleneck. A bad video also means a candidate never makes it into the Ready pool. This tool catches issues early and gives candidates the chance to fix them.

---

## Input

```
{
  "candidate_id": 312,
  "video_url": "https://...",
  "trigger_source": "upload" | "monthly_recheck"
}
```

## Output

A quality score + structured feedback:

```
{
  "overall_quality": "Pass" | "Borderline" | "Fail",
  "scores": {
    "audio_clarity": 0.85,
    "lighting": 0.90,
    "framing": 0.70,
    "energy": 0.80,
    "content_clarity": 0.75
  },
  "issues": ["Audio has background echo", "Lighting from behind, face in shadow"],
  "recommendations": "Record in a quieter room, face a window or light source, keep camera at eye level"
}
```

If `Fail`: triggers an email to the candidate with the recommendations + a link to Tool 07 (Intro Video Script) for guidance. Candidate Ops gets a task in their queue to follow up.

If `Pass`: marks the candidate's video as verified, no action.

---

## MVP scope

- Reads video URL, runs through a multimodal model
- Scores on the 5 dimensions
- Writes score + feedback to a new `video_quality_*` set of fields on the Candidate record
- Auto-emails on Fail
- Manual trigger + upload trigger; monthly cron re-check in v2

## Model

**Claude Sonnet 4.7 with vision** (or whichever multimodal Claude is current). For audio analysis, may need a transcription pre-step.

## What it deliberately doesn't do

- Doesn't reject the candidate. Just flags the video.
- Doesn't auto-record. Suggests, doesn't replace.

## Build estimate

- v1: 4–5 days (multimodal video analysis is the tricky bit)
