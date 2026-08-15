# Issue tracker: GitHub

Issues and PRDs for this repo live as GitHub issues in the repository configured by the local `origin` remote:

- `https://github.com/qwertfyghjbn/codex-security-learing.git`

Use the `gh` CLI for issue operations when GitHub access is configured.

## Conventions

- Create an issue: `gh issue create --title "..." --body "..."`
- Read an issue: `gh issue view <number> --comments`
- List issues: `gh issue list --state open`
- Comment on an issue: `gh issue comment <number> --body "..."`
- Apply or remove labels: `gh issue edit <number> --add-label "..."` or `--remove-label "..."`
- Close an issue: `gh issue close <number> --comment "..."`

## When a skill says "publish to the issue tracker"

Create a GitHub issue in this repository.

## When a skill says "fetch the relevant ticket"

Use `gh issue view <number> --comments`.

## Local fallback

If GitHub access is not configured in the current environment, record pending issue content locally under:

- `docs/agents/backlog/`

Then publish to GitHub later from a configured environment.
