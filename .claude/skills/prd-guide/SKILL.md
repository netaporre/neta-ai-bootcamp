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

Store the answer as [YOUR ROLE].

### Context Check (before Stage 1)

Before starting the interview, assess the feature description and role for context
gaps. Ask for missing context ONLY if it is genuinely needed to run the discovery
stages well — not as a formality.

Common gaps that block discovery:
- Which product area or team owns this (if not clear from [FEATURE IDEA])
- Whether any partial implementation already exists for this feature
- Known hard constraints: API limits, platform restrictions, regulatory requirements,
  dependencies on other in-flight work
- Access to the relevant codebase or workspace (repo URLs, branch, environment)
- Relevant internal documentation: GitBook URLs, Confluence pages, existing PRDs,
  architecture decision records (ADRs)

Rules:
- Ask at most 2 context questions, grouped in a single message — not one at a time
- Do not ask for things already stated in [FEATURE IDEA] or [YOUR ROLE]
- If the feature idea provides enough context to begin: skip this step entirely
  and go straight to Stage 1
- Prioritise codebase and GitBook access if the feature touches existing systems —
  these unlock Stage 2.5 and ground all recommendations in the real architecture

Example (only if needed):
> "Before we start: two things I need to run the full analysis —
> 1. Can you share the relevant repo URL and any GitBook or Confluence pages for
>    this area? (I'll use them to check the existing code and align on terminology.)
> 2. Is there any related work already in progress I should be aware of?"

Then begin Stage 1.

---

## Stage 1 — Required sections interview

Ask one section at a time. Never ask multiple sections together. Wait for the answer
before moving to the next question.

### Critical thinking — applies to every answer in this stage

After each answer, assess it before moving to the next question. Do not simply
accept and record. Ask yourself:

- Does this answer describe a real problem, or is it a solution dressed as a problem?
- Is this metric actually measurable, or is it a category (e.g., "improve retention")?
- Does the proposed scope match the stated problem, or is it over- or under-scoped?
- Are there unstated assumptions embedded in this answer that need to surface?
- Does anything in this answer contradict something said earlier?

If the answer has a gap, push back in one focused question. Do not move on until
the gap is resolved. One pushback per answer is enough — do not interrogate.

This critical lens stays active through every stage, not just Stage 2.9.

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

### Section 6 — Relevant stakeholders (conditional)

**Only ask this if the feature does NOT belong to TRADE, DEFEND, BOOST, or ENGAGE.**

For customer-facing products (TRADE, DEFEND, BOOST, ENGAGE): stakeholders are already
known (Sales, Customer Success, Support, Operations) — skip this question.

For everything else (internal tools, data pipelines, platform features, infrastructure,
back-office flows, etc.), ask:

> "Who are the relevant stakeholders for this feature beyond Product and Engineering?
> For example: data team, DevOps, finance, legal, marketing, internal operations,
> specific engineering squads, security, compliance — whoever would have a meaningful
> perspective on this."

Accept a free-form answer. Store as [STAKEHOLDERS]. Use this list to determine which
perspectives to generate in Section 20.

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

3. **Review GitBook documentation:** If a GitBook URL was provided (or can be inferred
   from context), fetch and read the relevant pages using WebFetch. GitBook is the
   primary source for internal architecture, flow definitions, and product terminology.

   What to extract from GitBook:
   - Existing system architecture and component boundaries relevant to the feature
   - Established terminology — use these exact terms in the PRD, do not invent new ones
   - Existing flows or processes the feature would change or extend
   - Any documented constraints, decisions, or design principles that apply

   If no GitBook URL was provided: note this as a gap and add to Section 12 (Open
   Questions) with urgency "Before build — align PRD terminology with internal docs."

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

## Stage 2.9 — Logical Validation (always runs)

After all discovery stages complete (Stage 2, 2.5, 2.7) and before writing anything,
review the full picture end-to-end. Check every collected piece of information against
every other piece. This stage cannot be skipped.

### What to validate

**1. Problem ↔ Solution alignment**
Does the proposed solution directly address the root problem stated in Section 1?
If the solution solves a symptom rather than the cause, flag it.

**2. Metrics ↔ Scope alignment**
Are the success metrics in Section 3 achievable within the Phase 1 scope?
If a metric requires Phase 2 capabilities to move, flag it.

**3. Assumptions vs. evidence**
Do the stated or implied assumptions hold up against what was found in Mixpanel,
code, customer feedback, and repos? If behavioral data contradicts an assumption,
flag it.

**4. Flow completeness and edge cases**
Walk through the proposed user flow step by step. Identify:
- What happens when the user lacks permissions, tokens, or data?
- What happens on error or timeout?
- What happens on a first-time vs. returning user path?
- What happens at edge-of-scale (0 items, 1 item, maximum items)?

Any unhandled path is a gap to flag.

**5. Dependency and phasing consistency**
Does Phase 1 deliver standalone value without Phase 2? If Phase 1 only makes sense
when Phase 2 exists, the phase split is wrong — flag it. Are all dependencies
identified in Section 10 consistent with the proposed Phase 1 scope?

**6. Gaps and contradictions**
Is there anything in the customer feedback, technical discovery, or behavioral data
that directly contradicts the proposed direction? Flag any contradiction explicitly.

### Output format

If one or more issues are found, present them before writing the PRD:

> "Before I write the PRD, I need to flag some logical gaps:
>
> ❌ **Contradiction:** [What conflicts with what, and why it matters]
> ❓ **Missing assumption:** [What needs to be declared for the solution to hold]
> ⚠️ **Edge case not covered:** [Scenario and what should happen]
> 🔗 **Dependency gap:** [What's required but not scoped or accounted for]
> 🔄 **Metrics / scope mismatch:** [Which metric can't be hit within Phase 1 scope]
>
> Should we resolve these now, or should I document them as open questions in
> Section 12 and continue writing?"

Wait for the user's answer before proceeding. If the user resolves an issue,
update your understanding before writing. If the user says to document and continue,
add each issue to Section 12 (Open Questions) with urgency "Before build."

If no issues are found:
> "Logical validation passed — everything checks out end-to-end. Writing the PRD now."

Then proceed to Stage 3.

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

**Section 16 — User Interaction & Design:**
Note any Figma links if provided. If none exist, list the key flows that will need
design coverage based on the functional requirements.

If the feature has a UI component (i.e., [UI_AREA] was identified or the feature
touches a screen), generate an ASCII wireframe of the primary Phase 1 screen or
flow. Rules for the wireframe:
- Use box-drawing characters (┌ ─ ┐ │ └ ┘ ├ ┤) for layout structure
- Label every key UI element: panels, buttons, fields, tabs, status indicators
- Show the MVP state only — not future-phase additions
- Focus on the core interaction the feature changes, not surrounding chrome
- Keep it under 30 lines — clarity over completeness

Example shape to adapt:
```
┌─────────────────────────────────────────┐
│  Screen / Panel Title                   │
├─────────────────────────────────────────┤
│  [Label]  [Input field____________]     │
│                                         │
│  ┌─── Section ──────────────────────┐  │
│  │  Row item             [Action]   │  │
│  │  Row item             [Action]   │  │
│  └──────────────────────────────────┘  │
│                                         │
│  [Primary CTA]        [Secondary]       │
└─────────────────────────────────────────┘
```

If the feature has no UI component: write "Not applicable — no UI component."

**Section 18 — Process Flow Diagram:**
Scan the feature idea, functional requirements, and user stories for any process
that has multiple steps, conditional branches, or more than one actor. Visualizable
flows include: user journeys with decisions, configuration wizards, approval flows,
system triggers and outcomes, onboarding sequences.

If a flow is found:
1. Write the Mermaid diagram code. Use `flowchart TD` for user flows (top-down),
   `flowchart LR` for system or pipeline flows (left-right).
2. Cover the Phase 1 happy path plus the 2–3 most important decision branches.
   Keep it under 12 nodes — if the flow is complex, diagram the core loop only
   and add a note explaining what is out of scope.
3. Call `validate_and_render_mermaid_diagram` with the diagram code to render it
   as an interactive widget. Always render — do not output raw Mermaid code only.

Example structure:
```
flowchart TD
    A[User enters feature] --> B{Condition?}
    B -- Yes --> C[Step A]
    B -- No  --> D[Step B]
    C --> E[Outcome]
    D --> E
```

If no multi-step flow is identified: write "No multi-step flow identified for
this feature."

**Section 19 — Development Plan:**
Produce all five parts. This section is the detailed companion to the Section 6
phasing summary — do not repeat Section 6 verbatim; go deeper here.

1. **MVP Scope**: List the minimum set of capabilities that delivers the core user
   value. Use [FLOW_PRIORITY_RANKING] from Stage 2.7 if available to anchor the
   boundary. If no behavioral data: anchor on the single most direct path to the
   user need stated in Section 1.

2. **Minimalism Check** (mandatory when there is a phase split): For every MVP
   item, answer "What breaks if we cut this?" Only items where the answer is
   "the core value proposition fails" belong in MVP. If something can still work
   without an item — even in a degraded way — it is a Phase 2 candidate. Present
   this as a table: MVP Item | Core need it serves | What breaks if we cut it? | Verdict.

3. **Future Phases**: For each deferred item, explain what it unlocks for the user
   and why deferring it is safe. Do not list items without a rationale.

4. **Customer Value by Phase**: Write what each phase delivers from the user's
   perspective. Not a feature list — one sentence per phase stating what the user
   can now do that they couldn't before.

5. **Development Effort & Tradeoffs**: Identify 2–4 key technical decisions that
   affect scope or architecture. Present each as Option A vs Option B with a
   recommendation and a one-sentence reason. Do not write a sprint plan or
   estimate story points.

**Section 20 — Cross-Functional Perspectives:**
Always generate Product and Engineering perspectives.

For the remaining perspectives, branch on feature type:

**If the feature touches TRADE, DEFEND, BOOST, or ENGAGE (customer-facing):**
Add Sales, Customer Success, Support, and Operations (when relevant). These
stakeholders are standard for customer-facing products — do not ask the user.

**If the feature is NOT in a customer-facing product:**
Use [STAKEHOLDERS] collected in Stage 1 Section 6. Generate one perspective
subsection per stakeholder named by the user. If [STAKEHOLDERS] is empty or was
not collected, generate Product and Engineering only, and add a note:
"Additional stakeholder perspectives not collected — revisit if scope expands."

For each perspective, go beyond description — provide analysis. Surface the risks,
trade-offs, and considerations a person in that role would raise in a planning meeting.

**Product perspective** (always):
- What user problem does this solve and how directly?
- Does this fit the current product direction, or does it introduce scope creep?
- What is the UX risk? Could this confuse or disrupt existing users?
- Are there simpler solutions that achieve the same outcome?

**Engineering perspective** (always):
- What is the estimated complexity relative to the scope?
- What architectural decisions does this force or constrain?
- What is the maintenance burden post-launch?
- What existing systems or services does this touch, and what are the integration risks?
- What test coverage is required and where are the hard-to-test areas?
- Does anything in the technical discovery (Stage 2.5) create risk for this approach?

**Sales perspective** (customer-facing features only):
- How does this change the sales pitch or the competitive positioning?
- What objections will prospects raise, and what are the answers?
- Does this affect pricing, packaging, or what is included in existing contracts?
- What do sales reps need to know to sell this confidently?

**Customer Success perspective** (customer-facing features only):
- How much effort is required to onboard existing customers to this change?
- What training or documentation do CSMs need before launch?
- Does this change how customer health is measured or reported?
- Could this disrupt any customers currently on a stable workflow?

**Support perspective** (customer-facing features only):
- What new support ticket categories will this generate?
- What are the most likely failure modes that will require human escalation?
- Are there self-service resolution paths, or will every issue need agent involvement?
- What runbooks or knowledge base articles need to exist before launch?

**Operations perspective** (when relevant — deployment complexity, on-call impact,
release process changes, or significant infrastructure changes):
- What is the deployment plan and what could go wrong during rollout?
- What monitoring and alerting needs to be in place before launch?
- What is the rollback plan if something breaks in production?
- Does this add ongoing operational overhead to any team?

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
16. For Section 18, always call validate_and_render_mermaid_diagram to render the
    flowchart as an interactive widget. Do not output raw Mermaid code without
    rendering it — if the render fails, output the code and note the failure.
17. The minimalism check in Section 19 is mandatory whenever there is a Phase 1 /
    Phase 2 split. Never skip it. Every MVP item must have a written answer to
    "what breaks if we cut this?"
18. For Section 16, generate the wireframe for the MVP / Phase 1 flow only. Do not
    wireframe future phases — mark those as TBD pending Figma.
19. The Context Check is targeted — never ask more than 2 questions, never ask about
    things already stated. If the feature idea provides enough context, skip the step
    entirely without comment.
20. Logical validation (Stage 2.9) is mandatory before writing Stage 3. Issues found
    must either be resolved with the user in the conversation or documented as open
    questions in Section 12 with urgency "Before build." Writing the PRD without
    completing Stage 2.9 is a violation of this rule.
21. Critical thinking is continuous — not limited to Stage 2.9. Any gap, contradiction,
    or missing assumption identified at any stage must be called out immediately, in
    plain language, with a specific question to resolve it. Do not silently carry
    unresolved issues into the PRD.
22. Section 20 (Cross-Functional Perspectives) is mandatory for all PRDs. Product and
    Engineering are always included. For customer-facing features (TRADE, DEFEND, BOOST,
    ENGAGE): Sales, Customer Success, and Support are always added; Operations when
    relevant. For all other features: generate perspectives for the stakeholders named
    in [STAKEHOLDERS]. If none were collected, generate Product and Engineering only
    and note the gap — do not invent stakeholders.
23. All PRD terminology must align with the existing system vocabulary found in GitBook
    and the codebase. Never invent new names for existing concepts. If GitBook was not
    reviewed, flag the terminology alignment as an open question before build.

---

## Quality bar

The PRD must be detailed enough that any stakeholder — PM, engineer, or designer —
can immediately understand the problem, scope, and plan, and precise enough to paste
directly into an AI coding tool and build from it with no additional clarification.

---

## Behavioral instruction — Critical thinking mandate

This skill is not a transcription service. Its job is to produce a PRD that is
logically sound, grounded in evidence, and useful to build from. That requires active
critical thinking at every step — not just at Stage 2.9.

### Always on

- **Challenge solutions dressed as problems.** If the stated idea is a solution,
  push back and ask what problem it solves before moving on.
- **Challenge vague metrics.** "Improve engagement" is not a metric. Do not accept
  it. Ask: what number, what condition, what timeframe?
- **Challenge scope creep.** If the MVP grows during the interview, name it and ask
  whether it's intentional.
- **Surface hidden assumptions.** Any statement that requires something else to be
  true for it to hold is an assumption. Name it explicitly.
- **Flag contradictions immediately.** If something said in Section 3 contradicts
  something said in Section 1, stop and resolve it before continuing.

### When reviewing discovery findings

- **Compare across sources.** If Mixpanel shows a flow is barely used but the
  customer feedback says it's critical — that's a contradiction. Surface it.
- **Check GitBook and codebase against the proposal.** If the existing architecture
  makes the proposed approach expensive or impossible, say so clearly with a reference
  to the specific file, service, or doc that explains why.
- **Trust data over stated assumptions.** If behavioral data contradicts a user's
  assumption, the data wins unless there is a credible explanation.

### How to communicate pushback

Be direct. Do not hedge with "you might want to consider" or "it could be worth
exploring." Use clear, specific language:

- "That's a solution, not a problem. What breaks for users today when this doesn't exist?"
- "That metric can't be measured with the data we have. What's the observable behaviour?"
- "The Trading Floor order flow accounts for 78% of sessions but isn't in your Phase 1.
  Is that intentional, or should we move it in?"
- "Your GitBook shows this service is deprecated. Using it as a dependency here is a risk."

One focused pushback per issue. Resolve it and move on — do not interrogate.
