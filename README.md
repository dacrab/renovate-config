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

## What it does

- Auto-merges patch and minor updates via branch automerge (no PR)
- Groups all non-major dependencies into a single update
- Labels security alerts and auto-merges them
- Commits with `chore:` prefix
- Timezone: Europe/Athens

## Requirements

`automergeType: "branch"` merges directly into the base branch without a PR, so:

- Your CI must run on `renovate/**` branches (e.g. `push: { branches: [main, 'renovate/**'] }`), or the required status checks will never report and updates will not auto-merge.
- Branch protection on the base branch must not require pull request reviews.

## License

MIT
