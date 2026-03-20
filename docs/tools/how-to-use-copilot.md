> **TODO:** Expand with more Copilot tips, prompt engineering techniques, and examples of effective vs ineffective prompts.

## Tips for Better Suggestions

* Only open your relevant files while running code suggestions
* Provide a top-level comment at the top of each file describing its purpose
* Set includes and references — specify which dependencies, library names, and versions you want
* Meaningful names matter — descriptive variable and function names lead to better suggestions
* Provide specific and well-scoped function comments
* Provide sample code or test cases to guide the suggestion

## Effective Prompting

- Be specific about the language, framework, and version
- Describe the **input** and **output** of the function you want
- Mention constraints (e.g., "without using external libraries")
- Use comments to guide: `// Using Express.js, create a middleware that...`

## What Copilot is Good At

- Boilerplate code and repetitive patterns
- Completing functions from descriptive comments
- Writing unit tests from existing code
- Converting pseudocode to real code

## What to Watch Out For

- It may generate plausible but incorrect logic — always review
- Security-sensitive code (auth, crypto) should be carefully audited
- May use deprecated APIs — verify against current docs