# Triage Labels

The repo uses five canonical triage roles. Agents should use these exact label strings in GitHub Issues unless the repository label vocabulary changes later.

| Canonical role | GitHub label | Meaning |
| --- | --- | --- |
| `needs-triage` | `needs-triage` | Maintainer needs to evaluate the issue |
| `needs-info` | `needs-info` | Waiting on the reporter for more information |
| `ready-for-agent` | `ready-for-agent` | Fully specified and ready for an agent to execute |
| `ready-for-human` | `ready-for-human` | Requires a human to implement or decide |
| `wontfix` | `wontfix` | Will not be actioned |

## Intended lifecycle

Typical progression:

1. new issue -> `needs-triage`
2. missing context -> `needs-info`
3. clear and agent-executable -> `ready-for-agent`
4. clear but requires human judgment or implementation -> `ready-for-human`
5. explicitly not pursued -> `wontfix`

## Usage notes

- Use labels to express workflow state, not severity.
- Do not overload these labels with security severity or priority meaning.
- Security severity, confidence, or exploitability should be tracked separately in findings or issue body content.
