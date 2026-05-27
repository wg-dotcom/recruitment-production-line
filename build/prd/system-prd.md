# Recruitment Production Line — System PRD (First Pass)

**Author:** Jesus Pacheco
**For:** Infra / Build team
**Status:** First-pass PRD. Companion to the video walkthrough.
**Related:** [Full proposal](../../index.html) · [Database schema](../database/schema.md) · [AI tools specs](../ai-tools/README.md)

---

## Purpose of this document

We're rebuilding recruitment as a production line: four stages, each owning one clear output, the output of one stage feeding the next. This PRD describes the **system the team needs to run that line**.

This is deliberately written as **jobs-to-be-done, not screens or modules.** I'm not telling you "I want a sourcing screen." I'm telling you what each person needs to *accomplish*. You design the interface around the jobs.

For every job, I've noted **how it's done today** so you can see what we're replacing or improving.

---

## How to read this

- **Roles** = the kinds of users who log in.
- **Jobs to be done** = the tasks each role needs to complete. Phrased as "X should be able to ___."
- **Today** = the current workflow/tool for that job (Airtable, dashboards, manual steps).
- **Supporting AI tool** = which planned tool, if any, accelerates that job. Full specs in [`../ai-tools/`](../ai-tools/README.md).

---

## The roles / users (overview)

| Role | Stage | One-line purpose | Count (rough) |
|---|---|---|---|
| **Sourcer** | 1 | Fill the funnel with raw candidates | A few |
| **Candidate Ops Specialist** | 2 | Turn raw candidates into presentation-ready assets | New team |
| **Talent Advisor (TA)** | 3 | Match, present, support interviews | Existing team |
| **Pod Lead** | 3 (mgmt) | Keep every HR on track across a pod of TAs | 2–3 |
| **Decision Partner** | 4 | Own the member relationship + strategic advice | New role |
| **System Owner / Stage Lead** | Cross | Monitor the line, fix bottlenecks, onboard | Jesus + leads |

---

## Role 1 · Sourcer (Stage 1)

**Who:** The people filling the top of the funnel. Active pipelines for high-frequency roles + an Assault Team for hard-to-fill specialty roles.

| # | The Sourcer should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 1.1 | Find candidates for the most-requested roles (EA, CSR, SDR, Bookkeeper, Accountant) at target volume | LinkedIn Recruiter + outbound tools, manual searching | — (human-led in v1) |
| 1.2 | Run outbound for specialty / hard-to-fill roles (the Assault Team) | Manual outbound, custom searches | — |
| 1.3 | Add a newly sourced candidate into the system | Manual entry into Airtable / current spreadsheet | — |
| 1.4 | Tag each candidate by role family + priority so the next stage knows what they're looking at | Manual, inconsistent tagging today | — |
| 1.5 | See which role families need more candidates (where's the pipeline thin?) | Gut feel / informal | Airtable view (Candidates grouped by role + Ready status) |

**Output of this role:** Raw candidate pool. Records created in the system at `lifecycle_status = Sourced`, tagged by role family and priority.

---

## Role 2 · Candidate Ops Specialist (Stage 2)

**Who:** A new team. Profile: efficient, async-first, follows instructions, attention to detail. They turn a sourced candidate into a presentation-ready asset.

| # | The Candidate Ops Specialist should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 2.1 | Take a sourced candidate and produce a polished resume | Manual editing in Docs/Canva, hours per candidate | **Tool 06 · Resume Polish Workflow** |
| 2.2 | Get a badass intro video from the candidate | Manual coordination, inconsistent quality | **Tool 07 · Intro Video Script Generator** |
| 2.3 | Confirm the candidate's availability and salary expectations | Manual outreach (email/WhatsApp), tracked in spreadsheet | **Tool 04 · Availability Bot** + **Tool 05 · Salary Refresh** |
| 2.4 | Promote a candidate to "Ready" (into the safety stock) once their package is complete | No formal gate today — candidates just get presented ad hoc | System: status validation on promotion to Ready |
| 2.5 | Keep candidates warm between opportunities so they don't go cold | Sporadic, manual, often forgotten | **Tool 08 · Warm-Keep Cadence** |
| 2.6 | Weekly: re-confirm availability of all Ready candidates so the database stays fresh | Not done systematically today — DB rots | **Tool 04 · Availability Bot** (scheduled) |

**Output of this role:** The ready-to-present database. Records at `lifecycle_status = Ready` with polished resume, video, confirmed availability + salary, role tags. **This team owns the database.**

---

## Role 3 · Talent Advisor / Recruiter (Stage 3)

**Who:** The existing TA team, now freed to focus only on recruiting (no more sourcing or chasing availability).

| # | The TA should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 3.1 | Receive a Hiring Request (JD) from a member | Airtable / Zach's dashboard / Slack, varies | System: HR intake |
| 3.2 | Find the best-matching candidates in the Ready database | Manual scanning of Airtable, hours per JD | **Tool 01 · JD ↔ Database Matching** |
| 3.3 | Build a polished presentation page for the member | Hand-built HTML from a template, copy-paste from resumes | **Tool 02 · Presentation Page Builder** |
| 3.4 | Prepare candidates ahead of member interviews | Manual coaching, inconsistent | **Tool 09 · Interview Prep Generator** |
| 3.5 | Support / run interviews with the member | Manual, calls | — |
| 3.6 | Capture a structured interview report after each interview | Free-text notes, often lost | **Tool 03 · Interview Report Creator** |
| 3.7 | Maintain the operational / HR-side relationship with the member | Email / Slack / calls | — |

**Output of this role:** Shortlisted candidates + interview intelligence. Up to 10 matched candidates per JD, presentation page, match rationale, interview reports, candidate insights (which feed back into the database via auto-enrichment).

---

## Role 4 · Pod Lead (Stage 3 management)

**Who:** Manages a pod of TAs. Keeps every HR moving. The figure that replaces ad-hoc, per-request micromanagement.

| # | The Pod Lead should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 4.1 | See every open HR across their pod and its current status | Manual check-ins, status meetings, scattered dashboards | System: HR pipeline view |
| 4.2 | Spot which HRs are stuck and at which stage | Reactive — find out when it's already late | **Tool 11 · Bottleneck Detector** |
| 4.3 | Balance workload across TAs (who's overloaded, who has capacity) | Gut feel | System: TA load view (open HRs per TA) |
| 4.4 | Know the next action needed on each HR without chasing the TA | 1:1 chasing | System: status + next-action field on HR |

**Output of this role:** Throughput. HRs flowing without stalls. The Pod Lead manages flow, not individual requests.

---

## Role 5 · Decision Partner / Account Growth (Stage 4)

**Who:** A new role. Sales-leaning, member-facing. Owns the strategic relationship and advises on the hire. (For White Glove customers, the same Advisor owns both Stage 3 and Stage 4.)

| # | The Decision Partner should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 5.1 | Understand each member's hiring roadmap and operations | In their head, scattered notes, Granola | System: Member roadmap field |
| 5.2 | Review the candidate shortlist + interview intel before advising the member | Manual, asks the TA | System: HR view with linked candidates + reports |
| 5.3 | Advise the member on which candidate to hire and why | Calls, relationship | — |
| 5.4 | Capture forward-looking demand signals from member conversations | Lost unless remembered | **Tool 10 · Demand Signal Analyzer** → Demand Signals table |
| 5.5 | Own account expansion + retention (spot the next hire, grow the account) | Relationship-driven, no system support | System: Member + open HRs view |
| 5.6 | See account health at a glance (placements, MRR, open roles) | Multiple dashboards / manual | System: Member dashboard |

**Output of this role:** Placement, retention, account expansion. Plus demand signals that feed back into Sourcing.

---

## Role 6 · System Owner / Stage Lead (cross-stage)

**Who:** Jesus + stage leads. Run the line, not the requests.

| # | The System Owner should be able to... | How it's done today | Supporting AI tool |
|---|---|---|---|
| 6.1 | See where the line is jammed (which stage has growing WIP) | Manual, multiple dashboards | **Tool 11 · Bottleneck Detector** |
| 6.2 | Track throughput + stage-level metrics (time-to-first-presentation, placement rate, etc.) | Zach's dashboard / Replit dashboard / manual reporting | System: metrics dashboard |
| 6.3 | Onboard a new team member into a single stage quickly | Ad hoc | — (process, not tool) |
| 6.4 | Audit the health of the database (freshness, decay, coverage) | Not done systematically | Airtable views + decay automation |

---

## The marquee metric

Across the whole system, the number that matters most is **time-to-first-presentation** — hours from a Hiring Request landing to the first candidate presented to the member. The entire production line exists to drive this down. It's a formula field on the Hiring Requests table, calculated automatically. (See [schema](../database/schema.md).)

---

## What the system must hold (data layer)

Full detail in the [database schema](../database/schema.md). In brief, 4 tables:

1. **Candidates** — the ready-to-present database (safety stock)
2. **Hiring Requests** — open member roles in flight
3. **Members** — the accounts
4. **Interview Reports** — structured interview output that auto-enriches Candidates

Recommendation: **Airtable as v1.** Already in use, MCP integration ready, no custom backend until volume forces it.

---

## What we're explicitly NOT building in v1

- A custom front-end app (Airtable Interfaces are enough for v1)
- Member-facing logins (members receive presentation pages as links, they don't log into the system)
- Automated sourcing (Stage 1 stays human-led in v1)

---

## Open questions for the infra team

1. **Where do the AI tools run?** Standalone scripts, an internal Sagan app, Airtable extensions, or a mix? (We have a per-tool view but want your take.)
2. **Airtable Interfaces vs. a light custom UI** — at what point does Airtable's native interface stop being enough?
3. **Auth / access** — Airtable's permission model vs. something we layer on top.
4. **Observability** — do we want a `tool_run_log` for debugging AI tool runs? (Not in the v1 schema yet.)
5. **Migration** — how much of the current Airtable / Zach's dashboard / Replit data do we carry over vs. start clean?

---

## How this connects to the rest

- The **why** and the full model: [proposal](../../index.html)
- The **data**: [schema](../database/schema.md)
- The **tools**: [AI tools specs](../ai-tools/README.md)
- This **PRD**: the bridge between them — who does what, and what they need to do it.
