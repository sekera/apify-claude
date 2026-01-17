---
description: Deploy to Apify, wait for build, and fix errors until build succeeds (iterative loop)
allowed-tools: Bash(*), Read(*), Edit(*), Write(*), Glob(*), Grep(*), Skill(*)
---

# Deploy and Get Working

Automated workflow to deploy code to Apify platform, wait for build, and fix any build errors until the build succeeds. For simple one-shot deploy, use `/deploy` instead.

## Pre-flight Checks (REQUIRED)

### 1. Verify Apify CLI Login

```bash
apify info
```

If not logged in, stop and inform the user to run `apify login` first.

### 2. Verify Actor Target

The `apify push` command uses:
- **Actor name** from `.actor/actor.json` (`name` field)
- **Logged-in user** to form `<username>/<actor-name>`

Check the current actor configuration:

```bash
cat .actor/actor.json | grep '"name"'
apify info
```

**STOP and confirm with the user:**
- "This will push to actor `<username>/<actor-name>`. Is this correct?"
- If the actor doesn't exist yet, it will be created
- Only proceed after user confirms the target is correct

### 3. Check Git Integration

If `apify info` shows the actor source is "Git repository":

1. **STOP and ask the user**: "This actor is currently linked to a Git repository. Running `apify push` will break Git integration and switch to Web IDE source. Do you want to proceed?"
2. Only continue if the user explicitly confirms
3. If user declines, suggest using `/commit` instead

### 4. Check for Uncommitted Changes

```bash
git status
```

If there are uncommitted changes, inform the user. They may want to commit first.

### 5. Run Tests (if available)

```bash
npm test
```

If tests fail, fix them before proceeding.

## Workflow

Execute this loop until the build succeeds:

### 1. Build Locally First

Validate TypeScript compiles before pushing:

```bash
npm run build
```

If local build fails, fix TypeScript errors before proceeding.

### 2. Push to Apify

Deploy directly to Apify platform:

```bash
apify push
```

This uploads source code and triggers a build on Apify infrastructure.

### 3. Wait for Build

Poll build status:

```bash
# Wait for build to start processing
sleep 5

# Check build status
apify builds ls
```

Keep polling every 10-15 seconds until the latest build shows "Succeeded" or "Failed".

### 4. Check Build Result

If **SUCCEEDED**: Done! Report success to user.

If **FAILED**:
1. Fetch build log using the build ID from `apify builds ls`:
   ```bash
   apify builds log <BUILD_ID>
   ```

2. Analyze the error type:
   - Schema validation errors → Fix `*_schema.json` files
   - Dockerfile errors → Fix `Dockerfile`
   - Dependency errors → Fix `package.json`, run `npm install`
   - TypeScript errors → Fix source files, run `npm run build`

3. Apply fix locally

4. **Repeat from step 1** (build locally and push again)

## Arguments

$ARGUMENTS - Optional: specific files to focus on, or "skip-build" to skip local build step

## Error Type Reference

| Error Pattern | Fix Location |
|--------------|--------------|
| `Invalid input schema` | `.actor/input_schema.json` |
| `Invalid output schema` | `.actor/output_schema.json` |
| `Invalid dataset schema` | `.actor/dataset_schema.json` |
| `COPY failed` | `Dockerfile` |
| `npm ERR!` | `package.json` |
| `TS\d+:` | TypeScript source files |
| `Cannot find module` | Missing dependency |

## Apify CLI Commands Reference

```bash
# Check login status and actor info
apify info

# Login to Apify (if needed)
apify login

# Push to Apify (triggers build)
apify push

# Push to specific actor (override actor.json name)
apify push --actor-id <username>/<actor-name>

# List recent builds
apify builds ls

# Get build log
apify builds log <BUILD_ID>

# Run the actor
apify call
```

## Success Criteria

The workflow completes when:
- Pre-flight checks pass (logged in, correct target confirmed)
- Local `npm run build` passes
- `apify push` succeeds
- Build status is `SUCCEEDED`
- No errors in build log

Report the final build URL to the user:
`https://console.apify.com/actors/<actorId>/builds/<buildId>`

## Restoring Git Integration

If you need to restore Git integration after using this command:
1. Go to Apify Console → Actor Settings → Source
2. Change source type back to "Git repository"
3. Re-link the repository URL and branch
