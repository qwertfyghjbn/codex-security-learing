# Runtime Guardrails

This document defines the minimum runtime protections for parallel agent work in this repository.

## Goals

- prevent cross-agent overwrite and drift
- reduce accidental secret exposure
- keep security analysis reproducible
- separate findings from implementation changes

## Execution defaults

- Prefer read-only inspection first.
- Use fake credentials and synthetic inputs unless real access is explicitly required and approved.
- Avoid running third-party repository code, package lifecycle scripts, and uncontrolled installers during static review stages.
- Do not assume local sandboxing alone is sufficient protection for high-risk experiments.

## Secrets policy

- Default to fake values such as `FAKE_*` placeholders.
- Do not put real API keys, tokens, passwords, or cloud credentials in prompts, repo files, or findings.
- If real credentials are ever needed, use:
  - least privilege
  - single-purpose credentials
  - short lifetime
  - isolated environment

## Parallel work protections

- Prefer separate branches for Codex and Claude work.
- Use separate worktrees for truly parallel sessions when both agents need write access.
- Avoid concurrent edits to the same file.
- Shared files must have a primary editor role.

## Directory ownership defaults

- Codex-owned:
  - `docs/codex-security/`
  - `docs/agents/`
  - repo-structure and non-security docs
- Claude-owned:
  - `docs/security/`
  - `reports/security/`
  - `docs/agents/findings/`

## High-risk action policy

The following require explicit human approval before execution:

- running real security scans against live or sensitive targets
- using real provider credentials
- executing third-party target code
- broad dependency installation in untrusted repos
- destructive cleanup outside clearly scoped temp paths
- publishing unconfirmed security findings externally

## Security finding flow

Recommended order:

1. inspect
2. write standalone finding
3. review confidence and evidence
4. decide whether a fix is needed
5. only then modify shared docs or code

## Isolation ladder

For higher-risk experiments, prefer this order:

```text
main machine
  -> isolated user account
  -> isolated branch/worktree
  -> container where useful
  -> disposable VM for highest-risk work
```
