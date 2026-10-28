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

Renovate's onboarding looks for `<owner>/renovate-config` and uses it as the default preset, so new repos pick this up automatically. `local>dacrab/renovate-config` is the platform-relative form of the same preset and also works on github.com.

## What it does

- Extends `config:best-practices`: pins GitHub Actions and Docker digests, pins devDependencies, enables config migration, abandonment reporting, and weekly lockfile maintenance.
- Groups minor/patch/pin/digest updates into one `all non-major dependencies` PR and automerges it once checks pass.
- Requires approval in the Dependency Dashboard before opening major PRs, so they do not consume PR slots or CI time.
- Waits 3 days before proposing npm and crates.io releases (`security:minimumReleaseAgeNpm` / `security:minimumReleaseAgeCrate`), with the preset exemptions for lockfile maintenance, pin, and replacement updates. Digest updates are exempt because Renovate has no release timestamp for them.
- Keeps TypeScript on 6.x for repos that are still on 6.x, because Astro's checker requires it. Repos already on 7.x continue to get 7.x updates.
- Checks dependencies against osv.dev and lists OSV CVEs in the Dependency Dashboard.
- Labels security PRs `security` in addition to `dependencies`, and automerges them once checks pass.
- Runs lockfile maintenance before 4am on Monday and automerges it.
- Default schedule: Sunday before 6am Europe/Athens. Override per repo only if you need a different window.
- Uses the semantic commit prefixes from `config:recommended` (`fix(deps):` / `chore(deps):`) and squash-merges.
- Sets `platformAutomerge: false`, so Renovate waits for checks itself instead of handing the merge to GitHub. This is the same behaviour on public repos and on private repos where branch protection is unavailable.

## Requirements

- CI must run on `pull_request` and actually test the update. Do not automerge in a repo without PR CI: there is nothing for either merge path to wait for.
- Branch protection or rulesets with required checks are still recommended. Without them, Renovate's own check polling is the only gate.
- Dependabot alerts must be enabled for the `vulnerabilityAlerts` block to work. `osvVulnerabilityAlerts` adds osv.dev coverage on top.
- The Renovate app must be enabled for the repo. If there is no Dependency Dashboard issue, Renovate is not running.

## Repo-specific overrides

Block a broken major instead of ignoring a package entirely:

```json
{
  "extends": ["github>dacrab/renovate-config"],
  "packageRules": [
    {
      "matchPackageNames": ["vite"],
      "matchUpdateTypes": ["major"],
      "enabled": false
    }
  ]
}
```

Disable automerge for a repo without CI:

```json
{
  "extends": ["github>dacrab/renovate-config"],
  "packageRules": [
    {
      "matchPackageNames": ["*"],
      "automerge": false
    }
  ],
  "lockFileMaintenance": {
    "automerge": false
  },
  "vulnerabilityAlerts": {
    "automerge": false
  }
}
```

A top-level `"automerge": false` is not enough: the preset's package rules set `automerge: true` for non-major updates, and package rules win over top-level config.

## Validation

CI runs `renovate-config-validator --no-global --strict default.json renovate.json` on every push and pull request. Run it locally with:

```sh
npx --yes --package renovate@44.192.2 -- renovate-config-validator --no-global --strict default.json renovate.json
```

To let the hosted Renovate app validate a config change before merging, push it to a branch named `renovate/reconfigure`.

## License

MIT
