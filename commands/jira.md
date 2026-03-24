---
allowed-tools: Bash, Read, Write, Edit, Glob, Grep, LS, Agent, AskUserQuestion, TaskCreate, TaskUpdate, TaskList, WebFetch, WebSearch, mcp__atlassian__getJiraIssue, mcp__atlassian__addCommentToJiraIssue, mcp__atlassian__transitionJiraIssue, mcp__atlassian__getTransitionsForJiraIssue, mcp__deepwiki__ask_question, mcp__deepwiki__read_wiki_contents, mcp__deepwiki__read_wiki_structure
description: Implement a Jira ticket end-to-end with research, design review, TDD, and multi-pass code review
---

Implement Jira ticket $ARGUMENTS

## Phase 1: Understand

1. **Fetch ticket** - Read the Jira issue details (summary, description, acceptance criteria, labels)
2. **Start work** - Transition ticket to "In Progress"
3. **Create branch** - `git checkout -b <type>/<ticket-id>-<short-description>` from main

## Phase 2: Research

Spawn **3 agents in parallel** to build a complete picture before designing anything:

### Agent 1: Codebase Explorer
```
Explore the codebase to understand all code flows relevant to this ticket.

Ticket: [summary and description]

Instructions:
1. Find all files, functions, and modules related to this feature area
2. Trace the existing data flows end-to-end (API → service → database, or UI → state → API)
3. Identify existing patterns for similar features in the codebase
4. Note any shared utilities, helpers, or base classes that should be reused
5. Read CLAUDE.md for project conventions
6. Return: a map of relevant files, the existing patterns, and any constraints discovered
```

### Agent 2: Documentation & Best Practices
```
Research current best practices for implementing this feature.

Ticket: [summary and description]
Tech stack: [from README/CLAUDE.md/package.json]

Instructions:
1. Use DeepWiki to check documentation for the primary framework and key libraries
2. Identify the idiomatic approach for this type of feature in this stack
3. Check for recent API changes or deprecations that affect the approach
4. Find any official guides or recommended patterns for this use case
5. Return: recommended approach with specific API references, any gotchas or deprecations to avoid
```

### Agent 3: Library Analysis
```
Identify existing libraries that solve the core functionality or sub-functions of this feature.

Ticket: [summary and description]
Current dependencies: [from package.json/Cargo.toml/etc.]

Instructions:
1. Read the project's dependency file to understand what's already available
2. For each significant piece of functionality in the ticket, check if an existing dependency already handles it
3. Search for well-maintained libraries that could replace custom implementation
4. For each candidate library, verify: actively maintained, compatible version, doesn't duplicate existing deps
5. Return: list of libraries to use (existing deps first, new deps second), what each covers, and what remains as custom code
```

## Phase 3: Design

Synthesize the research into a design document. This is the orchestrator's job - it requires all three research outputs:

1. **Wait for all 3 agents** to return
2. **Write PRD** to `.docs/PRD-[date]-[ticket-id].md` containing:
   - **Problem** - one sentence
   - **Solution** - the proposed approach, informed by all three research agents
   - **User Stories** - from the ticket's acceptance criteria
   - **Architecture** - Mermaid diagram showing the data/control flow
   - **Public Interface** - the function signatures, API endpoints, or component props that consumers will use. This is what the test architect receives. **For each function, identify the exact call site and verify the parameter types match what the caller has access to.** If you cannot name a concrete call site, the function is speculative (YAGNI).
   - **Files to create/modify** - with a one-line description of changes per file
   - **Libraries** - what existing/new dependencies handle what
   - **Test Strategy** - what tests will be written first, what they verify, and the expected test boundaries
   - **Edge Cases** - identified from the research
3. **Present summary** to user via AskUserQuestion - show the key decisions and wait for approval

## Phase 4: Design Review

After user approves the direction, spawn the **design-review** agent:

```
Review this design for simplicity, elegance, best practices, and testability.

PRD: [path to PRD file]
Ticket: [summary]
Project context: [tech stack, CLAUDE.md conventions]

Research findings:
- Codebase: [summary from Agent 1]
- Best practices: [summary from Agent 2]
- Libraries: [summary from Agent 3]

CRITICAL CHECK - For every function in the Public Interface section:
1. Identify the exact call site (which file and function calls this)
2. Read the call site and verify the parameter types match what the caller has access to
3. If the caller receives a different type (e.g., ThreadMessage[] vs WrappedMessage[]), the interface is wrong
4. If no call site exists, flag as YAGNI - the function has no consumer
```

**If verdict is RETHINK:** Update the PRD with the alternative design, present to user, re-run design review.
**If verdict is REVISE:** Apply the recommended changes to the PRD and proceed.
**If verdict is APPROVE:** Proceed.

## Phase 5: Write Tests (Red)

Spawn the **test-architect** agent. It receives ONLY the behavioral specification - not the implementation plan:

```
Write tests for this feature BEFORE implementation.

PRD: [path to PRD file]
Project context: [tech stack, CLAUDE.md conventions, test runner, test file conventions]
Public interface: [function signatures, API endpoints, component props from the PRD]
User stories: [acceptance criteria from the PRD]
Edge cases: [from the PRD]

You must NOT read the Architecture or Files to create/modify sections of the PRD.
You only see WHAT the feature does, not HOW it will be implemented.
```

When the test-architect returns:
1. **Review the test manifest** - verify coverage of all user stories and edge cases
2. **Run the tests** - confirm ALL fail (Red state). Record the output.
3. If any test passes, investigate: either the feature already exists or the test is vacuous

## Phase 6: Implement (Green)

Spawn the **implementer** agent. It receives the full PRD and the test files:

```
Implement this feature. Tests are already written and failing.

PRD: [path to PRD file]
Test manifest: [from test-architect output - which files, what they test, what's mocked]
Test files: [list of test file paths]
Project context: [tech stack, CLAUDE.md conventions, lint/type-check commands]
```

When the implementer returns:
1. **Run the full test suite** - confirm all new tests pass (Green state) AND no regressions
2. **Run lint and type-check** - confirm clean
3. **Verify PRD completeness** - walk through every user story and every file in the "Files to create/modify" table. Confirm each is implemented. If anything is missing, implement it yourself before proceeding.
4. **Record the TDD verification** in the PRD:

```markdown
## TDD Verification
- Tests written: [count] ([list test files])
- Red confirmation: [timestamp] - all [count] tests failed as expected
- Green confirmation: [timestamp] - all [count] tests pass
- Refactor: [description of cleanup, or "none needed"]
```

4. If the implementer reported test discrepancies, review them and decide:
   - Fix the test (if the test was wrong)
   - Fix the implementation (if the implementation was wrong)
   - Ask the user (if the PRD is ambiguous)

## Phase 7: Code Review

Read all 5 review prompt templates from `~/.claude/agents/review-code/`:
- `checklist.md`, `security.md`, `quality.md`, `testing.md`, `correctness.md`

Spawn **4 focused review agents in parallel** (security, quality, testing, correctness), following the same pattern as the `/review` command.

After collecting and deduplicating findings:
1. Apply fixes, creating a TODO list and addressing each item
2. **Re-run tests** after fixes to confirm they still pass
3. **Repeat** review until clean (no CRITICAL or HIGH findings)

## Phase 8: Finalize

1. **Update PRD** - rename using `mv .docs/PRD-[date]-{,DONE-}[ticket-id].md`
2. **Comment on Jira** -
   - **Summary:** One sentence describing what was implemented
   - **Approach:** One sentence on the solution
   - **Files changed:** Bullet list
   - **Design Review:** Verdict and key adjustments made
   - **TDD:** [count] tests written, Red/Green confirmed
   - **Code Review Fixes:** Bullet list of `problem → resolution`
3. **Transition** - Move ticket to "Needs Review"
4. **Commit** - stage relevant files and commit using conventional commits: `<type>(<ticket-id>): <summary>`. Do NOT commit the `.docs/` PRD files - those are local planning artifacts, not source code.
5. **Summarize** - Report to user: implementation summary, design decisions, TDD results, review findings, and fixes applied

## Agent Summary

| Phase | Agent | Focus | Isolation Benefit |
|-------|-------|-------|-------------------|
| 2 | Codebase Explorer | Map relevant code flows | Full attention on code archaeology |
| 2 | Documentation Researcher | Current best practices | Can consult docs without distraction |
| 2 | Library Analyst | Dependency opportunities | Focused evaluation of alternatives |
| 4 | Design Review | Critique the plan | Fresh eyes, skeptical default |
| 5 | Test Architect | Write failing tests | Cannot see implementation - enforces behavioral tests |
| 6 | Implementer | Make tests pass | Focused on code, tests as specification |
| 7 | Security Review | Exploitable vulnerabilities | Full context budget on security |
| 7 | Quality Review | DRY, YAGNI, patterns, simplification | Full context budget on quality |
| 7 | Testing Review | Coverage gaps, test quality | Full context budget on test analysis |
| 7 | Correctness Review | Edge cases, type safety | Full context budget on correctness |

## Principles

- **Research before designing, design before coding** - the cheapest time to change direction is before code exists
- **Tests describe behavior, not implementation** - if a test needs to know HOW the code works, the interface is wrong
- **Isolation prevents contamination** - the test architect can't be biased by implementation knowledge if it never sees it
- **Simpler is better** - when in doubt, remove. Prefer deletion over addition.
- **Verify, don't assume** - check docs, check existing code, check library APIs. Pre-training knowledge is stale.
- **Engineering mindset** - short, factual statements. No hyperbole. Focus on the "why."
