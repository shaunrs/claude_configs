# Shared Review Principles

These principles apply to ALL review passes regardless of focus area.

## Mandatory File Reading Protocol

You MUST call `Read` on the full file for every changed file in scope - not just the diff output. The diff tells you what changed; the full file tells you whether it's correct.

Before moving to the next file, either:
1. Produce specific findings with line numbers, OR
2. State what you examined and why nothing was flagged (1-2 sentences minimum)

"No issues found" is valid output - but only after doing the analysis. "Skipped" is never valid.

## Anti-Rationalization Rules

- **Never say "likely handled" or "probably tested"** - verify by reading the code, or flag as unverified
- **Never say "this looks fine"** - either cite evidence it IS fine (specific line, specific guard), or flag it as unverified
- **Every claim must cite a specific line number** - "proper validation exists" is not acceptable; "validated at line 42 via `schema.parse()`" is
- **Absence of evidence is not evidence of absence** - if you cannot confirm a guard exists, report it as missing, not as "probably exists elsewhere"

## Verification Requirements

Before including a finding in your report:
- [ ] Finding verified by reading actual code, not just scanning the diff
- [ ] Claims about "handled elsewhere" confirmed by reading that code
- [ ] Security issues traced through actual data flow (source to sink)
- [ ] Dead code confirmed unused via grep across the codebase
- [ ] Recommendations checked against project conventions (read CLAUDE.md first)

Before claiming no issues in a category:
- [ ] State what files and patterns you examined
- [ ] Explain why nothing was flagged

## Search Before Recommending

Before recommending a fix pattern:
1. **Consult live documentation** - use DeepWiki or fetch official docs to verify the recommended approach is current best practice for the framework/library version in use. Do not rely on pre-training knowledge for API signatures or method availability.
2. **Check for built-in solutions** - newer library versions often solve problems that required workarounds before. Read package.json/Cargo.toml for the version, then check current docs for built-in alternatives.
3. **Verify API signatures exist** - confirm the method, function, or parameter you're recommending actually exists in the version the project uses. Grep the codebase types or fetch docs - never guess.
4. **Match existing patterns** - if the codebase already solves a similar problem, recommend the same approach for consistency unless the existing approach is itself the problem.

A recommendation that uses a deprecated API or nonexistent method is worse than no recommendation.

## Two-Pass Severity Protocol

**Pass 1 (Critical)** - check each category explicitly for every changed file:
- **SQL & data safety** - unparameterized queries, missing transactions, data corruption paths
- **Race conditions** - TOCTOU, concurrent mutations, async ordering assumptions
- **Auth bypass** - missing authorization checks, privilege escalation, IDOR
- **Breaking API changes** - changed contracts, removed fields, altered semantics
- **LLM output trust boundary** - if code processes LLM output, verify it's sanitized before use in queries, templates, or user-facing content
- **Enum & value completeness** - new values handled in all consumers (requires reading outside the diff)

**Pass 2 (Informational)** - issues worth addressing:
- Code quality concerns (DRY violations, complexity)
- Missing test coverage for critical paths
- Completeness gaps (80-90% implementations where 100% is achievable)
- Type safety gaps
- Naming or structural improvements
- Documentation gaps

Complete Pass 1 for ALL files before starting Pass 2. This prevents attention from being spent on style while critical issues remain unexamined.

## Scope Drift Detection

Before reviewing code quality, compare the diff against stated intent:
- Read PR description, commit messages, or TODO files if available
- Flag unrelated changes that don't serve the stated purpose (scope creep)
- Flag stated goals that aren't addressed in the diff (missing implementation)

## Output Format

For each finding, provide:
1. **File and line number** - `filename:line_number`
2. **Severity** - CRITICAL, HIGH, MEDIUM, or LOW
3. **What** - the specific problem
4. **Why it matters** - concrete consequence, not theoretical risk
5. **Fix** - what to do instead, with a code sketch if helpful
6. **When fixing, avoid** - predict the likely incorrect fix based on patterns in the code
7. **Verification** - what you checked to confirm this is real (grep output, line references, data flow trace)
