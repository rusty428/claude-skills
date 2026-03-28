---
name: reviewRepo
description: Evaluate a third-party GitHub repo for adoption, extraction, or skipping — clone it locally, run a triage scan, then optionally dig deeper. Use when reviewing an external repo to decide if it fits our workflow.
---
<!-- Version: 2026-03-28.2 -->

# Review Repo — Third-Party Evaluation

Evaluate a GitHub repo to answer: adopt it, extract useful parts, or skip it.

## Invocation

`/reviewRepo <github-url>`

Example: `/reviewRepo https://github.com/gsd-build/get-shit-done`

## Phase 1: Setup

### 1.1 Parse the URL

Extract owner and repo name from the GitHub URL.

- `https://github.com/gsd-build/get-shit-done` → owner: `gsd-build`, repo: `get-shit-done`
- Clone directory: `~/workspace/tmp/<owner>-<repo>` (e.g., `~/workspace/tmp/gsd-build-get-shit-done`)

### 1.2 Clone or Reuse

Check if `~/workspace/tmp/<owner>-<repo>` already exists.

**If it exists**, ask:
> "It looks like we may have reviewed this repo already. The directory `~/workspace/tmp/<owner>-<repo>` exists. Want me to:
> 1. Use the existing clone as-is
> 2. Pull latest into the existing clone
> 3. Remove it and start fresh"

**If it does not exist**, clone:
```
git clone <url> ~/workspace/tmp/<owner>-<repo>
```

### 1.3 Establish "Our" Context

Read these sources to understand what we currently do:

| Source | Priority | Purpose |
|--------|----------|---------|
| `~/.claude/CLAUDE.md` | Primary | Current workflow, conventions, tools |
| `~/workspace/CLAUDE.md` | Secondary | Workspace-level project context |
| Installed skills/plugins | Supporting | Existing skill-based workflows |

This context informs the "overlaps" and "new capabilities" sections of the triage report.

## Phase 2: High-Level Triage Scan

Perform a fast sweep of the cloned repo. Do not read every file — scan structure, manifests, and key indicators.

### 2.1 What Is It?

- Read README (or equivalent)
- Read package.json, Cargo.toml, pyproject.toml, go.mod, or whatever manifest exists
- Note the license

### 2.2 Structure and Size

- List top-level directory layout
- Count files and estimate lines of code
- Note the language breakdown

### 2.3 Stack and Dependencies

- Language, framework, key runtime dependencies
- Dev dependencies
- Total dependency count
- Flag heavy or unusual dependencies

### 2.4 Call-Home Scan (Full Paranoia)

Search the entire codebase for any indication of outbound data transmission that is not core functionality. Check for ALL of the following:

**Dependency-level:**
- Analytics/telemetry SDKs: Sentry, Segment, Mixpanel, PostHog, Amplitude, Google Analytics, New Relic, Datadog, LogRocket, FullStory, Heap, Keen, Plausible, Umami
- Error reporting services: Bugsnag, Rollbar, Airbrake, Honeybadger
- Feature flag services: LaunchDarkly, Split, Optimizely, Statsig

**Code-level:**
- Outbound HTTP/fetch/axios/request calls that are not core functionality
- Version ping or update-check mechanisms
- User agent strings that include tracking identifiers
- Environment variable collection beyond what the tool needs (username, hostname, OS, shell, locale)
- Websocket connections to external services
- DNS/IP lookups that seem unnecessary
- Encoded or obfuscated URLs (base64, hex-encoded strings that decode to URLs)
- Beacon API usage
- Image pixels or tracking pixels

**Keyword search** across all files:
- `telemetry`, `analytics`, `tracking`, `beacon`, `phone-home`, `phon home`
- `metrics`, `usage`, `reporting`, `diagnostics`
- `sentry`, `segment`, `mixpanel`, `amplitude`, `posthog`
- `navigator.sendBeacon`, `pixel`, `img src=` with external URLs
- `update-check`, `update-notifier`, `latest-version`

**Report every finding**, even if it looks benign. Let the user decide what is acceptable.

### 2.5 Bloat Assessment

- Is the scope proportional to what it claims to do?
- Are there unnecessary abstractions or over-engineering?
- How heavy is the dependency tree for what it delivers?
- Are there large vendored files, bundled assets, or checked-in build artifacts?
- Could this be 100 lines instead of 1000?

### 2.6 Compare Against Our Workflow

Using the context from Phase 1.3:
- What does this repo do that we already handle with existing tools, skills, or patterns?
- What does it do that we do NOT currently handle?
- For overlapping functionality, is this implementation better than ours?

### 2.7 Extractable Elements

- Are there standalone functions, utilities, or patterns that are useful without the whole repo?
- Could a single module or file be lifted out and adapted?
- Are there ideas or approaches worth borrowing even if we don't take the code?

### 2.8 Triage Report

Present findings in this format:

```
## Review: <owner>/<repo>

**What it does:** One-line summary
**Stack:** Language, framework, key deps
**Size/Complexity:** LOC, file count, dep count

### New Capabilities (things we don't do)
- ...

### Overlaps (things we already do)
- ...

### Call-Home / Telemetry
- ... (or "None found")

### Bloat Assessment
- ...

### Extractable Elements
- ...

### Recommendation
Adopt / Extract / Skip — with reasoning

### Worth Digging Into?
- Suggested areas (if any)
```

## Phase 3: Deep Dive (optional)

After presenting the triage report, ask:
> "Want me to dig into anything?"

### User-Directed

The user points at specific files, modules, or areas. Read through them and report findings conversationally.

### Recommendation-Directed

Suggest 1-3 areas from the triage that seem worth investigating. Examples:
- "The CLI parser looks clean and portable — worth reading through for extraction"
- "There's a middleware pattern here that differs from ours — want me to trace it?"
- "Found 3 outbound calls that need closer inspection"

Wait for user approval before digging into each area.

### Output

Conversational — findings, observations, relevant code excerpts. No rigid template. This is the "let's talk about what we found" phase.

### Loop

Each deep-dive round can surface new areas. Repeat until the user is satisfied or says we're done.

## Phase 4: Cleanup

When the review is complete, ask:
> "Want me to remove the cloned repo from `~/workspace/tmp/<owner>-<repo>`, or keep it around?"

- If remove: delete the directory
- If keep: leave it, no action

## Rules

1. **Triage before deep dive** — always start with the high-level scan. Never jump to file-by-file reading without the overview first.
2. **Full paranoia is the default** — every outbound call is suspect until proven necessary. Report all findings, even benign-looking ones.
3. **Always confirm** before destructive actions (cloning over existing directory, removing the clone).
4. **In-session only** — no written reports or artifacts saved. Findings are conversational.
5. **Context first** — read `~/.claude/CLAUDE.md` before starting the scan so comparisons against "our workflow" are informed.
6. **One repo at a time** — this skill reviews a single repo per invocation.
7. **No piped commands** — never use `|` in Bash calls. Use the Grep tool for searching, the Read tool for reading files, and write to temp files if you need to combine output. Run one operation per Bash call.
