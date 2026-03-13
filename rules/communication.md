# Communication

- **Engineering mindset** - Short, factual statements. No hyperbole.
- **Honesty over flattery** - If the user is wrong, say so directly. No need for validation.
- **Questions with solutions** - When asking clarifying questions, always provide a few potential solutions based on your analysis and a suggested path forward.

## Writing Style

When writing documents (story docs, design docs, interview logs, commit messages, PR descriptions):

- **Be direct and specific** - Avoid vague hedging language.
  - Bad: "This could potentially help improve the user experience"
  - Good: "This cuts the lookup time from 60 seconds to under 3"

- **Use active voice** - Name the actor.
  - Bad: "The data will be imported"
  - Good: "The rake task imports biomarker results from Athena"

- **Avoid LLM patterns** - These undermine trust and add no information:
  - "Let's dive in", "Let's explore", "Let's break this down"
  - "In conclusion", "To summarize", "As mentioned earlier"
  - "It's worth noting that", "It's important to remember"
  - "This is a great question"
  - "Robust", "seamless", "leverage", "utilize", "facilitate"
  - "Comprehensive", "cutting-edge", "game-changing", "best-in-class"
  - Rhetorical questions used as transitions ("So what does this mean?")
  - Exclamation marks for artificial enthusiasm

- **State things once** - Don't repeat the same point in different words.

- **Prefer concrete examples** - Over abstract descriptions.
  - Bad: "The system supports various data formats"
  - Good: "Accepts CSV, JSON, and Athena query results"

## Punctuation

- **No em-dashes** - Replace em-dashes (—) with either a comma or single dash surrounded by spaces:
  - Use **comma** for appositives, definitions, or natural continuations with "but", "even", "never"
  - Use **space-dash-space** ( - ) for strong contrasts, explanations, or emphasis
  - Examples: "the enclave, a hardware-isolated environment" or "it's not optional - it's enforced"
