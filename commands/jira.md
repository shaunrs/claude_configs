Implement Jira ticket $ARGUMENTS

## Process

1. **Fetch ticket** - Read the Jira issue details
2. **Start work** - Transition ticket to "In Progress"
3. **Explore** - Use the Explore agent to understand all code flows relevant to the ticket
4. **Plan (if `feature` label)** - Enter plan mode, get user approval before proceeding
5. **Implement** - Implement the ticket, run `pnpm lint --fix` and `pnpm type-check`

### Review Loop

5. **Review** - Run `code-review` and `quality-review` agents in parallel on branch changes
6. **Consolidate** - Combine review outputs into a single change list. On conflicts, prefer `quality-review` - especially when it removes code over adding complexity
7. **Apply fixes** - Create TODO list for changes, systematically address each item
8. **Repeat** - If changes were made, return to step 5. Exit loop when reviews pass clean.

### Finalize

9. **Comment** - Add Jira comment:
   - **Summary:** One sentence describing what was implemented
   - **Approach:** One sentence on how it was solved
   - **Files changed:** Bullet list
   - **Code Review Fixes:** Bullet list of `problem → resolution`
10. **Transition** - Move ticket to "Needs Review"
11. **Commit** - `git add -A` and commit using conventional commits: `<type>(<scope>): <summary>`
12. **Summarize** - Report to user: implementation summary, review findings, and fixes applied

## Style

- Engineering mindset, no hyperbole
- Short, factual statements
- Focus on the "why" not just the "what"
