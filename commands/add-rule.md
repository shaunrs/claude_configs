Add a new rule based on $ARGUMENTS

## Process

### 1. Load Guidelines

Read and follow the guidelines in:
- `~/.claude/rules/rule-writing.md`
- `~/.claude/rules/communication.md`

### 2. Understand the Rule

Parse the user's input ($ARGUMENTS) or conversation context to identify:
- What behavior should be encouraged or prevented?
- What's the underlying principle?
- Is there a concrete example?

If the input is vague, use AskUserQuestion to clarify.

### 3. Determine the Best Fit File

List files in `~/.claude/rules/` and select where this rule fits most naturally. If uncertain between two, prefer the more specific file. If no file fits, suggest creating a new one.

### 4. Craft the Rule

Write the rule following the loaded guidelines from step 1.

### 5. Present for Approval

Show the user:
1. The selected file and why
2. The crafted rule
3. Where it will be inserted

Use AskUserQuestion to confirm before writing.

### 6. Write and Confirm

Edit the file to add the rule. Report the file modified and any related rules that might need updating.
