---
name: feature-spec-interview
description: "Interactive interview that produces AI-agent-executable feature specifications (NLSpec). Use when the user says 'write a feature spec', 'spec interview', 'dark factory spec', 'NLSpec', 'feature-spec-interview', or needs to create detailed behavioral contracts for any feature, pipeline step, or system component. Supports two modes: (1) Solo mode where one person handles both product and engineering decisions, (2) Split mode where PM writes behavioral contracts and Engineering fills technology decisions. Always uses AskUserQuestion for interactive interviewing. Produces specs precise enough that an AI agent or capable new hire could implement with at most one clarifying question."
---

# Feature Spec Interview

Interactive interview process that produces AI-agent-executable feature specifications. Based on the Dark Factory Framework's NLSpec methodology.

## Core Principle

Specs are behavioral contracts for AI agents, not delivery plans for humans. Every behavior requires three components: WHAT the system must do, WHEN (under what conditions), and WHY (the rationale that guides edge case decisions). Constraints are measurable invariants, not policy statements.

## References

- **Question banks**: See [references/question-banks.md](references/question-banks.md) for all interview questions organized by prompt level
- **Spec templates**: See [references/spec-templates.md](references/spec-templates.md) for 7-section and 14-section output formats plus NLSpec writing rules
- **Completeness audit**: See [references/completeness-audit.md](references/completeness-audit.md) for the 4-phase verification checklist

## Workflow Overview

```
Phase 0: Setup        → Mode selection, project intake, step identification
Phase 1: Interview    → Progressive question groups via AskUserQuestion
Phase 2: Draft        → Generate spec using appropriate template
Phase 3: Review       → User reviews draft, corrections applied
Phase 4: Audit        → Completeness audit + gap detection
Phase 5: Finalize     → Production-ready spec with audit report
```

---

## Phase 0: Setup

### Step 0.1 — Determine Mode

Use AskUserQuestion to ask:
- "Are you writing this spec solo (handling both product and engineering decisions), or split between PM and Engineering?"
  - **Solo**: One person makes all decisions. [OPEN] items are marked `[OPEN]` with a note on what's needed.
  - **Split**: PM writes behavioral contracts. Engineering fills technology decisions. [OPEN] items are marked `[OPEN — Engineering]`.

### Step 0.2 — Project Intake (first spec only)

If this is the first spec in a project, ask the three Phase 1 intake questions from the question bank:
1. Elevator pitch (2 sentences)
2. Who executes: AI agents, human engineers, or both?
3. Scope: full system or one step at a time?

Store the answers. Reference them in every subsequent spec.

If the user has already done project intake (previous specs exist), skip this step.

### Step 0.3 — Step Identification

Use AskUserQuestion to ask:
- "What step or feature are we speccing? Give me the name and a one-sentence description."
- "Does this step involve AI judgment calls (classification, routing, delegation, escalation)?"

Based on the answer, determine:
- **Structural step** (no AI judgment) → Use Prompt 1 only → 7-section format
- **Judgment step** (AI makes decisions) → Use Prompts 1+2+3 → 14-section format

If the user uploaded or referenced an existing document (PRD, feature doc, requirements), read it first. Extract what you can, then interview to fill gaps. Never generate a spec purely from a document without interviewing.

---

## Phase 1: Interview

**CRITICAL: Use AskUserQuestion for EVERY question group.** Do not generate answers. Do not assume. The interview surfaces implicit knowledge that no document contains.

Load [references/question-banks.md](references/question-banks.md) for the full question bank.

### Interview Flow

**For ALL steps (Prompt 1 — Specification Engineer):**

Work through Groups 1-6 sequentially. Ask 2-3 questions per AskUserQuestion call to keep the flow conversational without overwhelming. After each group, summarize what you heard and confirm before moving on.

1. **Group 1 — Desired Output**: What exists after this step? What's the one job? What's NOT in scope?
2. **Group 2 — Hard Constraints**: What must NEVER happen? Worst-case failure? Data sensitivity?
3. **Group 3 — Hidden Context**: Non-obvious environment facts? Surprising behaviors? Undocumented dependencies?
4. **Group 4 — Edge Cases**: Dangerous scenarios? Valid-but-unusual inputs? Recovery behaviors?
5. **Group 5 — Tradeoffs**: Where can quality yield to speed? What's sacred? Latency vs. correctness?
6. **Group 6 — Definition of Done**: How do you know it worked? Name three conditions.

**For judgment steps, ADD (Prompt 2 — Intent & Delegation):**

7. **Group 7 — Core Value**: What does this optimize for? What does "failed" look like?
8. **Group 8 — Decision Authority**: What's autonomous? What escalates? Where's the delegation boundary?
9. **Group 9 — Quality Thresholds**: Routine vs. high-stakes line?
10. **Group 10 — Special Handling**: True exceptions to normal rules?
11. **Group 11 — Pushback**: Construct a "build for AI agents" challenge specific to this step.

**For high-consequence steps, ADD (Prompt 3 — Constraint Architecture):**

12. **Group 12 — Failure Mode Extraction**: "What is the WORST thing that can go wrong?" Push for 3-5 specific scenarios. Then derive constraints from those scenarios only. Cut any constraint not traceable to a real failure.

### Interview Discipline

**The Grilling Principle**: When the user adds a constraint, HITL gate, or approval step, challenge it:
> "You said we're building for AI agents. [Restate constraint]. Why does [action] require [limitation]? Is that a real safety/compliance constraint, or defensive thinking?"

Keep the constraint only if removing it would cause real harm or a compliance violation. If the worst case is "the AI might do it differently than I would," remove it.

**The Klarna Test**: Before finalizing any classification or routing rule, ask: "Am I optimizing for the label/rule, or for the action it triggers?"

**No skipping**: Do not skip Groups 3 (Hidden Context) and 5 (Tradeoffs). These produce the most valuable spec content and are the groups most often rushed.

---

## Phase 2: Draft

After completing the interview, generate the spec.

### Template Selection

- Structural step (Prompt 1 only) → 7-section format from [references/spec-templates.md](references/spec-templates.md)
- Judgment step (Prompts 1+2+3) → 14-section format from [references/spec-templates.md](references/spec-templates.md)

### Writing Rules

1. Every behavioral statement uses NLSpec format: WHAT / WHEN / WHY
2. Every Must Not Do has a one-line failure mode explanation
3. Every [OPEN] item is explicitly labeled with an owner
4. Acceptance Criteria are numbered, each independently verifiable by an observer with no context
5. Definition of Done has exactly three conditions
6. Constraints are measurable invariants, not policy statements
7. No vague language: "high quality," "fast," "gracefully" are banned. Use numbers, thresholds, specific behaviors.
8. The "why" behind key decisions is always documented. Smart-but-wrong execution comes from knowing the rule but not the reason.

### Output Location

Save the spec to the user's preferred output directory. If working in a project with an existing spec structure, follow that convention. Otherwise, ask the user where to save it.

---

## Phase 3: Review

Present the draft to the user. Use AskUserQuestion with the post-draft review questions from the question bank:

1. "Is anything here that would surprise you to see in production?"
2. "Is anything here that would cause you to call a customer to explain?"
3. "Are any of the [OPEN] items actually already decided?"
4. "Did I miss any channel, input type, or stakeholder that exists in your real system?"

Apply corrections immediately. Document each correction with a brief note of what changed and why (this builds the project's correction log).

---

## Phase 4: Completeness Audit

Load [references/completeness-audit.md](references/completeness-audit.md) and run all four phases:

1. **Structural Completeness** — Every required section exists and is non-empty
2. **Content Quality** — Acceptance criteria are specific, constraints are measurable, [OPEN] items are labeled
3. **Gap Detection** — Input completeness, output completeness, concurrency, failure/recovery, scope boundaries, new-hire test
4. **Cross-Step Consistency** (if other specs exist) — Output/input matching, no contradictions, consistent naming

For every gap found, use AskUserQuestion to get the answer from the user. Do not fill gaps with assumptions.

### The New-Hire Test (Final Gate)

Read the entire spec and ask: "Could a capable new hire with no context implement this with at most one clarifying question?" If the answer is no, identify what they would ask. That answer belongs in the spec. Add it and re-check.

---

## Phase 5: Finalize

1. Apply all corrections and gap fills from Phases 3-4
2. Generate the audit report (format in [references/completeness-audit.md](references/completeness-audit.md))
3. Present the final spec + audit report to the user
4. Ask: "Is this spec ready for production, or are there remaining items to resolve?"

If remaining items exist, loop back to the relevant phase. If ready, save the final version.

For **split mode**: Remind the user which [OPEN] items need engineering input before the spec is execution-ready.

---

## Multi-Step Projects

When speccing multiple steps in sequence:

- Maintain a **decisions log** of decisions locked in earlier specs. Do not re-derive.
- Maintain a **corrections log** of corrections applied to earlier drafts. Patterns inform later specs.
- Check **cross-step consistency** after each new spec (Phase 4, check 4).
- Each step has one job. If a step description requires "and," it's probably two steps.
- Steps are written in order. Each fully specced before starting the next.
- Prompt levels are progressive: start with Prompt 1 for structural steps, add Prompt 2 when judgment appears, add Prompt 3 when consequences are high.
