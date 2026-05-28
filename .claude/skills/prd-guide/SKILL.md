---
name: prd-guide
description: Guide any team member through a structured interview to produce a complete, AI-coding-ready PRD. Invoke with /prd-guide. Do not auto-trigger.
disable-model-invocation: true
---

# prd-guide — Structured PRD creation via guided interview

## What this skill does

Takes a raw feature idea from any team member (PM, engineer, designer, or otherwise)
and guides them through a structured discovery interview. The output is a complete PRD
precise enough to paste directly into an AI coding tool and build from — no additional
clarification needed.

## Required reading

Before starting the interview, load the PRD output template:

- Read: `template.md` — the full PRD section structure used in Stage 3

## How to start

When invoked, ask:

> "What's the feature idea you want to build a PRD for? Give me a one-liner or a paragraph — doesn't matter how rough."

Store the answer as [FEATURE IDEA]. Then ask:

> "What's your role? (e.g., PM, engineer, designer, other)"

Store the answer as [YOUR ROLE]. Then begin Stage 1.

---

## Stage 1 — Required sections interview

Ask one section at a time. Never ask multiple sections together. Wait for the answer
before moving to the next question.

### Section 1 — Problem Statement

Ask:
> "What problem does this solve? Who experiences it, and what happens today when this
> feature doesn't exist?"

Push back if the answer is a solution description, not a problem description:
> "That sounds like a solution. What's the underlying problem it solves? What pain
> or gap exists today?"

### Section 2 — Goals & Success Metrics

Ask:
> "What does success look like once this ships? What would you measure, and what's
> the target — a number, a rate, a condition?"

Push back if the answer is vague:
> "'Improve engagement' is not a metric. What specific number or condition would tell
> you this feature worked?"

### Section 3 — Target Users

Ask:
> "Who benefits from this? What do they care about, and how do they currently work
> around the problem?"

### Section 4 — Repos & Documentation

Ask:
> "Are there any relevant repos or documentation I should review before writing the PRD?
> (e.g., GitHub repo URLs, Confluence pages, existing PRDs, API docs)"

Accept multiple links. If the user says none, note that and skip Stage 2.5.
If links are provided, store them as [REPOS_AND_DOCS].

### Section 5 — Product Area (for Mixpanel analysis)

Ask:
> "Is this feature related to a specific area of the ARENA360 UI? If yes, which product
> and screen? (e.g., TRADE / Trading Floor, BPS / Analysis, DEFEND / Player Management,
> BOOST / Coverage Matrix)"

If the user identifies a UI area: store as [UI_AREA] with [PRODUCT] (e.g., `Trade360`)
and [SCREEN] (e.g., `TradingFloor`). This triggers Stage 2.7.

If the user says no, unsure, or the feature has no UI component: note that and skip
Stage 2.7.

---

## Stage 2 — Confirmation

Before writing anything, summarize your understanding of all three sections:

> "Here's what I understood:
>
> **Problem:** [your summary]
> **Success looks like:** [your summary]
> **For:** [your summary]
>
> Is this correct, or should I adjust anything before writing the PRD?"

Do not proceed until the user confirms.

---

## Stage 2.5 — Technical Discovery (skip if no repos/docs provided)

If [REPOS_AND_DOCS] is populated, run this stage before writing the PRD.

### What to do

1. **Review each repo:** Read the codebase structure, search for files and modules
   relevant to the feature idea. Look for: existing implementations that overlap,
   related services or APIs, data models that will be affected, test coverage in
   the relevant area.

2. **Review each documentation link:** Read Confluence pages, existing PRDs, API docs,
   or any other linked material. Note what is already defined vs. what is missing.

3. **Identify gaps:** Compare what exists in code and docs against what the feature
   requires. Flag:
   - Existing code that conflicts with or constrains the proposed approach
   - Missing abstractions or services the feature will need
   - Undocumented behaviour the PRD must account for
   - Areas with no test coverage that the feature touches
   - Contradictions between existing docs and the proposed feature

### How findings feed into the PRD

- **Gaps in code coverage** → add to Section 10 (Dependencies & Risks)
- **Undocumented behaviour** → add to Section 5 (Assumptions) and Section 12 (Open Questions)
- **Existing implementations that overlap** → inform Section 6 (Scope & Phasing) and Section 14 (High Level Approach)
- **All findings** → populate Section 13 (Technical Discovery & Gap Analysis)

Do not invent findings. If a repo is clean and well-covered in the relevant area, say so.

---

## Stage 2.7 — Behavioral Discovery (skip if no UI area identified)

If [UI_AREA] is populated, run this stage before writing the PRD.

### Context to load first

Read `~/.claude/lsports-mixpanel/context/mixpanel-mapping.md` to understand:
- The event naming convention and which product prefix maps to [PRODUCT]
- The screen-level events that exist for [SCREEN]
- Global and event-specific properties

### What to query

Use the Mixpanel MCP tools to run the following analysis on [PRODUCT].[SCREEN]:

**Query 1 — Usage volume & trend**
Use `Run-Query` (or `Get-Events` to identify events first) to find:
- Total event count for [PRODUCT].[SCREEN].* events in the last 30 days
- Compare to the prior 30 days — is usage growing, flat, or declining?
- Use workspace `3740808` (Prod + Exclude LSports) for production user data

**Query 2 — Engagement breakdown**
- Which specific events in [PRODUCT].[SCREEN].* fire most often?
- Which events have near-zero volume (hints at abandoned or broken flows)?
- How many unique users are active in this area?

**Query 3 — Error & friction signals**
- Are there `ErrorToaster` events in this area? What's the error rate?
- Are there filter or search events with low follow-through?

**Query 4 — Related funnel (if applicable)**
- If the feature idea changes a specific flow, look for drop-off between the
  entry event and the completion event in that flow.

### How findings feed into the PRD

- **Usage volume + trend** → feed into Section 2 (Problem Statement) — quantify
  how heavily this area is used and whether it's growing
- **Low-volume events on important flows** → add to Section 10 (Dependencies & Risks)
  as evidence of abandoned paths or low adoption
- **Error rate** → add to Section 2 (Problem Statement) as current friction data
- **Unique active users** → use as baseline for Section 3 (Goals & Success Metrics)
- **All findings** → populate Section 14 (Customer Discovery) under a new
  "Mixpanel — Behavioral Data" subsection
- **Relevant existing Mixpanel dashboards found** → link in Section 17 (Analytics)

### How to report during the interview

After running queries, summarise to the user:

> "Here's what I found in Mixpanel for [PRODUCT] / [SCREEN]:
>
> **Usage:** [volume, trend]
> **Active users:** [count, period]
> **Top interactions:** [top 3 events]
> **Friction signals:** [errors, abandoned flows, or "none found"]
>
> I'll use this data to sharpen the Problem Statement and set metric baselines."

Do not invent Mixpanel findings. If an area has no matching events, say so —
it may mean the feature is entirely new territory with no existing instrumentation.

---

## Stage 3 — PRD output

Load `template.md` and produce the full PRD by filling in every section.

Follow these rules for each section:

**Sections 1–4:** Fill from the confirmed interview answers.
- Section 1 (Objective & User Statement): Derive from the problem and user answers —
  frame as "as a [role] / I want / so that".
- Section 2 (Problem Statement): If Stage 2.7 ran, sharpen this section with the
  Mixpanel usage volume, error rate, or drop-off data found. Cite the specific numbers
  — do not just say "data shows low adoption".
- Section 3 (Goals & Success Metrics): If Stage 2.7 ran, set Baseline values from
  Mixpanel data (active users, error rate, funnel conversion). Targets should be
  measurable deltas from these baselines.
- Section 5 (Assumptions): List what must be true for the solution to work. Flag
  anything unverified from the interview answers.

**Section 6 — Scope & Phasing:**
Analyze the feature idea and propose a Phase 1 / Phase 2 split.
Explain your reasoning. If the idea is small enough to ship in one phase, say so —
do not force a split.

**Sections 7–11:** Generate from the interview answers and your analysis.
For each section, show your reasoning — explain why something is flagged as optional
or phased.

**Section 13 — Technical Discovery & Gap Analysis:**
Populate from Stage 2.5 findings. If no repos or docs were provided, write:
"No repos or documentation were reviewed for this PRD."
Group findings under: Existing Code, Existing Docs, and Gaps Identified.
For each gap, reference the specific file, module, or doc where it was found.

**Section 12 — Customer Discovery:**
Search these sources before writing:
- #external-rca-for-customer (Slack, ID: C08J153BMU0)
- #voice-of-the-customers (Slack, ID: C05D4MYV162 — primary customer feedback channel)
- #product-insights (Slack, ID: C0AGY2ZJYPP — recordings and highlights from customer conversations)
- FeatureOS request board

Add a dedicated **"Mixpanel — Behavioral Data"** subsection using Stage 2.7 findings:
- Report the usage volume, active user count, top events, and friction signals found
- Cite the specific Mixpanel workspace and date range queried
- If Stage 2.7 was skipped (no UI area), write: "No Mixpanel analysis run — feature has
  no identified UI area."

For all sources: cite the channel/source name and link where available. Never paraphrase
without attribution. Never invent feedback or Mixpanel data.

**Section 13 — High Level Approach:**
Always generate two options with pros, cons, and trade-offs.

**Section 14 — User Interaction & Design:**
Note any Figma links if provided. If none exist, list the key flows that will need
design coverage based on the functional requirements.

**Section 15 — Analytics:**
Provide two parts:
1. Analytics Links — if Stage 2.7 found existing Mixpanel reports or dashboards
   relevant to this area, link them here. Mark new dashboards as TBD post-launch.
2. Instrumentation Plan — specific events and properties following the ARENA360
   naming convention `{Product}.{Screen}.{Element}.{Action}`. Read
   `~/.claude/lsports-mixpanel/context/mixpanel-mapping.md` for naming rules and
   existing property patterns to reuse.
Format: `Event: <event_name> | Properties: <prop_1>, <prop_2>, <prop_3>`
Cover: feature entry, key interactions, completion, and errors.
Do not repeat global super properties (env, version, source, platform, etc.) —
those are sent automatically.

---

## Rules

1. Ask one section's questions at a time — never dump all questions at once.
2. For sections 4–10, always show your reasoning: explain why you recommend a phase
   split or flag something as optional.
3. For Customer Discovery, always cite the source and link — never paraphrase without
   attribution.
4. For Analytics, define specific events and properties, not general categories.
5. Before writing the PRD, summarize your understanding and ask for confirmation.
6. Do not write the PRD until all three required sections are answered.
7. Do not invent customer feedback — if a source returns nothing relevant, say so explicitly.
8. Do not pad the scope — if the idea ships in one phase, do not force a split.
9. Do not use vague metrics — every success metric must have a number or a measurable condition.
10. Do not skip the confirmation step before writing.
11. Do not invent code or documentation findings — if a repo or doc has nothing
    relevant, say so explicitly.
12. Do not invent Mixpanel data — if a product area has no matching events or the
    query returns no data, report that explicitly. It may mean the area is
    uninstrumented, which is itself a useful finding.
13. For Analytics instrumentation (Section 17), always follow the ARENA360 naming
    convention from mixpanel-mapping.md — never invent a new naming pattern.

---

## Quality bar

The PRD must be detailed enough that any stakeholder — PM, engineer, or designer —
can immediately understand the problem, scope, and plan, and precise enough to paste
directly into an AI coding tool and build from it with no additional clarification.

---

## Behavioral instruction

Do not accept the feature idea at face value. If the stated idea is a solution, push
back and ask what problem it solves. The interview must surface the real need, not
just document what was asked for.
