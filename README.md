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
- Waits 3 days after a release before proposing it (`minimumReleaseAge`) to avoid bad publishes, and only automerges between 9am–11pm Athens time
- Weekly lockfile maintenance (transitive dep refresh), auto-merged
- Pins `typescript` to `^6` (TS 7 / tsgo lacks a programmatic API some tooling needs)
- Labels security alerts `security` and auto-merges them immediately (exempt from the 3-day wait and schedules)
- Checks dependencies against osv.dev in addition to GitHub advisories
- Migrates its own config when Renovate renames options (`configMigration`)
- Commits with `chore` prefix, timezone Europe/Athens

## Requirements

`automergeType: "branch"` merges directly into the base branch without a PR, so:

- Your CI must run on `renovate/**` branches (e.g. `push: { branches: [main, 'renovate/**'] }`), or the required status checks will never report and updates will not auto-merge.
- Branch protection on the base branch must not require pull request reviews.
- Renovate branches stuck with pending checks fall back to a PR after ~24h (`prNotPendingHours`).

## Notes

- `vulnerabilityAlerts` needs Dependabot alerts enabled (Settings → Code security → Dependabot alerts). `osvVulnerabilityAlerts` adds osv.dev coverage on top of it.
- Auto-merged vulnerability fixes skip the queue and are exempt from schedules/limits. Renovate docs warn a wrong advisory can cause a flapping fix — occasionally check merged `[SECURITY]` commits.
- `minimumReleaseAge: 3 days` delays regular updates until a release is 72h old; security fixes are exempt (`minimumReleaseAge: null` inside `vulnerabilityAlerts`).

## License

MIT
