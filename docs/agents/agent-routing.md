# Agent Routing

This document defines which agent lane should handle which class of work in this repository.

## Primary lanes

### Codex + GPT

Default for:

- repo organization
- study-plan decomposition
- documentation restructuring
- non-security engineering work
- helper scripts and scaffolding
- shared agent-operating docs

### Claude Code + Grok

Default for:

- security auditing
- attack-surface analysis
- vulnerability hypotheses
- exploitability review
- adversarial reasoning
- security finding writeups

## Decision rules

Use Claude as the primary lane when any of the following is true:

- the task may change the repository's security posture
- the task asserts or refutes a vulnerability
- the task evaluates exploitability, impact, or attack chains
- the task reviews untrusted inputs crossing trust boundaries

Use Codex as the primary lane when any of the following is true:

- the task is mainly organizational or editorial
- the task restructures docs or repo layout
- the task builds non-security helper tooling
- the task translates research material into study artifacts

## Shared-task handling

If a task spans both lanes:

1. Claude produces the security analysis artifact first.
2. Codex integrates approved conclusions into shared docs, plans, or implementation work.
3. Shared-policy changes are edited in Codex-owned docs unless the change is specifically Claude-only behavior.

## File ownership map

| Area | Primary lane |
| --- | --- |
| `docs/codex-security/` | Codex |
| `docs/agents/` | Codex |
| `docs/security/` | Claude |
| `reports/security/` | Claude |
| `docs/agents/findings/` | Claude |
| `AGENTS.md` | Codex |
| `CLAUDE.md` | Codex, with Claude-specific content allowed |

## Escalation rule

When there is uncertainty about which lane should own a task:

- default to Claude for security judgment
- default to Codex for repo structure and shared documentation
- ask the human before both lanes edit the same shared file
