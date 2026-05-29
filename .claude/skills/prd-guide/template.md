# PRD Template

Reference doc loaded by the prd-guide skill during Stage 3. Fill every section in order.
Sections 1–3 come from the confirmed interview. Sections 4–13 are model-generated.

---

## PRD: [FEATURE IDEA]

**Author:** [YOUR ROLE]
**Date:** [TODAY'S DATE]
**Status:** Draft

---

### 1. Objective & User Statement

[One sentence summarizing what we aim to achieve.]

As a [role]
I want to [specific action]
so that [value this delivers]

---

### 2. Problem Statement

[What pain or opportunity this addresses. Who experiences it. What happens today without
this feature. Reference data, customer feedback, or KPIs that show the problem.]

---

### 3. Goals & Success Metrics

[What success looks like. Specific, measurable targets — numbers or conditions, not
categories.]

| Goal | Metric | Baseline | Target Post-Launch | How to Measure |
|------|--------|----------|--------------------|----------------|
|      |        |          |                    |                |

---

### 4. Target Users

[Who benefits. Their role, motivation, and how the feature changes their workflow.]

---

### 5. Assumptions

[What you're assuming to be true for this solution to work. Flag anything unverified.]

- 

---

### 6. Scope & Phasing Recommendation

**Phase 1 — Must-have (ship this)**
- [Item]
- [Item]

**Phase 2 — Can wait**
- [Item]
- [Item]

**Reasoning:** [Why this split makes sense for this feature.]

---

### 7. Functional Requirements

[What the feature must do. Derived from interview answers and scope decision.]

1. [Requirement]
2. [Requirement]

---

### 8. Non-Functional Requirements

[Performance, security, compliance, scalability — only where relevant.]

- [Requirement]

---

### 9. User Stories / Acceptance Criteria

**Story 1**
As a [user], I want [action], so that [outcome].

Done when:
- [ ] [Condition]
- [ ] [Condition]

---

### 10. Dependencies & Risks

| Item | Type | Severity | Notes |
|------|------|----------|-------|
|      | Dependency / Risk | High / Medium / Low |  |

---

### 11. Timeline & Milestones

| Milestone | Phase | Notes |
|-----------|-------|-------|
|           |       |       |

---

### 12. Open Questions

| Question | Owner | Urgency |
|----------|-------|---------|
|          |       | Before build / During build / Nice to have |

---

### 13. Technical Discovery & Gap Analysis

**Repos reviewed**
[List each repo reviewed with a brief note on relevance, or: "No repos provided."]

**Documentation reviewed**
[List each doc or Confluence page reviewed, or: "No documentation provided."]

**Existing Code**
[What relevant code already exists. Reference specific files, modules, or services.]

**Existing Docs**
[What is already documented and relevant to this feature.]

**Gaps Identified**
[What the feature requires that doesn't exist yet. Reference specific files or docs
for each gap. Include: missing abstractions, absent test coverage, undocumented
behaviour, and any contradictions with the proposed approach.]

---

### 14. Customer Discovery & Source Links

**#external-rca-for-customer (Slack)**
[Relevant posts with direct links, or: "No relevant feedback found."]

**#voice-of-the-customers (Slack)**
[Relevant posts with direct links, or: "No relevant feedback found."]

**#product-insights (Slack)**
[Relevant highlights or recordings with direct links, or: "No relevant feedback found."]

**External customer channels (ext-*)**
[Relevant messages with channel name, timestamp, and direct quote.
Or: "No relevant posts found in ext- channels."]

**FeatureOS**
[Relevant requests with direct links, or: "No relevant requests found."]

**Mixpanel — Behavioral Data**
[Usage volume, active user count, top events, and friction signals from Stage 2.7.
Cite workspace (3740808 — Prod) and date range queried.
Or: "No Mixpanel analysis run — no UI area identified."]

---

### 15. High Level Approach

**Option A: [Name]**
- Description: [What this approach involves]
- Pros: [Why it's good]
- Cons / Trade-offs: [What it costs or risks]

**Option B: [Name]**
- Description: [What this approach involves]
- Pros: [Why it's good]
- Cons / Trade-offs: [What it costs or risks]

---

### 16. User Interaction & Design

- Figma: [link or "TBD"]
- Key flows requiring design: [list]

**Wireframe — MVP Flow** *(auto-generated; replace with Figma when ready)*

[ASCII wireframe of the primary Phase 1 screen or flow. Or: "Not applicable — no UI component."]

---

### 17. Analytics

**Analytics Links**
[Links to existing Mixpanel dashboards or BI reports relevant to this feature.
Add after launch.]
- Mixpanel: [link or "TBD — add post-launch"]
- BI dashboard: [link or "TBD"]

**Instrumentation Plan**

| Event | Properties |
|-------|------------|
| `feature_entry` | `user_id`, `entry_point`, `timestamp` |
| `[key_interaction]` | `user_id`, `[prop]`, `[prop]` |
| `feature_completed` | `user_id`, `duration_ms`, `outcome` |
| `feature_error` | `user_id`, `error_type`, `step` |

---

### 18. Process Flow Diagram

[Mermaid flowchart of the primary user or system flow for Phase 1.
Covers the happy path and key decision points.
Or: "No multi-step flow identified for this feature."]

---

### 19. Development Plan

**MVP Scope**
[The minimum set of capabilities that delivers the core user value.
Every item here survived the minimalism check below.]

**Minimalism Check**

| MVP Item | Core need it serves | What breaks if we cut it? | Verdict |
|----------|--------------------|--------------------------:|---------|
|          |                    |                           | Keep / Cut |

**Future Phases**

| Item | Phase | Why deferred | What it unlocks for users |
|------|-------|--------------|--------------------------|
|      |       |              |                          |

**Customer Value by Phase**

- **Phase 1:** [What the user can now do that they couldn't before — written from
  the user's perspective, not as a feature list.]
- **Phase 2:** [Additional capability unlocked.]

**Development Effort & Tradeoffs**

[2–4 key technical decisions that affect scope. Not a sprint plan — a guide for
the engineering conversation.]

| Decision | Option A | Option B | Recommended | Reason |
|----------|----------|----------|-------------|--------|
|          |          |          |             |        |

---

### 20. Cross-Functional Perspectives

**Product**
[Feature value, UX impact, roadmap alignment, and risks from a product standpoint.
What does this unlock? What does it cost in terms of product complexity or coherence?]

**Engineering**
[Technical complexity, architectural impact, maintenance burden, test coverage
requirements, performance considerations, and any risks introduced to the existing system.]

**Sales** *(customer-facing features — TRADE, DEFEND, BOOST, ENGAGE — only)*
[How does this change the sales story? What objections will arise? Does this affect
pricing, packaging, or contract terms? What does a rep need to know to position it?]

**Customer Success** *(customer-facing features only)*
[Onboarding complexity, training requirements, documentation needs, impact on customer
health metrics and renewal conversations.]

**Support** *(customer-facing features only)*
[Anticipated support ticket types, failure modes that will generate escalations,
documentation and runbook gaps, self-service vs. agent-assisted resolution.]

**Operations** *(when relevant)*
[Deployment complexity, monitoring and alerting requirements, rollback plan,
operational overhead added to on-call or release processes.]
