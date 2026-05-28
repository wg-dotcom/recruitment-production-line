# Tool 15 · Supervisor Dashboard (Supply-Chain View) (NEW)

**Stage:** Cross-stage (Jesus + Stage Leads)
**Trigger:** Always-on web app
**Priority:** Phase 4. Build once the line is producing real data.

---

## What it does

The command center for whoever runs the production line. Three parts:

### Part A · Metrics dashboard
All the line's KPIs in one view, like the existing White Glove + Core dashboards but unified and stage-aware:
- **Marquee:** time-to-first-presentation (rolling 7/30/90-day)
- **Throughput:** placements per week, per stage
- **Inventory:** Ready candidate depth by role family + decay color distribution
- **Pipeline:** open HRs by stage, time-in-stage per HR
- **Account health:** revenue per member, open HRs, last touchpoint
- **AI tool runs:** how often each tool fired, success/fail rates

### Part B · Natural-language query field
A prompt box at the top of the dashboard. Jesus types things like:

- "How many SDR placements did we do in April for White Glove members?"
- "Which TAs have the longest time-to-first-presentation this month?"
- "Show me Members with no touchpoint in 45 days"
- "What's the conversion rate from Sourced → Ready for bookkeepers?"

The tool translates the question into an Airtable query (or SQL-like query against an Airtable mirror), runs it, returns the answer + a generated chart if visual.

### Part C · Kanban CRM-style view
HRs as cards, columns by status (`New` → `Matching` → `Presenting` → `Interviewing` → `Placed` → `Closed`). Drag-and-drop, filter by member, by TA, by Decision Partner. Treats the HR pipeline like a sales pipeline. Enables **forecasting**:
- "We have 14 HRs in Presenting, historic conversion to Placed is 65%, expected placements next 30 days = ~9"
- Revenue projections from open HRs × expected close rate

---

## Why it matters

Jesus runs the system, not the requests. This dashboard is how he sees the whole line at once and intervenes only where intervention adds value. The NL query field is the difference between a static dashboard and a thinking tool.

---

## Input

Reads from the entire Airtable base (all 7 tables) + tool run logs.

## Output

A web app (or Airtable Interface + extensions, if that's enough in v1). Three views: Metrics, NL Query, Kanban.

---

## MVP scope

- Phase 4 tool — build once we have 1–2 months of production data
- Phase 4a (Weeks 8–10): Metrics dashboard (similar to WG/Core dashboards)
- Phase 4b (Weeks 10–12): NL Query field on top of the dashboard
- Phase 4c (Weeks 12+): Kanban view + forecasting

## Model

- NL Query: Claude Opus 4.7 for reasoning about ambiguous questions + writing Airtable formulas/queries
- Forecasting: deterministic statistical model (no LLM needed); LLM for natural-language commentary on the forecast

## What it deliberately doesn't do

- Doesn't run the line. It surfaces info so humans run the line.
- Doesn't auto-act on insights. Surfaces them, Jesus decides.

## Build estimate

- Phase 4a (Metrics): 2 weeks
- Phase 4b (NL Query): 2–3 weeks
- Phase 4c (Kanban + forecasting): 2–3 weeks

---

## Why this is Jesus's tool

Every other tool serves a stage. This one serves the system owner. It's how the supply chain manager sees the supply chain.
