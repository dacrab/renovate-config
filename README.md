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

- Auto-merges patch and minor updates
- Groups all non-major dependencies into a single PR
- Labels security alerts and auto-merges them
- Commits with `chore(deps):` prefix
- Cooldown of 3 days between PRs
- Timezone: Europe/Athens

## License

MIT
