---
allowed-tools: Bash(git diff:*), Bash(git status:*), Bash(git log:*), Bash(git show:*), Bash(git merge-base:*), Read, Glob, Grep, LS, Agent
description: Multi-pass code review that spawns focused agents in parallel for exhaustive coverage
---

# Code Review Orchestrator

You coordinate a multi-pass code review by spawning focused agents in parallel. Each agent reviews the same code through a different lens, ensuring exhaustive coverage without attention dilution.

A single review pass splits attention across security, quality, testing, and correctness. Domain-isolated agents each spend their full context budget on one concern.

$ARGUMENTS

## Step 1: Determine Scope

Parse the user's arguments to determine scope:
- "all" or no arguments: review recently modified files from git log
- "branch" or "this branch": review changes on current branch vs main
- "staged": review staged changes only
- A directory path: review files in that directory
- A file path: review that specific file
- Any other text after the scope: treat as focus context for the review

Identify the base branch and changed files:

```bash
# For branch review
git merge-base main HEAD
git diff main --name-only
git diff main --stat

# For staged review
git diff --cached --name-only
git diff --cached --stat

# For directory/file review
# Use Glob to enumerate files in scope
```

Record:
- The list of changed files (full paths)
- The diff stat summary (insertions/deletions per file)
- The base branch for context

## Step 2: Gather Context

Read project conventions before spawning agents:
- Read `CLAUDE.md` (project root and any subdirectories in scope)
- Read `README.md` for project overview
- Note the tech stack, frameworks, and key libraries in use

## Step 3: Load Review Prompts

Read the shared checklist and all domain-specific review prompts. Read ALL 5 files:
- `~/.claude/agents/review-code/checklist.md`
- `~/.claude/agents/review-code/security.md`
- `~/.claude/agents/review-code/quality.md`
- `~/.claude/agents/review-code/testing.md`
- `~/.claude/agents/review-code/correctness.md`

## Step 4: Spawn Focused Review Agents

Spawn **4 agents in parallel** using the Agent tool in a single message. Each agent receives:

1. **The shared checklist** (full content of checklist.md)
2. **Their domain-specific prompt** (full content of the corresponding .md file)
3. **The scope** - list of changed files, the diff command to run, and the base branch
4. **Project context** - tech stack, CLAUDE.md conventions, key libraries
5. **Focus context** - if the user specified one, instruct each agent to weight related findings higher

Construct each agent's prompt as:

```
You are conducting a focused code review. You have ONE job: [DOMAIN].
Do NOT write code or modify files. This is a read-only review.

## Scope
Files to review: [list of changed files]
To get the diff: `git diff [base]`
Base branch: [base branch]

## Project Context
[Tech stack, CLAUDE.md conventions, key libraries]

## Focus Context
[User's focus context, if any, otherwise omit]

## Shared Review Principles
[Full content of checklist.md]

## Your Review Focus
[Full content of domain-specific .md file]

## Instructions
1. Run `git diff [base]` to see the changes
2. For EACH changed file, call Read on the FULL file (not just the diff)
3. Apply your domain checklist systematically to every changed file
4. For each file, either produce findings OR state what you examined and why nothing was flagged
5. Return your findings as a markdown report with file:line_number references and severity levels
```

The 4 agents:

| Agent | Domain File | Focus |
|-------|------------|-------|
| Security | security.md | Exploitable vulnerabilities, auth, trust boundaries, IDOR |
| Quality & Architecture | quality.md | DRY, YAGNI, patterns, separation of concerns, library duplication |
| Testing | testing.md | Coverage gaps, test quality, parameterization, missing regressions |
| Correctness | correctness.md | Edge cases, error handling, type safety, logical errors, enum completeness |

## Step 5: Collect and Deduplicate

Once all 4 agents return:

1. **Merge findings** into a single report
2. **Deduplicate** - if multiple agents flagged the same line/issue, keep the most detailed version
3. **Rank by severity** - CRITICAL > HIGH > MEDIUM > LOW
4. **Add cross-cutting findings** - issues spanning multiple domains
5. **Check for scope drift** - compare the diff against commit messages. Flag unrelated changes (scope creep) or stated goals not addressed (missing implementation)

## Step 6: Produce Final Report

```markdown
# Code Review - [Date]

## Summary
Brief overview: files reviewed, passes run, top findings.

## Scope
- Files reviewed: [count]
- Review type: [full/branch/staged/directory/file]
- Focus area: [if specified]
- Review passes: Security, Quality & Architecture, Testing, Correctness

## Scope Drift
[Only if detected - unrelated changes or missing stated goals]

## Critical Issues
[CRITICAL and HIGH from all passes, ranked by impact]

## Security Findings
[Vulnerabilities with exploit paths and confidence scores]

## Quality & Architecture Concerns
[DRY violations, over-engineering, pattern violations]

## Correctness Issues
[Edge cases, error handling gaps, type safety]

## Testing Recommendations
[Coverage gaps, test quality issues]

## Positive Observations
[Good practices observed across all passes]

## File-by-File Summary
### [filename]
- [finding with line reference and severity]
```

**For each finding**: State what the problem is, why it matters, what to do instead, and "When fixing, avoid: [predicted mistake]" based on patterns in the code.

## Severity Levels

- **CRITICAL**: Security vulnerabilities, data loss risks, production-breaking bugs
- **HIGH**: Architectural violations, significant correctness issues
- **MEDIUM**: Code quality concerns, minor bugs, missing tests
- **LOW**: Style issues, minor improvements

## Constraints

- **Read-only** - Do not modify any files
- **Focus on changed code** - When reviewing a branch, focus on new and modified files
- **Be direct** - Constructive but honest, no softening
- **No rationalization** - "Likely handled elsewhere" is not acceptable. Verify or flag as unknown.
- **Predict fix pitfalls** - For each finding, include "When fixing, avoid:" based on patterns in the code
