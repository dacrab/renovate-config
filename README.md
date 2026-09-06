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

- Opens a PR for grouped patch/minor updates (`all non-major dependencies`) and auto-merges it via squash once CI passes — using GitHub's native auto-merge where the plan allows it, otherwise Renovate merges the PR itself once checks are green
- Keeps major updates as regular PRs for manual review
- Waits 3 days after a release before proposing it (`minimumReleaseAge`) to avoid bad publishes, and only automerges between 9am–11pm Athens time
- Weekly lockfile maintenance PR (transitive dep refresh), auto-merged
- Pins `typescript` to `^6` (TS 7 / tsgo lacks a programmatic API some tooling needs)
- Labels security alerts `security` and auto-merges them immediately (exempt from the 3-day wait and schedules)
- Checks dependencies against osv.dev in addition to GitHub advisories
- Migrates its own config when Renovate renames options (`configMigration`)
- Commits with `chore` prefix, timezone Europe/Athens

## Requirements

- CI must run on pull requests (the default) so checks report on the update PR before it merges.
- Auto-merge needs at least one passing check to be meaningful; with no branch protection at all, Renovate merges immediately — enable "Require status checks" where your plan allows it.
- On private repos on the free plan, GitHub's native auto-merge toggle and branch protection are unavailable — Renovate's own merge-after-checks handles those.

## Notes

- `vulnerabilityAlerts` needs Dependabot alerts enabled (Settings → Code security → Dependabot alerts). `osvVulnerabilityAlerts` adds osv.dev coverage on top of it.
- Auto-merged vulnerability fixes skip the queue and are exempt from schedules/limits. Renovate docs warn a wrong advisory can cause a flapping fix — occasionally check merged `[SECURITY]` commits.
- `minimumReleaseAge: 3 days` delays regular updates until a release is 72h old; security fixes are exempt (`minimumReleaseAge: null` inside `vulnerabilityAlerts`).

## License

MIT
