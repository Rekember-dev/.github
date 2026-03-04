# Contributing to Rekember

Welcome to the Rekember development team. This document covers everything you need to know to contribute effectively — branch structure, the PR workflow, how the CI/CD pipeline works, and what to do when something goes wrong.

If you have questions not covered here, reach out to a lead on Discord.

---

## Repositories

| Repo | Purpose |
|------|---------|
| `rekember-server` | rAthena source code, database configs, custom scripts |
| `rekember-client` | Game assets distributed to players (sprites, maps, UI, etc.) |
| `rekember-deploy` | Build automation, patch packaging, GitHub Actions pipelines |

> **Note:** You will rarely need to touch `rekember-deploy` directly. It runs automatically. Only leads and the pipeline manager should modify workflow files there.

---

## Branch Structure

```
develop ──[PR, reviewed]──► production ──[tag]──► patch build ──[PR, reviewed]──► live
```

| Branch | Purpose | Who can push |
|--------|---------|--------------|
| `develop` | Active development — all work goes here | Everyone |
| `production` | QA-ready staging — triggers builds | Leads only, via PR |
| `live` | What players use — current release | Leads only, via PR from production |

### Rules
- **Never commit directly to `production` or `live`** — always use a PR
- **Feature branches** should be cut from `develop` and merged back into `develop`
- **`main` is retired** — do not use it

---

## Day-to-Day Workflow

### Starting new work
Always branch from `develop`:
```bash
git checkout develop
git pull
git checkout -b feature/your-feature-name
```

### Finishing work
Push your branch and open a PR targeting `develop`. Get it reviewed and merged.

---

## Promoting to Production (Leads only)

When `develop` is stable and ready for a release:

1. Open a PR from `develop` → `production`
2. The server build will trigger automatically on `rekember-deploy`
3. **Before merging** — check that the build passed:
   👉 [rekember-deploy Actions](https://github.com/Rekember-dev/rekember-deploy/actions)
4. Get lead approval on the PR
5. Merge using **merge commit**

### After merging to production — Client patch

When client assets are ready to be distributed to players:

1. Create a tag on `production` in `rekember-client`:
   ```bash
   git checkout production
   git pull
   git tag client-v1.2.0
   git push origin client-v1.2.0
   ```
2. This automatically triggers the client patch build on `rekember-deploy`
3. A ZIP patch file is built and published to GitHub Releases
4. Check the build passed:
   👉 [rekember-deploy Actions](https://github.com/Rekember-dev/rekember-deploy/actions)

> Tag format: `client-vX.X.X` for client, `server-vX.X.X` for server

### After merging to production — Server build

Tag `production` in `rekember-server` to trigger a server build:
```bash
git checkout production
git pull
git tag server-v1.2.0
git push origin server-v1.2.0
```

---

## Promoting to Live (Leads only)

Once builds are confirmed passing on `production`:

1. Open a PR from `production` → `live` on both `rekember-client` and `rekember-server`
2. Get lead approval
3. Merge using **merge commit**
4. Log the release — date, version tags, what changed

> `live` is a historical record of exactly what players are running. Treat it carefully.

---

## Hotfix Workflow

Use this when something is broken on `production` or `live` and needs an urgent fix. Every hotfix must go through `production` — never patch `live` directly or the next release will overwrite your fix.

### Step by step

1. **Cut a hotfix branch from `production`** (not develop, not live):
   ```bash
   git checkout production
   git pull
   git checkout -b hotfix/describe-the-fix
   ```

2. **Make the fix, commit, push**

3. **Go to rekember-deploy → Actions → "Hotfix Chain"**
    - Input: repo name, hotfix branch name, tag (e.g. `client-v1.2.1-hotfix`)
    - This opens a PR: `hotfix/your-fix` → `production`

4. **Review and approve the PR — use SQUASH AND MERGE**

5. **Go to rekember-deploy → Actions → "Hotfix Propagate"**
    - Input: same repo and tag
    - This auto-merges `production` → `develop` (merge commit)
    - This opens a PR: `production` → `live`

6. **Review and approve the live PR — use MERGE COMMIT**

7. Hotfix chain complete ✓

### Why this order matters
```
hotfix/* → production (human approval, squash)
    └── production → develop (automatic, merge commit)
    └── production → live (human approval, merge commit)
```
If you skip merging back into `develop`, the next release from `develop` will overwrite your hotfix on `production`.

---

## CI/CD Pipeline

All build logic lives in `rekember-deploy`. The client and server repos contain only thin dispatcher workflows that trigger builds there.

| Trigger | What happens |
|---------|-------------|
| PR opened targeting `production` in rekember-server | Server build fires automatically |
| Tag `client-vX.X.X` pushed to rekember-client | Client patch ZIP is built and published |
| Tag `server-vX.X.X` pushed to rekember-server | Server binaries are built and uploaded as artifact |
| Hotfix Chain workflow run manually | Opens hotfix → production PR |
| Hotfix Propagate workflow run manually | Merges production → develop, opens production → live PR |

### Checking build status
Always check here before merging a production PR:
👉 [rekember-deploy Actions](https://github.com/Rekember-dev/rekember-deploy/actions)

### Build is failing — what do I do?
- Check the failing step in rekember-deploy Actions for the error
- Do not merge the PR until the build passes
- If you can't identify the issue, ping a lead on Discord

---

## PR Checklist

Every PR will include this checklist automatically. Do not skip it.

- [ ] All relevant builds are green — check [rekember-deploy Actions](https://github.com/Rekember-dev/rekember-deploy/actions)
- [ ] Changes have been reviewed by a lead
- [ ] No test, debug, or placeholder files included

---

## Commit Message Convention

Keep commit messages clear and consistent:

```
type: short description of what changed

Optional longer explanation if needed.
```

| Type       | When to use                          |
|------------|--------------------------------------|
| `feat`     | New feature or content               |
| `fix`      | Bug fix                              |
| `chore`    | Maintenance, configs, tooling        |
| `refactor` | Code restructure, no behavior change |
| `wip`      | Work in progress — do not merge      |
| `docs`     | Documentation                        |

Examples:
```
feat: add Soul Warden Mors Interna skill
fix: correct SC_WITHER duration calculation
chore: update skill_db.yml enum names
wip: homunculus infusion system in progress
```

---

## Tooling Reference

| Tool | Purpose |
|------|---------|
| rAthena | Game server emulator (C++, YAML config) |
| MySQL / XAMPP | Local database for development |
| GRF Editor | Pack/unpack client GRF archives |
| BrowEdit 2 | Map editor (.rsw, .gnd, .gat) |
| Elurair | Client patcher — distributes ZIP patches to players |
| Visual Studio | C++ build tools for recompiling rAthena locally |

---

## Getting Help

- **Discord** — fastest way to reach the team
- **Linear** — task tracking and issue management
- **rekember-deploy README** — full pipeline architecture reference