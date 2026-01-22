# Contributing to Agent Stuff

Thank you for your interest in contributing to this repository! This guide will help you add new workflows and rules for AI coding agents.

## 📋 Table of Contents

- [How to Contribute](#how-to-contribute)
- [Contributing Workflows](#contributing-workflows)
- [Contributing Rules](#contributing-rules)
- [Quality Standards](#quality-standards)
- [Submission Guidelines](#submission-guidelines)

---

## How to Contribute

There are several ways to contribute:

1. **Add new workflows** - Share your proven automation patterns
2. **Add new rules** - Contribute coding standards and best practices
3. **Improve existing content** - Enhance clarity, fix errors, or add examples
4. **Report issues** - Help identify problems or suggest improvements
5. **Share feedback** - Let us know what works and what doesn't

---

## Contributing Workflows

Workflows are step-by-step instructions that guide AI agents through complex tasks.

### Workflow File Format

All workflows must follow this format:

```markdown
---
description: Brief one-line description of what this workflow does
---

# Workflow Title

Brief introduction explaining the purpose and when to use this workflow.

---

## Step 1: [Step Name]

Detailed instructions for this step...

1. Sub-step one
2. Sub-step two
3. Sub-step three

---

## Step 2: [Step Name]

Continue with additional steps...

---

## Expected Outcome

What the agent should have accomplished after completing this workflow.
```

### Workflow Naming Conventions

- Use **kebab-case** for filenames: `setup-backend-cors.md`
- The slash command will match the filename: `/setup-backend-cors`
- Be descriptive but concise (2-4 words ideal)

### Workflow Location

Place workflows in the appropriate language directory:

- Python workflows: `workflows/python/`
- TypeScript/JavaScript workflows: `workflows/typescript/`

### Turbo Mode Annotations

You can mark steps for auto-execution:

- `// turbo` - Auto-run this single step
- `// turbo-all` - Auto-run ALL command steps in the workflow

Example:
```markdown
## Step 3: Install Dependencies

// turbo
Run the following command:
```bash
npm install
```
```

### What Makes a Good Workflow?

✅ **Good workflows:**
- Solve a specific, repeatable problem
- Include clear, actionable steps
- Specify expected outcomes
- Handle edge cases and errors
- Are language/framework agnostic when possible

❌ **Avoid:**
- Overly generic workflows ("write code")
- Project-specific hardcoded values
- Workflows that duplicate existing ones
- Steps that require manual intervention without explanation

---

## Contributing Rules

Rules define how AI agents should behave and write code for specific languages or frameworks.

### Rule File Types

We maintain three levels of rules for each language:

1. **`complete.md`** - Comprehensive, detailed standards (200+ lines)
2. **`know-how.md`** - Best practices and patterns (100-200 lines)
3. **`short.md`** - Concise, quick-reference guidelines (50-100 lines)

### Rule File Format

Rules should be organized into clear sections:

```markdown
# [Language] Rules for Antigravity

Brief introduction.

---

## 1. [Category Name]

### Subcategory

- Rule or guideline
- Another rule with example:

  ```[language]
  // Example code
  ```

---

## 2. [Next Category]

Continue with additional categories...
```

### Rule Categories

Common categories to include:

- **General Behavior** - Overall approach and philosophy
- **Language Features** - Version, syntax, type systems
- **Code Style & Quality** - Formatting, naming, documentation
- **Architecture & Patterns** - Design principles, project structure
- **Testing** - Test frameworks, coverage expectations
- **Security** - Common vulnerabilities to avoid
- **Deployment** - Platform-specific requirements
- **Dependencies** - Package management, version constraints

### Rule Location

Place rules in the appropriate language directory:

- Python rules: `rules/python/`
- TypeScript rules: `rules/typescript/`

### What Makes Good Rules?

✅ **Good rules:**
- Are specific and actionable
- Include code examples
- Explain the "why" not just the "what"
- Reference industry standards (PEP 8, ESLint, etc.)
- Balance strictness with pragmatism

❌ **Avoid:**
- Overly opinionated personal preferences
- Rules that conflict with language conventions
- Vague guidelines without examples
- Platform-specific rules in general files

---

## Quality Standards

All contributions should meet these standards:

### Documentation

- ✅ Clear, grammatically correct English
- ✅ Proper markdown formatting
- ✅ Code blocks with language specification
- ✅ Links to external resources when helpful

### Code Examples

- ✅ Syntactically correct
- ✅ Follow the rules being demonstrated
- ✅ Include comments when needed
- ✅ Show both good and bad examples when relevant

### Testing

Before submitting:

1. **Test with Antigravity** - Verify the workflow/rule works as intended
2. **Check formatting** - Ensure markdown renders correctly
3. **Validate links** - Confirm all links work
4. **Review for clarity** - Have someone else read it if possible

---

## Submission Guidelines

### Before You Submit

1. **Check for duplicates** - Search existing workflows/rules
2. **Follow the format** - Use the templates above
3. **Test thoroughly** - Verify it works with AI agents
4. **Update README** - Add your contribution to the appropriate section

### Pull Request Process

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b add-workflow-name`
3. **Add your files** in the correct directory
4. **Update README.md** to include your contribution
5. **Commit with clear message**: `docs: add workflow for X`
6. **Push and create a Pull Request**

### PR Description Template

```markdown
## Type of Contribution
- [ ] New workflow
- [ ] New rule
- [ ] Improvement to existing content
- [ ] Bug fix
- [ ] Documentation

## Description
Brief description of what this adds or changes.

## Testing
How you tested this (e.g., "Tested with Antigravity on 3 different projects")

## Related Issues
Fixes #123 (if applicable)
```

### Commit Message Format

Use conventional commits:

- `feat: add new workflow for database migrations`
- `docs: improve Python rules with async examples`
- `fix: correct typo in TypeScript workflow`
- `refactor: reorganize rule categories`

---

## Code of Conduct

### Our Standards

- **Be respectful** - Value diverse perspectives and experiences
- **Be constructive** - Provide helpful feedback
- **Be collaborative** - Work together toward better solutions
- **Be patient** - Remember we're all learning

### Unacceptable Behavior

- Harassment, discrimination, or personal attacks
- Trolling or inflammatory comments
- Publishing others' private information
- Other conduct inappropriate in a professional setting

---

## Questions?

- **Open an issue** for questions about contributing
- **Start a discussion** for ideas or suggestions
- **Review existing content** for examples and inspiration

---

## License

By contributing, you agree that your contributions will be licensed under the same license as this project (see [LICENSE](LICENSE)).

---

Thank you for helping make AI coding agents more effective! 🚀
