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

Do not proceed to Stage 3 until the user confirms.

---

## Stage 3 — PRD output

Load `template.md` and produce the full PRD by filling in every section.

Follow these rules for each section:

**Sections 1–4:** Fill from the confirmed interview answers.
- Section 1 (Objective & User Statement): Derive from the problem and user answers —
  frame as "as a [role] / I want / so that".
- Section 5 (Assumptions): List what must be true for the solution to work. Flag
  anything unverified from the interview answers.

**Section 6 — Scope & Phasing:**
Analyze the feature idea and propose a Phase 1 / Phase 2 split.
Explain your reasoning. If the idea is small enough to ship in one phase, say so —
do not force a split.

**Sections 7–11:** Generate from the interview answers and your analysis.
For each section, show your reasoning — explain why something is flagged as optional
or phased.

**Section 12 — Customer Discovery:**
Search these sources before writing:
- #external-rca-for-customer (Slack, ID: C08J153BMU0)
- #voice-of-the-customers (Slack, ID: C05D4MYV162 — primary customer feedback channel)
- #product-insights (Slack, ID: C0AGY2ZJYPP — recordings and highlights from customer conversations)
- FeatureOS request board

For each source: cite the channel/source name and link to the specific post or request
where available. If no relevant feedback is found in a source, state that explicitly.
Never paraphrase without attribution. Never invent feedback.

**Section 13 — High Level Approach:**
Always generate two options with pros, cons, and trade-offs.

**Section 14 — User Interaction & Design:**
Note any Figma links if provided. If none exist, list the key flows that will need
design coverage based on the functional requirements.

**Section 15 — Analytics:**
Provide two parts:
1. Analytics Links — placeholder for Mixpanel/BI dashboards (mark as TBD post-launch)
2. Instrumentation Plan — specific events and properties.
Format: `Event: <event_name> | Properties: <prop_1>, <prop_2>, <prop_3>`
Cover: feature entry, key interactions, completion, and errors.

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
