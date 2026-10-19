# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for dacrab repos.

## Usage

Add this to a repo's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>dacrab/renovate-config"]
}
```

On github.com, `local>dacrab/renovate-config` resolves to the same preset, so either form works. `local>` is only useful if you later move to a self-hosted Git host; it is not required for Unity or private repos.

Renovate also auto-discovers a repository named `renovate-config` in the account and suggests it during onboarding, so new repos pick this up automatically.

## What it does

- Extends `config:best-practices`: pins GitHub Actions and Docker digests, pins devDependencies, enables config migration, abandonment reporting, and weekly lockfile maintenance.
- Groups minor/patch/pin/digest updates into one `all non-major dependencies` PR and automerges it once CI is green.
- Requires approval in the Dependency Dashboard before opening major PRs, so they do not consume PR slots or CI time.
- Waits 3 days before proposing npm releases (`security:minimumReleaseAgeNpm`), with the correct exemptions for lockfile maintenance, pin, and replacement updates. Digest updates are exempt because Renovate has no release timestamp for them.
- Keeps `typescript` on 6.x until Astro tooling supports TypeScript 7.
- Blocks `@sveltejs/adapter-vercel` 7.x until SvelteKit 3 is stable (v7 requires Kit 3, so it cannot build on Kit 2).
- Checks dependencies against osv.dev and lists OSV CVEs in the Dependency Dashboard.
- Labels security PRs `security` in addition to `dependencies`, and automerges them once checks pass. Security PRs skip the normal schedule and PR limits.
- Runs lockfile maintenance before 4am on Monday and automerges it.
- Default schedule: Sunday before 6am Europe/Athens. Override per repo only if you need a different window.
- Uses the semantic commit prefixes from `config:recommended` (`fix(deps):` / `chore(deps):`) and squash-merges.

## Requirements

- CI must run on `pull_request` so update PRs get checks. Without checks, automerge will not run.
- Enable "Require status checks to pass" where available, so native auto-merge has something to wait for. Private repos on the free plan cannot enable native auto-merge; Renovate falls back to merging after checks pass.
- Dependabot alerts must be enabled for the `vulnerabilityAlerts` block to work. `osvVulnerabilityAlerts` adds osv.dev coverage on top.

## Repo-specific overrides

Block only a broken major instead of ignoring a package entirely:

```json
{
  "packageRules": [
    {
      "matchPackageNames": ["vite"],
      "matchUpdateTypes": ["major"],
      "enabled": false
    }
  ]
}
```

For a repo where updates must never auto-merge (for example a Unity project without CI):

```json
{
  "automerge": false,
  "dependencyDashboardApproval": true
}
```

## Validation

CI runs `renovate-config-validator --no-global --strict` on every push and pull request. Run it locally with:

```sh
npx --yes --package renovate -- renovate-config-validator --no-global default.json --strict
```

To let the hosted Renovate app validate a config change before merging, push it to a branch named `renovate/reconfigure`.

## License

MIT
