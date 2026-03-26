---
name: ship
version: 1.0.0
description: |
  Ship workflow: detect + merge base branch, run tests, review diff, bump VERSION, update CHANGELOG, commit, push, create PR. Use when asked to "ship", "deploy", "push to main", "create a PR", or "merge and push".
  Proactively suggest when the user says code is ready or asks about deploying.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
  - WebSearch
---
<!-- Originally from gstack by Garry Tan (https://github.com/garrytan/gstack) -->

## Step 0: Detect base branch

Determine which branch this PR targets. Use the result as "the base branch" in all subsequent steps.

1. Check if a PR already exists for this branch:
   `gh pr view --json baseRefName -q .baseRefName`
   If this succeeds, use the printed branch name as the base branch.

2. If no PR exists (command fails), detect the repo's default branch:
   `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`

3. If both commands fail, fall back to `main`.

Print the detected base branch name. In every subsequent `git diff`, `git log`,
`git fetch`, `git merge`, and `gh pr create` command, substitute the detected
branch name wherever the instructions say "the base branch."

---

# Ship: Fully Automated Ship Workflow

You are running the `/ship` workflow. This is a **non-interactive, fully automated** workflow. Do NOT ask for confirmation at any step. The user said `/ship` which means DO IT. Run straight through and output the PR URL at the end.

**Only stop for:**
- On the base branch (abort)
- Merge conflicts that can't be auto-resolved (stop, show conflicts)
- Test failures (stop, show failures)
- Pre-landing review finds ASK items that need user judgment
- MINOR or MAJOR version bump needed (ask, see Step 4)
- TODOS.md missing and user wants to create one (ask, see Step 5.5)
- TODOS.md disorganized and user wants to reorganize (ask, see Step 5.5)

**Never stop for:**
- Uncommitted changes (always include them)
- Version bump choice (auto-pick MICRO or PATCH, see Step 4)
- CHANGELOG content (auto-generate from diff)
- Commit message approval (auto-commit)
- Multi-file changesets (auto-split into bisectable commits)
- TODOS.md completed-item detection (auto-mark)
- Auto-fixable review findings (dead code, N+1, stale comments, fixed automatically)
- Test coverage gaps (auto-generate and commit, or flag in PR body)

---

## Step 1: Pre-flight

1. Check the current branch. If on the base branch or the repo's default branch, **abort**: "You're on the base branch. Ship from a feature branch."

2. Run `git status` (never use `-uall`). Uncommitted changes are always included, no need to ask.

3. Run `git diff <base>...HEAD --stat` and `git log <base>..HEAD --oneline` to understand what's being shipped.

4. Check review readiness: If you have a review readiness system, display the dashboard. If eng review is not cleared, ask the user whether to ship anyway, abort, or skip review for trivial changes.

---

## Step 2: Merge the base branch (BEFORE tests)

Fetch and merge the base branch into the feature branch so tests run against the merged state:

```bash
git fetch origin <base> && git merge origin/<base> --no-edit
```

**If there are merge conflicts:** Try to auto-resolve if they are simple (VERSION, schema.rb, CHANGELOG ordering). If conflicts are complex or ambiguous, **STOP** and show them.

**If already up to date:** Continue silently.

---

## Step 2.5: Test Framework Bootstrap

**Detect existing test framework and project runtime:**

```bash
# Detect project runtime
[ -f Gemfile ] && echo "RUNTIME:ruby"
[ -f package.json ] && echo "RUNTIME:node"
[ -f requirements.txt ] || [ -f pyproject.toml ] && echo "RUNTIME:python"
[ -f go.mod ] && echo "RUNTIME:go"
[ -f Cargo.toml ] && echo "RUNTIME:rust"
[ -f composer.json ] && echo "RUNTIME:php"
[ -f mix.exs ] && echo "RUNTIME:elixir"
# Detect sub-frameworks
[ -f Gemfile ] && grep -q "rails" Gemfile 2>/dev/null && echo "FRAMEWORK:rails"
[ -f package.json ] && grep -q '"next"' package.json 2>/dev/null && echo "FRAMEWORK:nextjs"
# Check for existing test infrastructure
ls jest.config.* vitest.config.* playwright.config.* .rspec pytest.ini pyproject.toml phpunit.xml 2>/dev/null
ls -d test/ tests/ spec/ __tests__/ cypress/ e2e/ 2>/dev/null
```

**If test framework detected** (config files or test directories found):
Print "Test framework detected: {name} ({N} existing tests). Skipping bootstrap."
Read 2-3 existing test files to learn conventions (naming, imports, assertion style, setup patterns).
Store conventions as prose context for use in Step 3.4. **Skip the rest of bootstrap.**

**If NO runtime detected** (no config files found): Use AskUserQuestion:
"I couldn't detect your project's language. What runtime are you using?"
Options: A) Node.js/TypeScript B) Ruby/Rails C) Python D) Go E) Rust F) PHP G) Elixir H) This project doesn't need tests.
If user picks H, continue without tests.

**If runtime detected but no test framework, bootstrap:**

### B2. Research best practices

Use WebSearch to find current best practices for the detected runtime:
- `"[runtime] best test framework 2025 2026"`
- `"[framework A] vs [framework B] comparison"`

If WebSearch is unavailable, use this built-in knowledge table:

| Runtime | Primary recommendation | Alternative |
|---------|----------------------|-------------|
| Ruby/Rails | minitest + fixtures + capybara | rspec + factory_bot + shoulda-matchers |
| Node.js | vitest + @testing-library | jest + @testing-library |
| Next.js | vitest + @testing-library/react + playwright | jest + cypress |
| Python | pytest + pytest-cov | unittest |
| Go | stdlib testing + testify | stdlib only |
| Rust | cargo test (built-in) + mockall | -- |
| PHP | phpunit + mockery | pest |
| Elixir | ExUnit (built-in) + ex_machina | -- |

### B3. Framework selection

Use AskUserQuestion:
"I detected this is a [Runtime/Framework] project with no test framework. I researched current best practices. Here are the options:
A) [Primary] -- [rationale]. Includes: [packages]. Supports: unit, integration, smoke, e2e
B) [Alternative] -- [rationale]. Includes: [packages]
C) Skip -- don't set up testing right now
RECOMMENDATION: Choose A because [reason based on project context]"

If user picks C, continue without tests.

If multiple runtimes detected (monorepo), ask which runtime to set up first, with option to do both sequentially.

### B4. Install and configure

1. Install the chosen packages (npm/bun/gem/pip/etc.)
2. Create minimal config file
3. Create directory structure (test/, spec/, etc.)
4. Create one example test matching the project's code to verify setup works

If package installation fails, debug once. If still failing, revert with `git checkout -- package.json package-lock.json` (or equivalent for the runtime). Warn user and continue without tests.

### B4.5. First real tests

Generate 3-5 real tests for existing code:

1. **Find recently changed files:** `git log --since=30.days --name-only --format="" | sort | uniq -c | sort -rn | head -10`
2. **Prioritize by risk:** Error handlers > business logic with conditionals > API endpoints > pure functions
3. **For each file:** Write one test that tests real behavior with meaningful assertions. Never `expect(x).toBeDefined()`, test what the code DOES.
4. Run each test. Passes, keep. Fails, fix once. Still fails, delete silently.
5. Generate at least 1 test, cap at 5.

Never import secrets, API keys, or credentials in test files. Use environment variables or test fixtures.

### B5. Verify

```bash
# Run the full test suite to confirm everything works
{detected test command}
```

If tests fail, debug once. If still failing, revert all bootstrap changes and warn user.

### B5.5. CI/CD pipeline

```bash
# Check CI provider
ls -d .github/ 2>/dev/null && echo "CI:github"
ls .gitlab-ci.yml .circleci/ bitrise.yml 2>/dev/null
```

If `.github/` exists (or no CI detected, default to GitHub Actions):
Create `.github/workflows/test.yml` with:
- `runs-on: ubuntu-latest`
- Appropriate setup action for the runtime (setup-node, setup-ruby, setup-python, etc.)
- The same test command verified in B5
- Trigger: push + pull_request

If non-GitHub CI detected, skip CI generation with note: "Detected {provider}. CI pipeline generation supports GitHub Actions only. Add test step to your existing pipeline manually."

### B6. Create TESTING.md

First check: If TESTING.md already exists, read it and update/append rather than overwriting. Never destroy existing content.

Write TESTING.md with:
- Philosophy: "100% test coverage is the key to great vibe coding. Tests let you move fast, trust your instincts, and ship with confidence."
- Framework name and version
- How to run tests (the verified command from B5)
- Test layers: Unit tests (what, where, when), Integration tests, Smoke tests, E2E tests
- Conventions: file naming, assertion style, setup/teardown patterns

### B7. Update CLAUDE.md

First check: If CLAUDE.md already has a `## Testing` section, skip. Don't duplicate.

Append a `## Testing` section:
- Run command and test directory
- Reference to TESTING.md
- Test expectations:
  - 100% test coverage is the goal
  - When writing new functions, write a corresponding test
  - When fixing a bug, write a regression test
  - When adding error handling, write a test that triggers the error
  - When adding a conditional (if/else, switch), write tests for BOTH paths
  - Never commit code that makes existing tests fail

### B8. Commit

```bash
git status --porcelain
```

Only commit if there are changes. Stage all bootstrap files (config, test directory, TESTING.md, CLAUDE.md, .github/workflows/test.yml if created):
`git commit -m "chore: bootstrap test framework ({framework name})"`

---

## Step 3: Run tests (on merged code)

Run the project's test suite. Adapt the command to whatever test runner the project uses.

After tests complete, check pass/fail.

**If any test fails:** Show the failures and **STOP**. Do not proceed.

**If all pass:** Continue silently, just note the counts briefly.

---

## Step 3.25: Eval Suites (conditional)

Evals are mandatory when prompt-related files change. Skip this step entirely if no prompt files are in the diff.

**1. Check if the diff touches prompt-related files:**

```bash
git diff origin/<base> --name-only
```

Match against prompt-related patterns (prompt builders, generation services, system prompts, eval infrastructure).

**If no matches:** Print "No prompt-related files changed, skipping evals." and continue to Step 3.5.

**2. Identify affected eval suites** by checking which eval runners reference the changed files.

**3. Run affected suites** at the highest judge tier available.

**4. Check results:**
- **If any eval fails:** Show the failures and **STOP**. Do not proceed.
- **If all pass:** Note pass counts and cost. Continue to Step 3.5.

**5. Save eval output** for inclusion in the PR body (Step 8).

---

## Step 3.4: Test Coverage Audit

100% coverage is the goal. Evaluate what was ACTUALLY coded (from the diff), not what was planned.

**0. Before/after test count:**

```bash
# Count test files before any generation
find . -name '*.test.*' -o -name '*.spec.*' -o -name '*_test.*' -o -name '*_spec.*' | grep -v node_modules | wc -l
```

Store this number for the PR body.

**1. Trace every codepath changed** using `git diff origin/<base>...HEAD`:

Read every changed file. For each one, trace how data flows through the code:

1. **Read the diff.** For each changed file, read the full file (not just the diff hunk) to understand context.
2. **Trace data flow.** Starting from each entry point (route handler, exported function, event listener, component render), follow the data through every branch:
   - Where does input come from? (request params, props, database, API call)
   - What transforms it? (validation, mapping, computation)
   - Where does it go? (database write, API response, rendered output, side effect)
   - What can go wrong at each step? (null/undefined, invalid input, network failure, empty collection)
3. **Diagram the execution.** For each changed file, draw an ASCII diagram showing:
   - Every function/method that was added or modified
   - Every conditional branch (if/else, switch, ternary, guard clause, early return)
   - Every error path (try/catch, rescue, error boundary, fallback)
   - Every call to another function (trace into it, does IT have untested branches?)
   - Every edge: what happens with null input? Empty array? Invalid type?

**2. Map user flows, interactions, and error states:**

For each changed feature, think through:
- **User flows:** What sequence of actions does a user take that touches this code?
- **Interaction edge cases:** Double-click, navigate away mid-operation, stale data, slow connection, concurrent actions
- **Error states the user can see:** Clear error message or silent failure? Can the user recover?
- **Empty/zero/boundary states:** Zero results, 10,000 results, single character, max-length input

**3. Check each branch against existing tests:**

Go through your diagram branch by branch. For each one, search for a test that exercises it.

Quality scoring rubric:
- Three stars: Tests behavior with edge cases AND error paths
- Two stars: Tests correct behavior, happy path only
- One star: Smoke test / existence check / trivial assertion

**4. Output ASCII coverage diagram** including both code paths and user flows.

**Fast path:** All paths covered, continue.

**5. Generate tests for uncovered paths:**

If test framework detected (or bootstrapped in Step 2.5):
- Prioritize error handlers and edge cases first
- Read 2-3 existing test files to match conventions exactly
- Generate unit tests. Mock all external dependencies.
- Run each test. Passes, commit as `test: coverage for {feature}`
- Fails, fix once. Still fails, revert, note gap in diagram.

Caps: 30 code paths max, 20 tests generated max, 2-min per-test exploration cap.

If no test framework AND user declined bootstrap, diagram only, no generation.

**6. After-count and coverage summary:**

```bash
# Count test files after generation
find . -name '*.test.*' -o -name '*.spec.*' -o -name '*_test.*' -o -name '*_spec.*' | grep -v node_modules | wc -l
```

For PR body: `Tests: {before} -> {after} (+{delta} new)`

---

## Step 3.5: Pre-Landing Review

Review the diff for structural issues that tests don't catch.

1. Read your project's review checklist if one exists.

2. Run `git diff origin/<base>` to get the full diff.

3. Apply the review checklist in two passes:
   - **Pass 1 (CRITICAL):** SQL & Data Safety, LLM Output Trust Boundary
   - **Pass 2 (INFORMATIONAL):** All remaining categories

4. **Classify each finding as AUTO-FIX or ASK:**
   - AUTO-FIX: Dead code, N+1 queries, stale comments, mechanical CSS fixes
   - ASK: Complex architectural concerns, security model changes, ambiguous intent

5. **Auto-fix all AUTO-FIX items.** Output one line per fix:
   `[AUTO-FIXED] [file:line] Problem -> what you did`

6. **If ASK items remain,** present them in ONE AskUserQuestion.

7. **After all fixes (auto + user-approved):**
   - If ANY fixes were applied: commit fixed files, then **STOP** and tell the user to run `/ship` again to re-test.
   - If no fixes applied: continue to Step 4.

8. Output summary: `Pre-Landing Review: N issues, M auto-fixed, K asked (J fixed, L skipped)`

---

## Step 4: Version bump (auto-decide)

1. Read the current `VERSION` file (4-digit format: `MAJOR.MINOR.PATCH.MICRO`)

2. **Auto-decide the bump level based on the diff:**
   - Count lines changed (`git diff origin/<base>...HEAD --stat | tail -1`)
   - **MICRO** (4th digit): < 50 lines changed, trivial tweaks, typos, config
   - **PATCH** (3rd digit): 50+ lines changed, bug fixes, small-medium features
   - **MINOR** (2nd digit): **ASK the user**, only for major features or significant architectural changes
   - **MAJOR** (1st digit): **ASK the user**, only for milestones or breaking changes

3. Compute the new version:
   - Bumping a digit resets all digits to its right to 0
   - Example: `0.19.1.0` + PATCH -> `0.19.2.0`

4. Write the new version to the `VERSION` file.

---

## Step 5: CHANGELOG (auto-generate)

1. Read `CHANGELOG.md` header to know the format.

2. Auto-generate the entry from **ALL commits on the branch** (not just recent ones):
   - Use `git log <base>..HEAD --oneline` to see every commit being shipped
   - Use `git diff <base>...HEAD` to see the full diff against the base branch
   - The CHANGELOG entry must be comprehensive of ALL changes going into the PR
   - If existing CHANGELOG entries on the branch already cover some commits, replace them with one unified entry for the new version
   - Categorize changes into applicable sections:
     - `### Added` -- new features
     - `### Changed` -- changes to existing functionality
     - `### Fixed` -- bug fixes
     - `### Removed` -- removed features
   - Write concise, descriptive bullet points
   - Insert after the file header (line 5), dated today
   - Format: `## [X.Y.Z.W] - YYYY-MM-DD`

**Do NOT ask the user to describe changes.** Infer from the diff and commit history.

---

## Step 5.5: TODOS.md (auto-update)

Cross-reference the project's TODOS.md against the changes being shipped. Mark completed items automatically; prompt only if the file is missing or disorganized.

**1. Check if TODOS.md exists** in the repository root.

**If TODOS.md does not exist:** Use AskUserQuestion:
- Message: "It's recommended to maintain a TODOS.md organized by skill/component, then priority (P0 at top through P4, then Completed at bottom). Would you like to create one?"
- Options: A) Create it now, B) Skip for now
- If A: Create `TODOS.md` with a skeleton (# TODOS heading + ## Completed section). Continue to step 3.
- If B: Skip the rest of Step 5.5. Continue to Step 6.

**2. Check structure and organization:**

Read TODOS.md and verify it follows the recommended structure:
- Items grouped under `## <Skill/Component>` headings
- Each item has `**Priority:**` field with P0-P4 value
- A `## Completed` section at the bottom

**If disorganized** (missing priority fields, no component groupings, no Completed section): Use AskUserQuestion:
- Message: "TODOS.md doesn't follow the recommended structure (skill/component groupings, P0-P4 priority, Completed section). Would you like to reorganize it?"
- Options: A) Reorganize now (recommended), B) Leave as-is
- If A: Reorganize in-place. Preserve all content, only restructure, never delete items.
- If B: Continue to step 3 without restructuring.

**3. Detect completed TODOs:**

This step is fully automatic, no user interaction.

Use the diff and commit history already gathered in earlier steps:
- `git diff <base>...HEAD` (full diff against the base branch)
- `git log <base>..HEAD --oneline` (all commits being shipped)

For each TODO item, check if the changes in this PR complete it by:
- Matching commit messages against the TODO title and description
- Checking if files referenced in the TODO appear in the diff
- Checking if the TODO's described work matches the functional changes

**Be conservative:** Only mark a TODO as completed if there is clear evidence in the diff. If uncertain, leave it alone.

**4. Move completed items** to the `## Completed` section at the bottom. Append: `**Completed:** vX.Y.Z (YYYY-MM-DD)`

**5. Output summary:**
- `TODOS.md: N items marked complete (item1, item2, ...). M items remaining.`
- Or: `TODOS.md: No completed items detected. M items remaining.`
- Or: `TODOS.md: Created.` / `TODOS.md: Reorganized.`

**6. Defensive:** If TODOS.md cannot be written (permission error, disk full), warn the user and continue. Never stop the ship workflow for a TODOS failure.

Save this summary for the PR body in Step 8.

---

## Step 6: Commit (bisectable chunks)

**Goal:** Create small, logical commits that work well with `git bisect` and help LLMs understand what changed.

1. Analyze the diff and group changes into logical commits. Each commit should represent **one coherent change**, not one file, but one logical unit.

2. **Commit ordering** (earlier commits first):
   - **Infrastructure:** migrations, config changes, route additions
   - **Models & services:** new models, services, concerns (with their tests)
   - **Controllers & views:** controllers, views, JS/React components (with their tests)
   - **VERSION + CHANGELOG + TODOS.md:** always in the final commit

3. **Rules for splitting:**
   - A model and its test file go in the same commit
   - A service and its test file go in the same commit
   - A controller, its views, and its test go in the same commit
   - Migrations are their own commit (or grouped with the model they support)
   - Config/route changes can group with the feature they enable
   - If the total diff is small (< 50 lines across < 4 files), a single commit is fine

4. **Each commit must be independently valid**, no broken imports, no references to code that doesn't exist yet. Order commits so dependencies come first.

5. Compose each commit message:
   - First line: `<type>: <summary>` (type = feat/fix/chore/refactor/docs)
   - Body: brief description of what this commit contains
   - Only the **final commit** (VERSION + CHANGELOG) gets the version tag and co-author trailer:

```bash
git commit -m "$(cat <<'EOF'
chore: bump version and changelog (vX.Y.Z.W)

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

---

## Step 6.5: Verification Gate

**IRON LAW: NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.**

Before pushing, re-verify if code changed during Steps 4-6:

1. **Test verification:** If ANY code changed after Step 3's test run (fixes from review findings; CHANGELOG edits don't count), re-run the test suite. Paste fresh output. Stale output from Step 3 is NOT acceptable.

2. **Build verification:** If the project has a build step, run it. Paste output.

3. **Rationalization prevention:**
   - "Should work now" -> RUN IT.
   - "I'm confident" -> Confidence is not evidence.
   - "I already tested earlier" -> Code changed since then. Test again.
   - "It's a trivial change" -> Trivial changes break production.

**If tests fail here:** STOP. Do not push. Fix the issue and return to Step 3.

Claiming work is complete without verification is dishonesty, not efficiency.

---

## Step 7: Push

Push to the remote with upstream tracking:

```bash
git push -u origin <branch-name>
```

---

## Step 8: Create PR

Create a pull request using `gh`:

```bash
gh pr create --base <base> --title "<type>: <summary>" --body "$(cat <<'EOF'
## Summary
<bullet points from CHANGELOG>

## Test Coverage
<coverage diagram from Step 3.4, or "All new code paths have test coverage.">
<If Step 3.4 ran: "Tests: {before} -> {after} (+{delta} new)">

## Pre-Landing Review
<findings from Step 3.5 code review, or "No issues found.">

## Eval Results
<If evals ran: suite names, pass/fail counts. If skipped: "No prompt-related files changed, evals skipped.">

## TODOS
<If items marked complete: bullet list of completed items with version>
<If no items completed: "No TODO items completed in this PR.">
<If TODOS.md created or reorganized: note that>
<If TODOS.md doesn't exist and user skipped: omit this section>

## Test plan
- [x] All tests pass

Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

**Output the PR URL**, then proceed to Step 8.5.

---

## Step 8.5: Auto-invoke /document-release

After the PR is created, automatically sync project documentation. Read the
`document-release/SKILL.md` skill file (adjacent to this skill's directory) and
execute its full workflow:

1. Follow its instructions: it reads all .md files in the project, cross-references
   the diff, and updates anything that drifted (README, ARCHITECTURE, CONTRIBUTING,
   CLAUDE.md, TODOS, etc.)
2. If any docs were updated, commit the changes and push to the same branch.
3. If no docs needed updating, say "Documentation is current, no updates needed."

This step is automatic. Do not ask the user for confirmation. The goal is zero-friction
doc updates. The user runs `/ship` and documentation stays current without a separate command.

---

## Important Rules

- **Never skip tests.** If tests fail, stop.
- **Never skip the pre-landing review.** If your review checklist is unreadable, stop.
- **Never force push.** Use regular `git push` only.
- **Never ask for confirmation** except for MINOR/MAJOR version bumps and pre-landing review ASK items (batched into at most one AskUserQuestion).
- **Always use the 4-digit version format** from the VERSION file.
- **Date format in CHANGELOG:** `YYYY-MM-DD`
- **Split commits for bisectability**, each commit = one logical change.
- **TODOS.md completion detection must be conservative.** Only mark items as completed when the diff clearly shows the work is done.
- **Never push without fresh verification evidence.** If code changed after Step 3 tests, re-run before pushing.
- **Step 3.4 generates coverage tests.** They must pass before committing. Never commit failing tests.
- **The goal is: user says `/ship`, next thing they see is the review + PR URL + auto-synced docs.**
