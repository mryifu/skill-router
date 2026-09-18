# Skill Router

An explicit Codex skill that selects and sequences the best installed skills for a task.

`skill-router` helps Codex decide whether a request needs design, planning, implementation, testing, browser automation, document tooling, parallel agents, or external skill discovery. It favors the smallest useful skill chain and explains the route before work begins.

## Install

Install globally with the Skills CLI:

```bash
npx skills add mryifu/skill-router --skill skill-router -g
```

Or clone this repository and copy the skill directory into your Codex skills directory.

## Use

Call it explicitly with `$skill-router`:

```text
$skill-router Build a dashboard with authentication, a database, and end-to-end tests.
```

The router will briefly report a route such as:

```text
ponytail → brainstorming → writing-plans → tdd → verification-before-completion
```

Then it will load and apply the selected skills in dependency order.

## Routing behavior

### Coding tasks

Coding, refactoring, dependency, and architecture work uses `ponytail` as a simplicity gate by default. It checks for existing code, standard-library solutions, native platform features, and unnecessary abstractions before implementation.

Small tasks stay small. A larger project may add:

```text
ponytail
→ brainstorming
→ writing-plans
→ using-git-worktrees
→ subagent-driven-development or dispatching-parallel-agents
→ tdd
→ requesting-code-review
→ verification-before-completion
```

Parallel-agent skills are selected only when subtasks have clear boundaries and do not share mutable state.

### Specialist tasks

- Web UI: `frontend-design`
- Browser automation: `agent-browser`
- Bugs and regressions: `systematic-debugging`
- Test-first implementation: `tdd`
- Documents: `docx` or `documents:documents`
- PDFs: `pdf` or `pdf:pdf`
- Spreadsheets: `xlsx` or `spreadsheets:Spreadsheets`
- Slides: `pptx` or `presentations:Presentations`

The router prefers a narrow specialist over a broad, overlapping skill.

## External skill discovery

When no local skill clearly fits, or when the task is niche or tool-specific, the router uses `find-skills` to search for candidates. It may search adjacent terms across public skill registries, inspect the actual `SKILL.md`, and report fit, source, maintenance signals, and safety concerns.

Discovery does not install anything automatically. A newly discovered third-party skill is installed only after explicit user confirmation.

Example:

```text
$skill-router Find a skill for migrating a legacy GraphQL API to REST.
```

## Design principles

- Explicit invocation only; it does not hijack ordinary requests.
- One primary skill plus only necessary supporting skills.
- Preserve user-selected skills and constraints.
- Search before inventing a workflow when local coverage is weak.
- Never broaden permissions or perform external mutations merely because a skill was selected.
- Verify the result before claiming completion.
