# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for dacrab repos.

## Usage

Add to your repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>dacrab/renovate-config"]
}
```

For Unity projects use `local>dacrab/renovate-config` instead (Renovate's `github>` preset syntax requires a public GitHub host; `local>` resolves the same file from the local clone).

## What it does

- Groups all patch/minor updates into one `all non-major dependencies` update and auto-merges it via branch automerge (no PR when CI is green; a PR is raised as backup if checks fail or stay pending >24h)
- Keeps major updates as regular PRs for manual review
- Pins `typescript` to `^6` (TS 7 / tsgo lacks a programmatic API some tooling needs)
- Labels security alerts `security` and auto-merges them immediately
- Commits with `chore` prefix, timezone Europe/Athens

## Requirements

`automergeType: "branch"` merges directly into the base branch without a PR, so:

- Your CI must run on `renovate/**` branches (e.g. `push: { branches: [main, 'renovate/**'] }`), or the required status checks will never report and updates will not auto-merge.
- Branch protection on the base branch must not require pull request reviews.
- Renovate branches stuck with pending checks fall back to a PR after ~24h (`prNotPendingHours`).

## Notes

- `vulnerabilityAlerts` only works on GitHub repos with the Dependency graph + Dependabot alerts enabled (Settings → Code security).
- Auto-merged vulnerability fixes skip the queue and are exempt from schedules/limits. Renovate docs warn a wrong advisory can cause a flapping fix — occasionally check merged `[SECURITY]` commits.

## License

MIT
