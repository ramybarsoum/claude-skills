# Completeness Audit & Verification

Run this audit after writing every spec, before presenting the final version to the user. The audit has three phases.

## Phase 1: Structural Completeness

Check every section exists and is non-empty:

### 7-Section Specs
- [ ] Overview states what the step does AND what it does NOT do
- [ ] Acceptance Criteria are numbered, each independently verifiable
- [ ] Constraint Architecture has all four quadrants (Must Do, Must Not Do, Prefer, Escalate)
- [ ] Every Must Not Do has a one-line failure mode explanation
- [ ] Task Decomposition has sub-tasks with Input/Output/Acceptance/Dependencies/Scope
- [ ] Evaluation Criteria are specific and measurable
- [ ] Definition of Done has exactly three conditions

### 14-Section Specs (all of the above, plus)
- [ ] Core Intent states what the system optimizes for
- [ ] Priority Hierarchy is explicitly ordered (when X conflicts with Y, X wins)
- [ ] Constraints are organized by failure mode, not by category
- [ ] Decision Authority Map has all three sections (Autonomous / Notify / Escalate)
- [ ] Quality Thresholds define the routine vs. high-stakes line
- [ ] Common Failure Modes have: what happened, root cause, correct approach
- [ ] Klarna Test is applied (optimizing for action, not label)

## Phase 2: Content Quality Audit

For each acceptance criterion:
- [ ] Can an independent observer verify this without asking anyone?
- [ ] Is the language specific (numbers, names, thresholds) or vague ("high quality", "fast")?
- [ ] Does it describe an observable outcome, not an internal state?

For each constraint:
- [ ] Is it traceable to a specific failure mode?
- [ ] Is it measurable/automatically verifiable?
- [ ] Apply the pushback: "If I removed this and let the AI decide, what's the worst case?"

For the Definition of Done:
- [ ] Are all three conditions independently testable?
- [ ] Do they cover correctness (right output), completeness (nothing missing), and auditability (provably happened)?

For [OPEN] items:
- [ ] Is every technology/architecture decision explicitly marked [OPEN]?
- [ ] Is every [OPEN] item assigned to a specific owner?
- [ ] Are there any implicit decisions that should be [OPEN] but aren't?

## Phase 3: Gap Detection (The Deep Dive)

These are the questions that surface hidden gaps. Run each one against the spec:

### Input Completeness
- "What are ALL the input types/channels/sources this step receives?"
- "For each input type: what does a malformed version look like? Is it handled?"
- "What happens when the input is empty, null, or missing required fields?"
- "What happens when the input is valid but unusual (very large, very small, unexpected encoding)?"

### Output Completeness
- "What are ALL the outputs this step produces?"
- "For each output: who consumes it downstream? What happens if it's wrong?"
- "Is there a state where this step produces no output at all? Is that handled?"

### Concurrency & Timing
- "What happens if two identical requests arrive simultaneously?"
- "What happens if a dependency this step relies on is slow or down?"
- "What happens if this step is re-run on an already-processed input?"
- "Is there a race condition between this step and any parallel step?"

### Failure & Recovery
- "For each external dependency: what happens when it returns an error?"
- "What is the retry behavior? Is it explicit or assumed?"
- "After a failure and recovery, is the system in a consistent state?"
- "Is there a failure mode that produces silent data corruption?"

### Scope Boundary Violations
- "Does this step do anything that belongs in the previous or next step?"
- "Is there logic here that duplicates logic in another step?"
- "Are there decisions being made here that should be made elsewhere?"

### The New-Hire Test
- "Could a capable new hire implement this with at most one clarifying question?"
- "What would they ask? That question's answer belongs in the spec."

## Phase 4: Cross-Step Consistency (Multi-Spec Projects)

When multiple specs exist for the same project:

- [ ] Output of Step N matches expected input of Step N+1
- [ ] No contradictory constraints between steps
- [ ] Entity/field names are consistent across specs
- [ ] State transitions are consistent (a state defined in one step is recognized in all others)
- [ ] [OPEN] items don't create circular dependencies between specs
- [ ] Parallel steps don't have conflicting write targets

## Audit Report Format

After running the audit, produce a summary:

```
## Spec Audit: [Step Name]

**Structural:** [PASS/FAIL] — [issues if any]
**Content Quality:** [PASS/FAIL] — [issues if any]
**Gap Detection:** [N gaps found] — [list]
**Cross-Step:** [PASS/FAIL/N/A] — [issues if any]

### Gaps Requiring User Input
1. [Gap description] — needs answer before spec is production-ready
2. ...

### Auto-Fixed Issues
1. [Issue] — [fix applied]
2. ...

### Recommendation
[READY FOR PRODUCTION / NEEDS N ANSWERS BEFORE PRODUCTION]
```
