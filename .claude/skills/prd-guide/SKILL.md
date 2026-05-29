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

### Flow Priority Ranking — required output of this stage

After running all queries, produce a ranked list of the user flows in [UI_AREA]
ordered by behavioral importance. This ranking directly drives the Phase 1 / Phase 2
split in Section 6.

For each flow, assess:
- **Volume:** how many events / unique users touch this flow per month?
- **Criticality:** is it on the primary path (e.g., order placement) or secondary (e.g., export)?
- **Friction:** does it have a high error rate or a visible funnel drop-off?
- **Feature overlap:** does the proposed feature change this flow directly?

Rank flows as:
- **Priority 1 (Phase 1 candidate):** high volume + the feature directly touches it
- **Priority 2 (Phase 2 candidate):** low volume, secondary path, or no friction signal
- **Not in scope:** flow exists but the feature has no impact on it

Store this ranking as [FLOW_PRIORITY_RANKING].

### How to report during the interview

After running queries, summarise to the user:

> "Here's what I found in Mixpanel for [PRODUCT] / [SCREEN]:
>
> **Usage:** [volume, trend]
> **Active users:** [count, period]
> **Top interactions:** [top 3 events]
> **Friction signals:** [errors, abandoned flows, or "none found"]
>
> **Flow priority for phasing:**
> - Phase 1: [flow A — reason], [flow B — reason]
> - Phase 2: [flow C — reason]
>
> I'll use this to drive the phase split and explain the reasoning in the PRD."

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

If Stage 2.7 ran and [FLOW_PRIORITY_RANKING] is available:
- Phase 1 must contain the flows ranked Priority 1 — the highest-volume paths the
  feature directly touches. The reasoning must cite the Mixpanel data: name the flow,
  its event volume or active user count, and why that makes it the right starting point.
- Phase 2 contains Priority 2 flows — lower-volume paths or secondary interactions.
  Again, cite the data: explain why deferring these is safe given their lower usage.
- Write the reasoning as prose inside the PRD, not as a footnote. Example:
  "We start with the Trading Floor order flow because it accounts for 78% of daily
  active sessions in this area (Mixpanel, last 30 days). The Export and Bulk Edit
  flows are deferred to Phase 2 — they have <5% of event volume and no observed
  error signal."

If Stage 2.7 did not run: reason from the interview answers and feature complexity.
Explain your reasoning either way. If the idea is small enough to ship in one phase,
say so — do not force a split.

**Sections 7–11:** Generate from the interview answers and your analysis.
For each section, show your reasoning — explain why something is flagged as optional
or phased. Wherever Mixpanel data informed the decision, write that explanation
inline in the PRD body. Do not hide behavioral rationale in a footnote or data
appendix — it belongs next to the decision it justifies.

**Section 13 — Technical Discovery & Gap Analysis:**
Populate from Stage 2.5 findings. If no repos or docs were provided, write:
"No repos or documentation were reviewed for this PRD."
Group findings under: Existing Code, Existing Docs, and Gaps Identified.
For each gap, reference the specific file, module, or doc where it was found.

**Section 14 — Customer Discovery:**
Run all four source searches before writing. Never skip a source — if it returns
nothing relevant, say so explicitly.

**Source 1 — Internal feedback channels (always search these three):**
- #external-rca-for-customer (Slack, ID: C08J153BMU0)
- #voice-of-the-customers (Slack, ID: C05D4MYV162)
- #product-insights (Slack, ID: C0AGY2ZJYPP)

Use `slack_search_public_and_private` with the problem-area keywords for each channel.
If a channel returns no results with narrow keywords, broaden the search terms once.

**Source 2 — External customer channels (ext-*):**

Step 1 — Discover which ext- channels exist:
```
slack_search_channels(query="ext-", channel_types="public_channel,private_channel", limit=20)
```
This returns all channels whose names contain "ext-". These are direct customer channels.

Step 2 — Search for relevant content across all discovered ext- channels:
Run `slack_search_public_and_private` with the core problem keywords. Do not
filter by channel — let the search return results across all channel types, then
identify which results come from ext- channels.

Example query pattern:
```
slack_search_public_and_private(
  query="[problem keyword 1] [problem keyword 2]",
  channel_types="public_channel,private_channel",
  sort="score"
)
```

Step 3 — If the broad search returns ext- channel results, cite them.
If the broad search returns no ext- results, re-run with broader synonyms
or related terms from the problem statement. One retry is sufficient — do not
loop more than twice.

Step 4 — For each ext- channel result that is relevant:
- Record the channel name, message timestamp, and direct quote
- Note whether the message is a question, complaint, feature request, or workaround

**Source 3 — FeatureOS request board:**
Search for requests related to the feature area. Cite the specific request URL
and upvote count where visible.

**Reporting rules for all sources:**
- Cite channel name and message link (or timestamp) for every finding
- Quote directly — do not paraphrase without attribution
- If a source has no relevant content: write "No relevant posts found in [source]"
- Never invent feedback

**Mixpanel — Behavioral Data subsection:**
Include Stage 2.7 findings here:
- Usage volume, active user count, top events, friction signals
- Cite the Mixpanel workspace (3740808 — Prod) and date range queried
- If Stage 2.7 was skipped: "No Mixpanel analysis run — no UI area identified."

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
   split or flag something as optional. If behavioral data drove the decision, say so
   with the specific number — not "data shows" but "Trading Floor accounts for 78%
   of sessions, so it goes to Phase 1."
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
14. Never hide behavioral reasoning in a data appendix. Every Mixpanel-driven decision
    (phase split, requirement priority, metric baseline, risk flag) must have its
    explanation written inline, next to the decision — as prose, not as a table row.
15. Always run the ext- channel discovery step before writing Section 14. Do not skip
    it on the assumption that there are no relevant channels — always check. If
    slack_search_channels returns no ext- channels, note that explicitly.

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
