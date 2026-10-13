# Design Decisions and Notes

## 1. CLAUDE.md Content Decisions
- **Included**: Essential build/test commands (`npm test`), coding conventions (using named exports over default exports), and high-level architectural guidelines. These provide actionable context that Claude Code needs to work effectively on the project.
- **Deliberately Omitted**: Detailed project history, verbose documentation, secrets, and one-off tasks. Omitting these keeps context usage minimal, prevents distraction, and ensures Claude strictly adheres to the core actionable rules.

## 2. Permission Rules and Security Reasoning
- **Deny Rules Added**: 
  - `Read(./.env)`
  - `Bash(git push --force:*)`
- **Risks without Deny Rules**:
  - Without blocking access to `.env`, sensitive credentials, API keys, or database secrets could be read and unintentionally exposed.
  - Without blocking forced pushes, Claude could execute destructive git commands that overwrite shared remote history and erase team members' work.