# CLAUDE.md — AI Assistant Guide

This file provides context, conventions, and instructions for AI assistants (Claude and others) working in this repository.

---

## Repository Overview

**Repository:** `AyoolaDare/automation-`
**Current state:** Freshly initialized — no source files have been committed yet.
**Working branch convention:** `claude/<task-slug>-<session-id>`

This is an automation-focused repository. As code is added, update this file to reflect the actual project structure, stack, and conventions.

---

## Branch & Git Conventions

- **Feature branches:** `claude/<task-slug>-<session-id>` (AI-generated) or `feat/<short-description>` (human-authored)
- **Bug fix branches:** `fix/<short-description>`
- **Always develop** on the designated branch — never commit directly to `main` or `master`
- **Commit messages** should follow [Conventional Commits](https://www.conventionalcommits.org/):
  - `feat: add user authentication module`
  - `fix: correct off-by-one error in pagination`
  - `chore: update dependencies`
  - `docs: update CLAUDE.md with project structure`
- **Push** with `-u origin <branch>` to set upstream tracking:
  ```bash
  git push -u origin <branch-name>
  ```
- Retry failed pushes (network errors only) with exponential backoff: 2s → 4s → 8s → 16s

---

## Development Workflow

Because the repository is currently empty, the following steps apply when initializing the project:

1. **Define the stack** — choose language, framework, and tooling before writing code.
2. **Add dependency manifest** — `package.json`, `requirements.txt`, `go.mod`, etc.
3. **Add `.gitignore`** appropriate for the chosen stack.
4. **Add configuration files** — linter, formatter, TypeScript config, etc.
5. **Create `src/` (or equivalent) directory** for source code.
6. **Set up tests** — add a test runner and at minimum one smoke test before merging.
7. **Update this file** (`CLAUDE.md`) with the real structure, commands, and conventions.

---

## Common Commands

> Replace the placeholder commands below once the actual stack is chosen.

```bash
# Install dependencies
npm install          # Node.js / JavaScript / TypeScript
pip install -r requirements.txt  # Python
go mod download      # Go

# Run the project in development mode
npm run dev

# Run tests
npm test
pytest
go test ./...

# Lint
npm run lint
flake8 .
golangci-lint run

# Format code
npm run format
black .
gofmt -w .

# Build / compile
npm run build
go build ./...

# Type-check (TypeScript)
npx tsc --noEmit
```

---

## File & Directory Structure

The structure will evolve as code is added. A typical layout:

```
automation-/
├── .github/
│   └── workflows/      # CI/CD pipeline definitions
├── src/                # Primary source code
├── tests/              # Test suites
├── scripts/            # Utility / automation scripts
├── docs/               # Additional documentation
├── .gitignore
├── CLAUDE.md           # This file
└── README.md           # Human-facing project overview
```

Update this section to reflect the actual layout once the project structure is established.

---

## AI Assistant Instructions

### General

- **Read before editing.** Always read a file before modifying it. Never guess at existing content.
- **Minimal changes.** Only change what is necessary to complete the task. Avoid refactoring unrelated code.
- **No unnecessary files.** Do not create files (including markdown docs) unless explicitly required.
- **No emojis** unless the user explicitly asks for them.
- **No time estimates.** Do not predict how long tasks will take.

### Security

- Never introduce SQL injection, XSS, command injection, or other OWASP Top 10 vulnerabilities.
- Validate input only at system boundaries (user input, external APIs) — trust internal framework guarantees.
- Never commit secrets, API keys, or credentials. Use environment variables.

### Testing

- Write or update tests whenever adding or modifying logic.
- All tests must pass before marking a task complete.
- Do not mark a task done if there are failing tests or unresolved errors.

### Commits

- Commit **only** to the branch specified in the task.
- Never amend previous commits unless explicitly asked.
- Stage specific files rather than `git add -A` to avoid accidentally committing secrets or binaries.
- Pass the commit message via a heredoc to preserve formatting:
  ```bash
  git commit -m "$(cat <<'EOF'
  feat: add initial project scaffold

  Sets up directory structure, package.json, and base configuration.
  EOF
  )"
  ```

---

## Environment Variables

Document required environment variables here as they are added.

| Variable | Required | Description |
|----------|----------|-------------|
| *(none yet)* | — | — |

Create a `.env.example` file in the repository root when environment variables are introduced.

---

## CI/CD

No CI/CD pipeline has been configured yet. When added, document:

- The CI provider (GitHub Actions, CircleCI, etc.)
- What triggers a build (push, pull request, schedule)
- Required secrets / environment variables in the CI environment
- Steps run in the pipeline (lint → test → build → deploy)

---

## Updating This File

Keep `CLAUDE.md` current. Update it whenever:

- The technology stack or major dependencies change
- New development commands are introduced
- Significant architectural decisions are made
- Environment variables are added or removed
- CI/CD configuration changes
