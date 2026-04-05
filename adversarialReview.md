---
name: adversarialReview
description: >
  Pre-PR last-look review using scored adversarial agents (Finder/Adversary/Referee).
  Surfaces logic errors, edge cases, and security blind spots in working code.
  Use when code is tested and ready for PR — the final gate before submission.
argument-hint: "<optional: description of what this change does>"
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
  - Agent
  - Edit
user-invocable: true
---
<!-- Version: 2026-04-05.1 -->

# Adversarial Review — Pre-PR Last-Look

Scored multi-agent review using opposing incentives to cancel sycophancy bias. Surfaces what testing and manual review miss: logic holes, edge cases, race conditions, and security blind spots.

**When to use:** Code works, tests pass, known issues are filed. This is the final gate before creating the PR.

**What it does NOT do:** test coverage analysis, type design review, CLAUDE.md compliance, style/formatting. Other tools handle those.

---

## Phase 1: Scope the Review

### 1.1 Gather the Changeset

1. Detect base branch: `git merge-base HEAD main`
2. Get full diff: `git diff main...HEAD`
3. Get changed file list: `git diff main...HEAD --name-only`
4. Get commit messages: `git log main..HEAD --oneline`
5. If `$ARGUMENTS` is provided, note it as additional context for the Finders.

If empty diff, stop: "No changes found on this branch relative to main."

### 1.2 Measure Scope

Count total LOC across all changed files. This determines Finder mode:

| Total LOC in changed files | Logic Finder Mode |
|---|---|
| < 1500 | Single Logic Finder — one Sonnet agent reads everything |
| >= 1500 | Parallel Logic Finders — MECE decomposition into chunks of ~500-1500 LOC by module/directory, plus one cross-cutting Sonnet agent for boundary issues |

### 1.3 Detect Security Surface

Scan the diff for security-relevant patterns. Any match spawns the Security Finder.

**Trigger patterns:**

- **Auth/identity:** `auth`, `token`, `session`, `jwt`, `oauth`, `password`, `credential`, `login`, `signup`
- **IAM/permissions:** `iam`, `policy`, `role`, `permission`, `principal`
- **Data handling:** `encrypt`, `decrypt`, `hash`, `secret`, `api-key`, `apiKey`, `API_KEY`, `.env`
- **Infrastructure:** `securityGroup`, `SecurityGroup`, `bucketPolicy`, `BucketPolicy`, `iamRole`, `IamRole`, `PolicyStatement`
- **Database:** `query(`, `execute(`, `raw(`, `sql`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- **Dependency changes:** files named `package.json`, `package-lock.json`, `requirements.txt`, `Cargo.toml`, `go.mod`

Report what was detected:

- If found: "Security surface detected: [list what triggered]. Spawning Security Finder."
- If not: "No security surface detected. Logic Finder only."

---

## Phase 2: Spawn the Finders

### 2.1 Logic Finder

Launch a Sonnet agent using the Agent tool with `model: "sonnet"`.

**Single Mode (< 1500 LOC)** — one agent, this prompt:

```
You are the LOGIC FINDER in an adversarial review. Your job: find EVERY possible
logic issue in the changed code.

CONTEXT: This code is working and tested. The developer is about to submit a PR.
You are the last line of defense. Focus on what testing doesn't catch: logic errors,
edge cases, race conditions, missing error handling, breaking changes, performance
regressions, dead code introduced.

SCORING:
- +1 point for each LOW impact issue
- +5 points for each MEDIUM impact issue
- +10 points for each CRITICAL issue
Your goal is to maximize your score.

RULES:
- Be aggressive — flag anything questionable rather than missing a real issue
- Categorize every issue: LOW / MEDIUM / CRITICAL
- For each issue: state the file and line, describe the issue, explain the impact
- Do not suggest fixes — just find and report
- Do not flag style, formatting, or naming issues — linters handle that
- Do not flag missing tests — other tools handle that
- Report your total score at the end

COMMIT MESSAGES (for context on intent):
{commit_messages}

ADDITIONAL CONTEXT: {arguments or "None provided"}

CHANGED FILES (read these yourself using the Read tool):
{list of changed file paths}
```

**Parallel Mode (>= 1500 LOC)** — split files into non-overlapping chunks by module/directory, ~500-1500 LOC each. Launch N parallel Sonnet agents, each with the same prompt plus:

```
IMPORTANT: You are one of {N} parallel Logic Finder agents. You are reviewing ONLY
your assigned chunk. Other agents cover other files. Focus deeply on YOUR files —
you are the ONLY agent reading them.

YOUR CHUNK: {chunk_name} ({chunk description})
YOUR FILES: {list of file paths in this chunk}
OTHER CHUNKS (for awareness, do NOT read these files):
{brief list of what other chunks cover}
```

Plus one cross-cutting Sonnet agent in parallel:

```
You are the CROSS-CUTTING LOGIC FINDER. Other agents are deep-diving into
individual modules. Your job: find issues that only appear at the BOUNDARIES
between modules.

Focus on:
- Import chains and circular dependencies
- Function signature mismatches between caller and callee
- State passed between modules (wrong shape, missing fields, stale data)
- Config values used inconsistently across modules
- Error handling gaps at module boundaries (one module throws, caller doesn't catch)

Read these integration-critical files (entry points, shared types, config):
{list of entry points and shared files}

For other changed files, only read function SIGNATURES (first 5-10 lines), not
full bodies. Other agents handle the deep logic review.

Use the same scoring and rules as the other Finders.
```

After all Logic Finder agents return, merge reports:

1. Concatenate all issue lists
2. Deduplicate — keep the more detailed version
3. Renumber issues sequentially

### 2.2 Security Finder (conditional)

Only spawn if security surface was detected. Launch an Opus agent with `model: "opus"`.

```
You are the SECURITY FINDER in an adversarial review. Your job: find EVERY possible
security issue in the changed code.

CONTEXT: This code is working and tested. The developer is about to submit a PR.
You are the security gate. Another agent handles logic issues — you focus exclusively
on security.

WHAT WAS DETECTED: {security surface triggers from Phase 1.3}

SCORING (weighted higher — security misses are costlier):
- +2 points for each LOW impact issue
- +10 points for each MEDIUM impact issue
- +20 points for each CRITICAL issue
Your goal is to maximize your score.

LOOK FOR:
- Injection vulnerabilities (SQL, command, XSS, template)
- Authentication/authorization bypass
- Data exposure (secrets in code, overly broad API responses, logging sensitive data)
- Insecure defaults (open CORS, permissive IAM, missing rate limits)
- Missing input validation at system boundaries
- Dependency risks (known CVEs, typosquatting, unnecessary permissions)
- IAM over-permission (broader than needed for the operation)
- Secrets or credentials in code or config
- Cryptographic misuse (weak algorithms, hardcoded keys, missing salt)

RULES:
- Be aggressive — flag anything that could be a security issue
- Categorize every issue: LOW / MEDIUM / CRITICAL
- For each issue: state the file and line, describe the vulnerability, explain what
  an attacker could do
- Do not suggest fixes — just find and report
- Report your total score at the end

CHANGED FILES (read these yourself using the Read tool):
{list of changed file paths}
```

Security Finder runs in parallel with Logic Finder(s).

### 2.3 Merge Finder Reports

After ALL Finders return:

1. Combine all issue lists into one merged report
2. Prefix each issue with its source: `[LOGIC]` or `[SECURITY]`
3. Deduplicate — if both flagged same issue, keep the Security Finder's version
4. Renumber issues sequentially
5. Calculate combined score

---

## Phase 3: Spawn the Adversary

Launch an Opus agent with `model: "opus"`.

```
You are the ADVERSARY in an adversarial review. The Finders have identified issues
in changed code. Your job: disprove as many as possible.

SCORING:
- You EARN the score of each issue you successfully disprove
  (disprove a +10 MEDIUM issue = you get +10)
- You LOSE 2x the score of each issue you wrongly disprove
  (wrongly disprove a +10 MEDIUM issue = you lose -20)
Your goal is to maximize your score. This means: be aggressive about disproving
weak findings, but careful — wrong calls cost double.

RULES:
- For each issue the Finders raised, give your verdict: CONFIRMED or DISPROVED
- If DISPROVED: explain exactly why this is not actually an issue. Cite specific
  code that prevents the claimed problem (guard clauses, upstream validation,
  framework guarantees, etc.)
- If CONFIRMED: briefly state why the issue is real (no penalty for confirming)
- Read the actual code yourself — do not trust the Finders' descriptions alone
- Report your total score at the end

FINDERS' MERGED REPORT:
{merged finder report}

CHANGED FILES (read these yourself using the Read tool):
{list of changed file paths}
```

Wait for the Adversary to complete.

---

## Phase 4: Spawn the Referee

Launch an Opus agent with `model: "opus"`.

```
You are the REFEREE in an adversarial review. Two sides have given opposing
assessments. The Finders identified issues. The Adversary tried to disprove them.
You determine the truth.

IMPORTANT: A ground truth exists and your assessment will be compared against it.
You get +1 for each correct ruling and -1 for each incorrect ruling.

RULES:
- For each issue, review BOTH the Finders' argument and the Adversary's rebuttal
- Read the actual code yourself — you are the third independent pair of eyes
- Give your FINAL VERDICT: REAL ISSUE or FALSE POSITIVE
- If REAL ISSUE: assign final severity (LOW / MEDIUM / CRITICAL) — you may change
  the Finders' severity if warranted. State file and line number.
- If FALSE POSITIVE: explain why the Adversary was right to disprove it
- Be precise — do not hedge or equivocate
- Output a clean final report with ONLY the confirmed real issues, grouped by severity

FINDERS' REPORT:
{merged finder report}

ADVERSARY'S REPORT:
{adversary report}

CHANGED FILES (read these yourself using the Read tool):
{list of changed file paths}
```

Wait for the Referee to complete.

---

## Phase 5: Present Results

Take the Referee's output and present a clean report:

```markdown
# Last-Look Review: {branch-name}

## Verdict: {CLEAN or ISSUES FOUND or BLOCK}

## Stats
- Finder issues raised: {total from merged report}
- Disproved by Adversary: {count of DISPROVED}
- Confirmed by Referee: {count of REAL ISSUE}
- Confidence: {confirmed}/{total} survived challenge

## Confirmed Issues

### CRITICAL
{numbered list: [file:line] — description — why it matters}

### MEDIUM
{numbered list: [file:line] — description — why it matters}

### LOW
{numbered list: [file:line] — description — why it matters}

## Disproved (for reference)
{bulleted list of issues that didn't survive challenge}
```

**Verdict logic:**

- **CLEAN** — zero confirmed issues
- **ISSUES FOUND** — confirmed issues, all LOW or MEDIUM
- **BLOCK** — any CRITICAL confirmed issue

Omit severity sections with zero issues.

After the report, ask: "Want me to fix the confirmed issues?" If yes, fix directly in session.

---

## Rules

1. **Never skip the Adversary** — the whole point is adversarial challenge
2. **Agents read files themselves** — pass file paths, not contents
3. **Sequential after Finders** — Finders parallel, then Adversary, then Referee
4. **No piped commands** — use Grep/Read tools instead of shell pipes, one operation per Bash call
5. **Don't fix during review** — find first, present report, then fix if asked
6. **Respect the scope** — only review changed files on the branch
