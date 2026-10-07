# sharechat-ai-context

ShareChat's shared skills marketplace. Skills live here once, and every repo installs them from here
instead of keeping its own copy, so one change applies everywhere.

## Plugins

| Plugin | Skills | Use it for |
|---|---|---|
| `api-compatibility` | `mobile-api-compatibility` | Changing or consuming APIs without breaking installed app versions |

## Layout

```
.claude-plugin/marketplace.json        lists every plugin in this repo
plugins/
  api-compatibility/
    .claude-plugin/plugin.json
    skills/mobile-api-compatibility/
      SKILL.md
      references/compatibility-rules.md
```

Skills hold the *how*. They don't hold API contracts or service-specific details; each service
keeps its own contract next to its code (e.g. `sharechat-feed-service/api/openapi.yaml`).

## Install

```
/plugin marketplace add <org>/sharechat-ai-context
/plugin install api-compatibility@sharechat-ai-context
```

## CodeRabbit pre-merge checks

`coderabbit/` holds CodeRabbit config fragments generated from the skills, so pre-merge checks in every
repo enforce the exact rule text. Don't edit them by hand: change the skill, then run

```
node scripts/generate-coderabbit-checks.mjs
```

CI fails if a fragment is out of date. A repo uses a fragment from its `.coderabbit.config.ts`:

```ts
import { defineConfig, includeRemote, mergeConfig } from "@coderabbitai/config"

const mobileApiCompatibility = includeRemote({
	repo: "coderabbit-demo/sharechat-ai-context",
	path: "coderabbit/mobile-api-compatibility.ts",
	ref: "main",
})

export default defineConfig(mergeConfig(mobileApiCompatibility, { /* repo settings */ }))
```

## Adding a skill

Add it under an existing plugin's `skills/`, or create a new plugin folder and list it in
`.claude-plugin/marketplace.json`. Keep skills general enough for more than one repo.
