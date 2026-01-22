# Agent Stuff

A collection of workflows and rules for AI coding agents, primarily designed for use with **Antigravity** (Google's AI coding assistant). This repository contains reusable patterns, best practices, and automation workflows for Python and TypeScript projects.

## 📁 Repository Structure

```
agent-stuff/
├── workflows/          # Executable workflow commands
│   ├── python/        # Python-specific workflows
│   └── typescript/    # TypeScript/JavaScript workflows
└── rules/             # Agent behavior rules
    ├── python/        # Python coding standards
    └── typescript/    # TypeScript coding standards
```

---

## 🔄 Workflows

Workflows are step-by-step instructions that guide Antigravity through complex tasks. They're stored as markdown files with YAML frontmatter and can be invoked using slash commands.

### Python Workflows

Located in [`workflows/python/`](workflows/python/):

* [`/code-quality-sweep`](workflows/python/code-quality-sweep.md) - Comprehensive code quality audit and improvement
* [`/optimize-python`](workflows/python/optimize-python.md) - Deep code audit focusing on performance, security, and maintainability
* [`/setup-fastapi-backend`](workflows/python/setup-fastapi-backend.md) - Initialize a FastAPI backend with best practices
* [`/setup-python-venv`](workflows/python/setup-python-venv.md) - Set up Python virtual environment

### TypeScript Workflows

Located in [`workflows/typescript/`](workflows/typescript/):

* [`/bug-fix`](workflows/typescript/bug-fix.md) - Systematic bug fixing workflow
* [`/code-review`](workflows/typescript/code-review.md) - Structured code review process
* [`/feature-implementation-docs`](workflows/typescript/feature-implementation-docs.md) - End-to-end feature implementation (Draft → Test → Code → Document → Preview)
* [`/setup-backend-cors`](workflows/typescript/setup-backend-cors.md) - Configure CORS for backend APIs
* [`/setup-render-keep-alive`](workflows/typescript/setup-render-keep-alive.md) - Set up keep-alive for Render deployments
* [`/setup-uptimerobot-health-check`](workflows/typescript/setup-uptimerobot-health-check.md) - Configure UptimeRobot health checks

### How to Use Workflows

1. **Invoke with slash commands**: Type `/workflow-name` in Antigravity
2. **Follow the steps**: Workflows guide the agent through structured processes
3. **Auto-run with turbo**: Workflows marked with `// turbo` or `// turbo-all` can auto-execute commands

Example:
```
/optimize-python
```

This will trigger a deep audit of your Python codebase following the structured workflow.

---

## 📋 Rules

Rules define how Antigravity should behave and write code. They're applied automatically based on the project context.

### Python Rules

Located in [`rules/python/`](rules/python/):

* [`complete.md`](rules/python/complete.md) - Comprehensive Python coding standards (type hints, Pydantic, FastAPI, CORS, deployment)
* [`know-how.md`](rules/python/know-how.md) - Python best practices and patterns
* [`short.md`](rules/python/short.md) - Concise Python guidelines

**Key Python Standards:**
- Python 3.10+ with full type hints
- Pydantic for data validation
- Google-style docstrings
- FastAPI + Uvicorn for web services
- Mandatory CORS configuration
- Render deployment ready

### TypeScript Rules

Located in [`rules/typescript/`](rules/typescript/):

* [`complete.md`](rules/typescript/complete.md) - Comprehensive TypeScript coding standards
* [`know-how.md`](rules/typescript/know-how.md) - TypeScript best practices and patterns
* [`short.md`](rules/typescript/short.md) - Concise TypeScript guidelines

### How Rules Work

Rules are automatically applied when working in projects. You can reference them explicitly or let Antigravity use them contextually based on your project's language and framework.

---

## 🎯 Common Use Cases

### Starting a New FastAPI Project
```
/setup-fastapi-backend
```

### Auditing Python Code Quality
```
/optimize-python
```

### Implementing a New Feature (TypeScript)
```
/feature-implementation-docs
```

### Setting Up CORS for Backend
```
/setup-backend-cors
```

### Code Review
```
/code-review
```

---

## 🔧 Customization

These workflows and rules are designed to be **templates**. Feel free to:

1. **Fork and modify** for your specific needs
2. **Add project-specific workflows** in your project's `.agent/workflows/` directory
3. **Override rules** with project-specific rules in `.agent/rules/`

---

## 📝 Notes

- Workflows are designed for **Antigravity** but may work with other AI coding agents
- Rules enforce **production-grade code** with emphasis on maintainability and safety
- All workflows follow **incremental, test-driven approaches**
- CORS configuration is treated as **mandatory** for API projects

---

## 📄 License

See [LICENSE](LICENSE) for details.