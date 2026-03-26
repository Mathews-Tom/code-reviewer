# code-reviewer

Multi-phase code quality reviewer that systematically analyzes naming conventions, cyclomatic complexity, error handling, DRY violations, security surface, and test coverage gaps. Produces severity-ranked findings with file:line precision and actionable fix recommendations.

[![gitagent registry](https://img.shields.io/badge/gitagent-registry-blue)](https://registry.gitagent.sh)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Run

```bash
npx @open-gitagent/gitagent run -r https://github.com/Mathews-Tom/code-reviewer
```

---

## What It Can Do

- **Naming Analysis** — checks variable, function, file, constant, and type naming against language conventions and project-specific config
- **Complexity Detection** — flags functions exceeding cyclomatic complexity thresholds, deep nesting, excessive parameters, and long function bodies
- **Error Handling Audit** — identifies bare except/catch blocks, silent failures, missing error handling on I/O operations, and generic error messages
- **DRY Violation Detection** — finds copy-paste code blocks, repeated patterns, magic numbers, and configuration duplication with extraction recommendations
- **Security Surface Scan** — lightweight check for hardcoded secrets, SQL concatenation, unvalidated input, and path traversal
- **Test Coverage Gaps** — identifies untested public API, missing edge case coverage, error path testing gaps, and over-mocking

---

## Structure

```
code-reviewer/
├── agent.yaml
├── SOUL.md
├── RULES.md
├── README.md
├── assets/
│   ├── icon.png
│   └── banner.png
└── knowledge/
    ├── complexity-metrics.md
    ├── naming-conventions.md
    ├── error-handling-patterns.md
    └── dry-violations.md
```

---

## Built with

Built for the [gitagent](https://gitagent.sh) ecosystem.
