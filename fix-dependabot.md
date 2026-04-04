---
name: fix-dependabot
description: Scan and fix Dependabot security alerts across all component repos in a project. Run from the project root (scaffold repo). Reads manifest.json for components, audits each, creates worktrees with fixes, and opens PRs.
---
<!-- Version: 2026-04-04.3 -->

# Fix Dependabot Security Alerts

Scan all component repos in a project, fix vulnerable transitive dependencies, and open PRs.

## Prerequisites

- Run from the **project root** (scaffold repo directory)
- `manifest.json` must exist with a `components` object
- Component directories must exist locally (on `main`)
- `gh` CLI authenticated

## Bash Rules

**Never chain commands.** Each Bash call must be a single operation:
- `cd` must be its own separate Bash call — never `cd /path && npm audit`
- No `&&`, `;`, `||`, or pipes
- After `cd`, the working directory persists for subsequent Bash calls

## Phase 1 — Scan

1. Read `manifest.json` from the current directory
2. For each component that has a local `path` directory:
   - `cd` into the component directory (separate Bash call)
   - Run `npm audit --json` (separate Bash call)
   - If no root `package.json` exists, scan for nested `package.json` files (monorepo). Audit each subdirectory that has one — `cd` then `npm audit --json` separately for each.
   - Parse the JSON output to extract: total alerts, severity counts, vulnerable packages
3. Present a summary table:

   ```
   Component          Alerts  Critical  High  Medium  Low
   project-infra         18         1     9       7    1
   project-web            5         0     2       3    0
   project-admin          0         —     —       —    —
   ```

4. Skip components with zero alerts
5. Ask: **"Fix all N components with alerts, or pick specific ones?"**

Wait for confirmation before proceeding.

## Phase 2 — Fix (per selected component)

For each component, run these steps sequentially:

### Step 1: Create worktree

```bash
mkdir -p .worktrees/chore-dependabot
```

```bash
git -C <component-path> worktree add ../../.worktrees/chore-dependabot/<component-path> -b chore/dependabot-fixes
```

If the branch already exists (from a previous run), use it instead of `-b`.

### Step 2: Baseline test

Before making changes, run tests in the worktree to establish a baseline:

```bash
npm test
```

Record whether tests pass or fail. If tests fail here, these are **pre-existing failures** — not caused by the audit fix. Note them but continue.

### Step 3: npm audit fix

Working inside `.worktrees/chore-dependabot/<component-path>/`:

```bash
npm audit fix
```

### Step 4: Check remaining alerts

```bash
npm audit --json
```

Parse the output. Classify remaining alerts into three categories:

**Fixable via overrides** — transitive dev deps with known patched versions that appear in the dependency tree (not bundled):
- Add `overrides` entries to `package.json` targeting the patched versions
- Example: `"overrides": { "handlebars": ">=4.7.8", "picomatch": ">=4.0.4" }`
- If `overrides` already exists in package.json, merge into it (do not replace)

**Bundled inside a parent package** (e.g., `brace-expansion` and `yaml` bundled inside `aws-cdk-lib`):
- Overrides will NOT work for these — the vulnerable code is shipped inside the parent package's tarball
- Skip these. Report them as: "Resolves when `<parent-package>` releases a patched version"

**Needs `--force` (breaking major version bump)** (e.g., `esbuild <=0.24.2` → `0.28.0`):
- Do NOT attempt to fix these
- Report them as: "Needs manual intervention — breaking change to `<package>@<target-version>`"

After adding overrides (if any):

```bash
npm install
```

```bash
npm audit --json
```

Report what was resolved and what remains by category.

### Step 5: Build

```bash
npm run build
```

If build fails: **stop this component**, report the failure, move to the next component. Do not push broken code.

### Step 6: Test

```bash
npm test
```

- If no test script exists, skip.
- If tests fail but the **baseline also failed** (Step 2): the failure is pre-existing. **Continue** — the audit fix didn't cause it. Note it in the PR body.
- If tests fail but the **baseline passed**: the audit fix broke something. **Stop this component**, report the failure, move to the next component.

## Phase 3 — PR (per component)

After a component passes build + test:

1. Stage changes:
   ```bash
   git add package.json package-lock.json
   ```

2. Commit — write the message to `/tmp/<project>-commit-msg.txt` using the Write tool, then commit with `--file`:
   ```bash
   git commit --file /tmp/<project>-commit-msg.txt
   ```
   Message content:
   ```
   Fix Dependabot security alerts

   - npm audit fix for semver-compatible updates
   - Added overrides for transitive dev dependencies
   ```

3. Push:
   ```bash
   git push -u origin chore/dependabot-fixes
   ```

4. Create PR via `gh pr create`:
   - **Title:** `Fix Dependabot security alerts`
   - **Body:** Summary of what was fixed — packages updated, overrides added, remaining alerts if any
   - Target repo is the component's GitHub repo from manifest

5. Report the PR URL

## Phase 4 — Summary

After all components are processed, present:

```
PRs Created:
  project-infra  — PR #42 (15/18 alerts fixed)
  project-web    — PR #17 (5/5 alerts fixed)

Remaining Alerts:
  Alert  Package           Why unfixable
  #11    brace-expansion   Bundled inside aws-cdk-lib
  #5     yaml              Bundled inside aws-cdk-lib
  #8     esbuild           Needs manual intervention (breaking change to 0.28.0)

Skipped:
  project-admin  — no alerts

Pre-existing test failures:
  project-infra  — frontend-stack.test.ts (unrelated to audit fix)
```

Worktrees remain at `.worktrees/chore-dependabot/` until PRs merge.

## Known Errors

**E401 — npm auth token invalid:** If `npm audit fix` or `npm install` fails with E401, check two things:

1. **Global `.npmrc`** — stale auth token for registry.npmjs.org. If the component uses no private packages, suggest: `npm config delete //registry.npmjs.org/:_authToken`
2. **`package-lock.json` resolved URLs** — lockfiles may point to a private registry (e.g., AWS CodeArtifact) with an expired token. Check `resolved` URLs in the lockfile. If the project doesn't need the private registry, offer to regenerate the lockfile against the public registry (`rm package-lock.json` then `npm install`). If it does need it (e.g., `claudetrail-collector`), suggest the user re-authenticate (`! aws codeartifact login ...` or `! npm login`).

Ask the user to run auth commands themselves — never attempt registry authentication automatically.

## Rules

1. **Always confirm** before creating worktrees or PRs
2. **Never use `npm audit fix --force`** — major version bumps risk breaking changes
3. **Stop on build/test failure** — do not push or PR broken code
4. **npm only** — skip components without npm (report them)
5. **One branch per component** — `chore/dependabot-fixes`
6. **No Co-Authored-By** in commit messages
7. **No Dependabot API calls** — GitHub auto-closes alerts when the patched lockfile merges
