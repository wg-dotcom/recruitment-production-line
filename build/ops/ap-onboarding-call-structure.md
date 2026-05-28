# Account Partner — Onboarding Call Structure

The first call between an Account Partner and a new member. **This is the first impression of the partnership.** Everything downstream — trust, hiring decisions, account growth — flows from how this call lands.

**Target length:** 60–75 minutes
**Frequency:** Once per new member, at deal close
**Output:** Populated Member record, hiring + AI agents roadmap, captured demand signals, first HR confirmed, next touchpoint scheduled.

---

## Before the call — Tool 16 generates the Member Brief

The AP doesn't walk into this call cold. **Tool 16 · Member Intelligence Enricher** runs 24h before the call and produces a deep brief covering:

- Company snapshot (what they do, stage, size, geographies)
- Leadership profiles (the people on the call, their backgrounds)
- Recent signals (funding rounds, hires, press, leadership changes)
- Sales call insights (pain points discussed, budget hints, decision criteria)
- Industry context (competitors, trends, talent market)
- **Anticipated needs** — educated guesses from similar Sagan members
- **Recommended opening** — what to lead with based on their context
- **Gaps to fill in the call** — questions where AI didn't have enough info

The AP reviews this 15 min before the call. Walks in ready.

---

## Call structure (8 sections)

### 1. Opening — set the tone (5 min)

- Warm intro. Personal.
- Frame Sagan's approach: "We're not a staffing agency. We're a production line built for outcomes. Today's goal is to build the foundation of our partnership."
- Lay out what the next 60 min will cover (transparency = trust).
- **Outcome:** member knows what to expect, AP knows who's in the room.

### 2. Member context — understand the business (15 min)

Open-ended discovery. The AP listens.

- What does the business actually do? (Get it in their words, not the website's words.)
- What stage are they in? Pre-revenue / scaling / mature?
- What's the org chart look like? Who reports to whom?
- What's working well in their operations right now?
- What's broken or slowing them down?
- What tools do they live in? (CRM, accounting, marketing, support stack)

**Outcome:** AP has the member's mental model. Populates `Members.notes` + opens hiring roadmap thinking.

### 3. Hiring roadmap — the 6–12 month picture (15 min)

The structured part. Three horizons:

| Horizon | What we ask |
|---|---|
| **Confirmed (0–90 days)** | What roles do you already know you need? |
| **Probable (3–6 months)** | What's coming based on your current trajectory? |
| **Possible (6–12 months)** | If everything goes to plan, what does the org look like? |

For each role captured:
- Role + seniority
- Budget range (USD)
- Urgency (Standard / Urgent / Specialty)
- Replacement vs. growth
- What's driving the timing (revenue milestone, fundraise, launch, etc.)

**Outcome:** `Members.hiring_roadmap` populated. Demand Signals captured for the Confirmed and Probable rows. First HR identified.

### 4. AI Agents Build roadmap — the parallel offering (15 min)

If Sagan is also building AI agents for this member:

- Are there processes ripe for AI agent automation?
- Which functions: sales, ops, support, finance, content?
- Build vs. buy preference?
- What's their current AI tooling? (Are they already using Claude / OpenAI / something else?)
- Risk tolerance: do they want experimental builds or proven workflows?

Same three-horizon framework: Confirmed / Probable / Possible.

**Outcome:** `Members.ai_agents_roadmap` populated. Cross-references to the hiring roadmap (e.g. "We thought we needed a CSR, but actually we want a CSR + a triage agent").

### 5. Cultural & personality fit (5 min)

For Stage 3 (Recruitment) to do its job, the AP needs to capture:

- How does the member describe their team's culture?
- What kind of personalities thrive vs. struggle with this member?
- Communication preferences (formal / casual, async / sync, written / verbal)
- Deal-breakers we should know about

**Outcome:** `Members.notes` enriched with culture signals. Match quality on Tool 01 improves significantly.

### 6. Working relationship — the operating model (5 min)

- Cadence: how often do we want to talk? (Default: bi-weekly touchpoints with the AP, weekly with the TA on active HRs.)
- Channels: Slack, email, calls? Where do they want updates?
- Decision-makers on hires: who signs off?
- Member Portal walkthrough — show them where everything lives.

**Outcome:** clear operating contract.

### 7. First action — commit to something concrete (5 min)

Never leave an onboarding call without:

- First HR confirmed (role, budget, timeline)
- Next touchpoint scheduled (calendar invite during the call)
- One thing the member is sending you (org chart, JD, intro to someone)
- One thing you're sending them (their populated Member Portal link, first candidate timeline)

**Outcome:** momentum. The partnership has tangible motion before the call ends.

### 8. Close — anchor the relationship (5 min)

- Reinforce: "You'll see your roadmap and our notes in the Member Portal by EOD."
- Brief recap of what was decided.
- Genuine close. No corporate goodbye.

**Outcome:** member walks away feeling heard and clear on what happens next.

---

## After the call — automation kicks in

**Tool 14 · AP Meeting Briefing + Roadmap Updater** runs automatically:

- Ingests the Granola transcript
- Generates a structured briefing doc
- Updates `Members.hiring_roadmap` + `Members.ai_agents_roadmap`
- Creates Demand Signals records for the Confirmed + Probable hires
- Posts the briefing into the Member Portal (visible to the member)
- Notifies Sourcing of new demand signals via Slack

**AP review:** 15 min after the call, AP reviews the auto-generated briefing, edits anything wrong, hits "Publish to Member Portal."

---

## Open question for the team

- Do we want to send the **Member Brief** (Tool 16 output) to the member *before* the call as a "we did our homework" signal? Or keep it internal?
  - **Pro:** signals seriousness and saves them having to explain basic facts
  - **Con:** anything we got wrong looks bad; better to confirm in the call
  - **My lean:** keep it internal for v1, revisit once the brief quality is consistently high.
