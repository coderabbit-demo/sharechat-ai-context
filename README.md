# sharechat-ai-context

ShareChat's shared skills marketplace. Skills live here once, and every repo installs them from here
instead of keeping its own copy, so one change applies everywhere.

## Plugins

| Plugin | Skills | Use it for |
|---|---|---|
| `api-compatibility` | `mobile-api-compatibility` | Changing or consuming APIs without breaking installed app versions |
| `performance` | `backend-performance` | Keeping high-traffic endpoints fast: no N+1 calls, timeouts, bounded caches |

## Layout

```
.claude-plugin/marketplace.json        lists every plugin in this repo
plugins/
  api-compatibility/
    .claude-plugin/plugin.json
    skills/mobile-api-compatibility/
      SKILL.md
      references/compatibility-rules.md
  performance/
    .claude-plugin/plugin.json
    skills/backend-performance/
      SKILL.md
```

Skills hold the *how*. They don't hold API contracts or service-specific details; each service
keeps its own contract next to its code (e.g. `sharechat-feed-service/api/openapi.yaml`).

## Install

```
/plugin marketplace add <org>/sharechat-ai-context
/plugin install api-compatibility@sharechat-ai-context
```

## Adding a skill

Add it under an existing plugin's `skills/`, or create a new plugin folder and list it in
`.claude-plugin/marketplace.json`. Keep skills general enough for more than one repo.
