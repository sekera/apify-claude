---
allowed-tools: Bash(apify:*), Bash(git:*), Bash(npm:*)
description: Deploy Actor to Apify platform
---

Deploy the current Actor to Apify platform.

## Pre-deployment checklist

1. Verify logged in: `apify whoami`
2. Check for uncommitted changes: `git status`
3. Run build: `npm run build`
4. Run tests if available: `npm test`

## Deploy

```bash
apify push
```

## Post-deployment

Report the deployment URL and any warnings from the push output.
