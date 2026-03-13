# Rule Writing Guidelines

Rules in `.claude/rules/` are loaded into the context window at session start. How you write them affects how reliably Claude follows them.

## Structure

- **Hierarchical bullet points with natural language** - Use bullets as the primary format, with **inline bold** for key terms. Each bullet should be a complete thought, not a telegraphic fragment.
- **Nested bullets for sub-rules** - Group related details under their parent rule.
- **No tables** - Bullet points with inline bold are more scannable.
- **No horizontal rules (`---`)** - Section headers provide sufficient separation.

## Writing Style

- **Complete sentences over fragments** - "Always adhere to DRY principles and avoid code duplication" not "DRY - no duplication"
- **Specificity over vagueness** - Write instructions concrete enough to verify. If a rule can't be objectively checked, it's too vague.
- **Single mention** - Define each concept once with its full meaning, then reference by name only.
- **Preserve the "why"** - Include explanatory context that clarifies the reason behind a rule.

<example>
<!-- BAD: Vague, unverifiable -->
- Format code properly
- Test your changes

<!-- GOOD: Concrete, verifiable -->
- Use 2-space indentation
- Run `pnpm test` before committing
</example>

## Code Examples

- **Minimal examples only** - Keep examples that demonstrate complex or non-obvious patterns. Remove examples for simple rules.
- **Use `<example>` tags for workflows** - When demonstrating multi-step interactions.

<example>
<!-- REMOVE: Obvious rule needs no example -->
- Use descriptive variable names

<!-- KEEP: Pattern is non-obvious -->
- **Blocking providers** - Always render children (hidden):
  ```tsx
  <div style={{ display: ready ? "contents" : "none" }}>{children}</div>
  ```
</example>

## File Organization

- **Target: under 200 lines per file** - If a file exceeds this, consider splitting into multiple focused files.
- **Logical grouping** - Each file covers one coherent topic. A new rule should have an obvious home.
- **Adapt categories to need** - Combine small sections; split large ones.

## Maintaining Rules

- **Never lose a rule** - If a rule exists, there was a reason. Restructure and clarify, don't delete.
- **Check for contradictions** - When rules conflict, Claude may pick arbitrarily. Flag contradictions for resolution.
- **Learn from mistakes** - When a mistake is made and resolved, add a rule to prevent recurrence.
