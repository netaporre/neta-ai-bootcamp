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

### 13. Customer Discovery & Source Links

**#external-rca-for-customer (Slack)**
[Relevant posts with direct links, or: "No relevant feedback found."]

**#voice-of-the-customers (Slack)**
[Relevant posts with direct links, or: "No relevant feedback found."]

**#product-insights (Slack)**
[Relevant highlights or recordings with direct links, or: "No relevant feedback found."]

**FeatureOS**
[Relevant requests with direct links, or: "No relevant requests found."]

---

### 14. High Level Approach

**Option A: [Name]**
- Description: [What this approach involves]
- Pros: [Why it's good]
- Cons / Trade-offs: [What it costs or risks]

**Option B: [Name]**
- Description: [What this approach involves]
- Pros: [Why it's good]
- Cons / Trade-offs: [What it costs or risks]

---

### 15. User Interaction & Design

[Links to Figma files, mockups, or design specs. If none exist yet, note that design
is pending and list the key flows that need design coverage.]

- Figma: [link or "TBD"]
- Key flows requiring design: [list]

---

### 16. Analytics

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
