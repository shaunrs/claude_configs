Implement Jira ticket $ARGUMENTS

## Process

1. **Fetch ticket** - Read the Jira issue details
2. **Start work** - Transition ticket to "In Progress"
3. **Explore** - Use the Explore agent to understand all code flows relevant to the ticket
4. **Plan (if `feature` label)** - Write plan to `.docs/PRD-[date]-[ticket].md`, present summary, use AskUserQuestion for approval before proceeding (do NOT use EnterPlanMode - it clears context)
5. **Implement** - Implement the ticket, run `pnpm lint --fix` and `pnpm type-check`

### Review Loop

6. **Review** - Run `code-review` and `quality-review` agents in parallel on branch changes
7. **Consolidate** - Combine review outputs into a single change list. On conflicts, prefer `quality-review` - especially when it removes code over adding complexity
8. **Apply fixes** - Create TODO list for changes, systematically address each item
9. **Repeat** - If changes were made, return to step 6. Exit loop when reviews pass clean.

### Simplification Check

10. **Simplify** - Run `review-simplification` agent on branch changes. If recommendation is SIMPLIFY, apply the proposed changes and return to step 6.

### Finalize

11. **Comment** - Add Jira comment:
   - **Summary:** One sentence describing what was implemented
   - **Approach:** One sentence on how it was solved
   - **Files changed:** Bullet list
   - **Code Review Fixes:** Bullet list of `problem → resolution`
   - **Simplifications:** Bullet list of structural improvements applied (if any)
12. **Transition** - Move ticket to "Needs Review"
13. **Commit** - `git add -A` and commit using conventional commits: `<type>(<scope>): <summary>`
14. **Summarize** - Report to user: implementation summary, review findings, and fixes applied

## Style

- Engineering mindset, no hyperbole
- Short, factual statements
- Focus on the "why" not just the "what"
