---
name: checkup
description: Use when wanting a periodic health check of Claude Code setup — analyzes actual usage, config, and session history to surface dead weight, config friction, missed features, and actionable improvements
allowed-tools: Read, Glob, Grep, Bash, WebFetch, WebSearch, Agent
effort: high
---

# Checkup

ultrathink

You are running a comprehensive Claude Code audit. The goal is to help the user discover and use every Claude Code feature relevant to how they actually work — not just recent additions, but the full capability surface. A user who types /checkup wants a periodic health check — actionable findings, not a feature catalog.

The output has two parts:
1. **The full picture** — here is everything Claude Code can do, organized by category, with your current adoption status next to each
2. **Personalized action items** — here is what you specifically should do, backed by evidence from your actual sessions, config, and codebase

---

## ANTI-HALLUCINATION RULE

Every feature citation must quote exact text from a source you fetched. Never invent version numbers, dates, or feature descriptions. If you cannot find a real source for a claim, write: `[no source found — inferred from analysis]`. This rule applies to every single item in your output.

---

## Step 1 — Gather context

Run these as separate commands (one per Bash call, no pipes or chains):

1. Get the current working directory:
```bash
pwd
```

2. Get the Claude Code version:
```bash
claude --version
```

3. Find the sessions directory. Check the projects directory for a folder matching the current working directory path:
```bash
ls "$HOME/.claude/projects/"
```
Then use the CWD to identify the matching sessions directory (the path with `/` replaced by `-`). Confirm it exists by listing the directory:
```bash
ls "$HOME/.claude/projects/<matched-dir>/"
```
**Note:** There is no `sessions-index.json`. Sessions are stored as individual `.jsonl` files named by UUID. The sessions directory contains `*.jsonl` files (conversation data), UUID-named subdirectories (with `subagents/` inside), and a `memory/` directory.

4. Detect workspace mode — if the CWD contains multiple subdirectories with their own `manifest.json` or `CLAUDE.md` files, this is a multi-project workspace, not a single project:
```bash
ls */manifest.json */CLAUDE.md 2>/dev/null
```

Record:
- **SESSIONS_DIR**: the full path to the sessions directory
- **PROJECT_CWD**: the current working directory
- **WORKSPACE_MODE**: `true` if multiple project subdirectories detected, `false` if single project

---

## Step 2 — Web research (parent does this, not agents)

Before launching agents, attempt to gather changelog/release data yourself. Subagents often cannot access WebFetch or WebSearch due to tool availability and permission constraints.

Try these in order (stop at first success):
1. WebSearch for "Claude Code changelog" or "Claude Code release notes 2026"
2. WebFetch on https://docs.anthropic.com/en/docs/claude-code/changelog
3. WebFetch on https://api.github.com/repos/anthropics/claude-code/releases?per_page=20

If any succeed, save the results to embed in Agent A's prompt as `{{WEB_RESEARCH_RESULTS}}`.
If all fail, proceed without — Agent A will note the gap and work from local sources.

## Step 3 — Launch three research agents in parallel

**Critical**: spawn all three agents in a single message so they run simultaneously. **Use `model: "sonnet"` for all three agents** — these are research/data-gathering tasks that don't need Opus. The parent session (Opus) handles the synthesis in Steps 4-5.

Embed the actual SESSIONS_DIR, PROJECT_CWD, WORKSPACE_MODE values, and any web research results into the agent prompts before launching.

---

### Agent A — Full Feature Researcher

This agent builds the feature inventory using a **local-first strategy** — reading installed plugin files, CLI help output, and local documentation. Web research supplements but is not required.

**Before launching Agent A**: The parent (you) should attempt web research to get recent changelog data. Try WebSearch or WebFetch for "Claude Code changelog" or "Claude Code release notes". If successful, include the results in Agent A's prompt under `## Web Research Results`. If web tools are unavailable or blocked, launch Agent A anyway — it will work from local sources.

```
You are researching the complete Claude Code feature set to build an exhaustive inventory.

**Research strategy — local first:**

Do NOT attempt WebFetch or WebSearch — those tools may not be available to you. Instead:

1. Run `claude --help` as a Bash command to get all CLI flags and subcommands
2. Use Glob to find installed plugin documentation:
   - ~/.claude/plugins/cache/**/*.md — plugin docs, READMEs, command files
   - ~/.claude/plugins/cache/**/SKILL.md — all installed skills and their frontmatter
   - ~/.claude/plugins/cache/**/commands/*.md — all commands from plugins
   - ~/.claude/plugins/marketplaces/**/plugins/*/README.md — marketplace plugin docs
3. Read a sample of discovered files to understand what each plugin/skill/command does
4. Use your training knowledge for features you cannot discover locally — mark these with `[from training knowledge, verify against current version]`

{{WEB_RESEARCH_RESULTS}}

From all sources, build a COMPLETE feature inventory organized into these categories. For each item, include: feature name, what it does (1 sentence), how to enable it, and the source (local file path, CLI output, or training knowledge).

CATEGORIES TO COVER (be exhaustive within each):

**A. Built-in slash commands**
List every /command. Discover from plugin command files and `claude --help`. Include built-ins: /help, /compact, /clear, /exit, /resume, /config, /memory, /permissions, /status, /doctor, /login, /logout, etc.

**B. Hook events**
List every hook event type (PreToolUse, PostToolUse, PostToolUseFailure, UserPromptSubmit, Stop, SubagentStop, PostCompact, TaskCreated, WorktreeCreate, WorktreeRemove, CwdChanged, FileChanged, StopFailure, SessionStart, SessionEnd, InstructionsLoaded, ConfigChange, ElicitationResult, PermissionDenied, Notification, etc.)
For each: what triggers it, what data it receives, what it can return.

**C. MCP capabilities**
What MCP servers can do, how to configure them, OAuth support, transport types (stdio, HTTP, SSE), etc.

**D. Skill system**
All frontmatter fields available in SKILL.md. Read actual SKILL.md files from installed plugins to discover real fields in use. What skills can do that plain commands can't.

**E. Agent/subagent system**
Custom agents in .claude/agents/, frontmatter fields, background agents, worktree isolation, the Agent tool, SendMessage, subagent_type parameter, etc.

**F. CLI flags and modes**
Every flag from `claude --help`. Special modes: auto mode, bypass-permissions, accept-edits, plan mode, fast mode.

**G. Settings and configuration**
Every meaningful setting: permissions, hooks, MCP, model, statusLine, env, enabledPlugins, experimental.

**H. Keyboard shortcuts**
All default shortcuts and any that are rebindable.

**I. Recent launches (last 60 days)**
If web research results were provided above, use them. Otherwise, note the installed version and mark this section `[requires web research for completeness — parent should provide changelog data]`.

**J. Status line**
What statusLine supports, JSON schema, refreshInterval, rate_limits field, etc.

**K. Memory system**
Auto-memory, /memory command, MEMORY.md, custom memory directory, project-scoped memory, etc.

Return the COMPLETE inventory. Do not summarize or truncate. For each item, cite your source.
```

---

### Agent B — Deep Usage Analyst

```
You are doing a deep analysis of Claude Code usage history for this project.

Sessions directory: {{SESSIONS_DIR}}
Working directory: {{PROJECT_CWD}}
Workspace mode: {{WORKSPACE_MODE}}

**IMPORTANT: JSONL format notes**
- There is NO sessions-index.json. Sessions are individual .jsonl files named by UUID.
- Each line is a JSON object with fields: type, message, timestamp, sessionId, version, gitBranch, cwd, uuid
- Tool usage appears in assistant messages: message.content[] has objects with type="tool_use" and a "name" field
- User messages have type="user" at the top level, with message.content (string or array of {type:"text", text:"..."})
- Subagent data lives in <session-uuid>/subagents/*.jsonl subdirectories — skip these for main analysis
- Error indicators: "is_error" field in tool results, or error strings in content

Perform ALL of the following analyses. Use Read, Grep, Glob, and Bash tools as appropriate. For Bash, run one command per call — no pipes, no && chains, no $() substitution.

**B1. Session overview**
Use Glob to find all top-level .jsonl files in {{SESSIONS_DIR}} (pattern: *.jsonl, NOT recursive into subdirs). Then run this Python script to build a session summary from the JSONL files themselves:
```bash
python3 <<'PYEOF'
import json, os, glob
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)
print(f"Total sessions: {len(files)}")
print(f"Showing most recent 25:\n")
for f in files[:25]:
    sid = os.path.basename(f).replace(".jsonl", "")
    first_ts = last_ts = branch = version = "?"
    msg_count = 0
    first_prompt = ""
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            ts = obj.get("timestamp", "")
            if ts and first_ts == "?":
                first_ts = ts
            if ts:
                last_ts = ts
            if obj.get("gitBranch"):
                branch = obj["gitBranch"]
            if obj.get("version"):
                version = obj["version"]
            if obj.get("type") == "user":
                msg_count += 1
                if not first_prompt:
                    msg = obj.get("message", {})
                    content = msg.get("content", "") if isinstance(msg, dict) else ""
                    if isinstance(content, list):
                        texts = [b.get("text", "") for b in content if isinstance(b, dict) and b.get("type") == "text"]
                        content = " ".join(texts)
                    t = str(content).strip()
                    if t and not t.startswith("<") and len(t) > 5:
                        first_prompt = t[:100]
        except:
            pass
    date = first_ts[:10] if first_ts != "?" else "?"
    print(f"{date} | {msg_count:>4}msg | {branch} | v{version}")
    print(f"  -> {first_prompt}")
    print()
PYEOF
```

**B2. Tool call frequency**
```bash
python3 <<'PYEOF'
import json, os, glob
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)[:25]
tools = Counter()
for f in files:
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if not isinstance(obj, dict):
                continue
            msg = obj.get("message", {})
            if not isinstance(msg, dict):
                continue
            content = msg.get("content", [])
            if isinstance(content, list):
                for block in content:
                    if isinstance(block, dict) and block.get("type") == "tool_use":
                        name = block.get("name", "")
                        if name:
                            tools[name] += 1
        except:
            pass
for tool, count in tools.most_common(40):
    print(f"{count:>6}  {tool}")
PYEOF
```

**B3. MCP tool usage**
```bash
python3 <<'PYEOF'
import json, os, glob
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)[:25]
mcp = Counter()
for f in files:
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            msg = obj.get("message", {})
            if not isinstance(msg, dict):
                continue
            content = msg.get("content", [])
            if isinstance(content, list):
                for block in content:
                    if isinstance(block, dict) and block.get("type") == "tool_use":
                        name = block.get("name", "")
                        if name.startswith("mcp__"):
                            mcp[name] += 1
        except:
            pass
for tool, count in mcp.most_common():
    print(f"{count:>6}  {tool}")
if not mcp:
    print("No MCP tool usage found")
PYEOF
```

**B4. Slash commands used**
```bash
python3 <<'PYEOF'
import json, os, glob, re
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)[:25]
cmds = Counter()
for f in files:
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if obj.get("type") != "user":
                continue
            msg = obj.get("message", {})
            if not isinstance(msg, dict):
                continue
            content = msg.get("content", "")
            if isinstance(content, list):
                content = " ".join(b.get("text", "") for b in content if isinstance(b, dict))
            for m in re.findall(r'<command-name>(/[a-z][a-z0-9_:/-]*)</command-name>', str(content)):
                cmds[m] += 1
            for m in re.findall(r'(?<!\w)(/[a-z][a-z0-9_:/-]+)', str(content)):
                if not m.startswith("/Users") and not m.startswith("/tmp") and not m.startswith("/var"):
                    cmds[m] += 1
        except:
            pass
for cmd, count in cmds.most_common(30):
    print(f"{count:>6}  {cmd}")
if not cmds:
    print("No slash commands found")
PYEOF
```

**B5. Repeated user prompts (pattern mining)**
```bash
python3 <<'PYEOF'
import json, os, glob
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)[:25]
prompts = Counter()
for f in files:
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if obj.get("type") != "user":
                continue
            msg = obj.get("message", {})
            if not isinstance(msg, dict):
                continue
            content = msg.get("content", "")
            if isinstance(content, list):
                texts = [b.get("text", "") for b in content if isinstance(b, dict) and b.get("type") == "text"]
                content = " ".join(texts)
            t = str(content).strip()
            if 15 < len(t) < 150 and not t.startswith("<") and not t.startswith("{"):
                prompts[t] += 1
        except:
            pass
for prompt, count in prompts.most_common(40):
    if count > 1:
        print(f"{count:>4}x  {prompt}")
PYEOF
```

**B6. Error patterns**
Use Grep to search the JSONL files for error indicators. Then run:
```bash
python3 <<'PYEOF'
import json, os, glob
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)[:25]
errors = Counter()
error_count = 0
patterns = ["String to replace not found", "permission denied", "requires permission",
            "not allowed", "command not found", "ENOENT", "EACCES", "Hook PreToolUse",
            "hook denied", "403", "404", "500"]
for f in files:
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if isinstance(obj, dict) and obj.get("is_error"):
                error_count += 1
            for p in patterns:
                if p.lower() in line.lower():
                    errors[p] += 1
        except:
            pass
print(f"Total is_error: {error_count}")
print()
for err, count in errors.most_common():
    print(f"{count:>6}  {err}")
PYEOF
```

**B7. Session depth distribution**
```bash
python3 <<'PYEOF'
import json, os, glob
sessions_dir = "{{SESSIONS_DIR}}"
files = sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl")), key=os.path.getmtime, reverse=True)
counts = []
for f in files:
    msg_count = 0
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if obj.get("type") in ("user", "assistant"):
                msg_count += 1
        except:
            pass
    counts.append(msg_count)
counts.sort(reverse=True)
print(f"All sessions by depth (top 30): {counts[:30]}")
avg = round(sum(counts) / len(counts), 1) if counts else 0
print(f"Average: {avg}")
print(f"Max: {max(counts) if counts else 0}")
over30 = len([c for c in counts if c > 30])
over60 = len([c for c in counts if c > 60])
print(f"Sessions > 30 msgs: {over30}")
print(f"Sessions > 60 msgs (autocompact risk): {over60}")
PYEOF
```

**B8. Git branch patterns from session history**
```bash
python3 <<'PYEOF'
import json, os, glob
from collections import Counter
sessions_dir = "{{SESSIONS_DIR}}"
files = glob.glob(os.path.join(sessions_dir, "*.jsonl"))
branches = Counter()
for f in files:
    branch = None
    for line in open(f, errors="replace"):
        try:
            obj = json.loads(line)
            if obj.get("gitBranch"):
                branch = obj["gitBranch"]
                break
        except:
            pass
    branches[branch or "unknown"] += 1
for b, count in branches.most_common():
    print(f"{count:>4}x {b}")
PYEOF
```

**B9. Recent session summaries**
Read the 10 most recent JSONL files (by modification time). For each, extract:
- The first user prompt (first line where type="user" and message.content is not a system tag)
- The session date range (first and last timestamp)
- The git branch
- A count of user messages vs assistant messages
Present as a readable summary of recent activity.

**B10. Tool use interrupts**
Use Grep to search the top-level JSONL files for `"interrupted"` and `"cancelled"` patterns. Report the count.

If workspace mode is true: note that these sessions cover workspace-level activity spanning multiple projects. Call this out in your summary.

Return ALL raw data. Do not truncate output. The caller needs the full picture to make recommendations.
```

---

### Agent C — Config and Project Auditor

```
You are doing a complete audit of a Claude Code configuration and project structure.

Working directory: {{PROJECT_CWD}}
Workspace mode: {{WORKSPACE_MODE}}

Use Read, Glob, Grep, and Bash tools directly — do NOT use shell pipes, &&, or $(). One command per Bash call.

**C1. Global Claude configuration**
- Read ~/.claude/CLAUDE.md — report line count and full content
- Read ~/.claude/settings.json — full content
- Read ~/.claude/settings.local.json — full content (if exists)
- Use Glob for ~/.claude/skills/*/SKILL.md — for each, read the frontmatter (first 10 lines)
- Use Glob for ~/.claude/commands/*.md — list all command names
- Use Glob for ~/.claude/agents/*.md — list all agent names, read frontmatter of each
- Use Glob for ~/.claude/plugins/**/* — list any installed plugins

**C2. Project-level Claude configuration**
- Read {{PROJECT_CWD}}/CLAUDE.md — full content and line count
- Read {{PROJECT_CWD}}/.claude/settings.json if it exists
- Read {{PROJECT_CWD}}/.claude/settings.local.json if it exists
- Use Glob for {{PROJECT_CWD}}/.claude/skills/*/SKILL.md — list all
- Use Glob for {{PROJECT_CWD}}/.claude/commands/*.md — list all
- Use Glob for {{PROJECT_CWD}}/.claude/agents/*.md — list all
- Read {{PROJECT_CWD}}/.mcp.json if it exists

**C3. Auto-memory files**
- Use Glob to find all files in ~/.claude/projects/*/memory/
- For the directory matching this project, read every memory file in full
- These are crucial for understanding what the project is about

**C4. Project stack detection**
If workspace mode is false (single project):
- Read package.json if it exists — show name, version, scripts, dependencies
- Use Glob for .github/workflows/*.yml — list CI workflows
- Use Glob for prisma/ or migrations/ directories
- Use Glob for **/*.test.* or **/*.spec.* — count test files
- List src/ directory contents

If workspace mode is true (multi-project workspace):
- List all subdirectories that contain a manifest.json or CLAUDE.md
- For each project subdirectory, read its manifest.json (if present) to get component list and stack info
- Report the workspace layout: which projects exist, their components, their stacks
- Read the workspace-level CLAUDE.md for conventions

**C5. MCP configuration detail**
From the settings files read in C1 and C2, extract all MCP server configurations. For each: name, type, command/url, and config options.

**C6. Hook configuration detail**
From the settings files read in C1 and C2, extract every hook: event type, matcher, command, and whether global or project-level.

**C7. Current git state**
Run these as separate Bash commands (one per call):
```bash
git branch --show-current
```
```bash
git log --oneline -10
```
```bash
git remote -v
```

If workspace mode is true, also check git state of a few subdirectory repos:
- Use Glob for */. git or look for subdirectories that are git repos
- Report which subdirectories are their own git repos vs part of the parent

Return EVERYTHING. Full file contents, not summaries. The caller needs the raw data.
```

---

## Step 4 — Synthesize all three agent results

After all three agents complete, you have:
- **A**: Complete feature inventory from official docs + changelog
- **B**: Deep usage profile from JSONL history
- **C**: Full config + project context

Now cross-reference everything and produce the report below.

---

## Step 5 — Produce the report (tiered output)

The report has two tiers. The terminal summary is what the user reads. The detail file is reference material they can consult later.

### Tier 1: Terminal Summary (print directly to the user)

This is what you output to the conversation. **Keep it under 60 lines.** Structure:

```
## Catch-Up Audit — {date}

**Profile**: v{version} | {N} sessions analyzed | avg {N} msgs/session | {N} plugins | {N} hooks

### Dead Weight — Remove Now
{Numbered list of things configured but producing zero value. For each: what it is, why it's dead, the exact command to remove it. Max 5-8 items.}

### Config Fixes
{Numbered list of settings changes that reduce friction. For each: what to change, the config snippet or command, why. Max 5-8 items.}

### New Features Worth Trying
{Bulleted list of recent launches (last 60 days) that are relevant to this user's workflow. Only include items that genuinely apply — skip the rest. For each: feature name, one sentence on what it does, how to enable. Max 5-8 items.}

### Workflow Gaps
{Bulleted list of automation opportunities or CLAUDE.md gaps found from usage patterns. Max 3-5 items.}

---
Full detail report saved to: {path to detail file}
Reply with item numbers to execute now.
```

**Filtering rules for Tier 1:**
- Only include items with specific evidence from this project's usage, config, or session history
- No generic advice. If you can't cite a session count, error pattern, or config entry, don't include it.
- No feature catalog. Don't list things just because they exist.
- Every item must answer: "what should this user DO differently?"

### Tier 2: Detail File (saved to disk)

Use the Write tool to save the full detailed report to `{PROJECT_CWD}/tmp/checkup-report.md`. This file contains:

1. **Profile dashboard** — version, session stats, tool breakdown, error patterns, full plugin/hook/MCP inventory
2. **Feature adoption matrix** — every feature with status (Used / Configured / Not set up / Dead weight / N/A). Organize by category. Include notes for unused features that would help this project.
3. **Recent launches** — all features shipped in the last 60 days with applicability assessment
4. **Dead weight detail** — full evidence for each removal recommendation
5. **Automation opportunities** — repeated prompts, workflow patterns, suggested implementations
6. **CLAUDE.md gaps** — line count, missing conventions, memory items that should graduate
7. **Config improvements** — full config snippets with before/after
8. **Complete action item list** — every recommendation numbered and ranked

The detail file CAN be long. It's reference material, not something the user reads end-to-end.

## Output rules

- **Terminal output is concise**. If you can say it in one line, don't use three.
- **Detail file is thorough**. Include all the data, tables, and evidence there.
- **No generic advice anywhere**. Every item must cite specific evidence from this project.
- **End terminal output with**: "Full report: `tmp/checkup-report.md` — Reply with item numbers to execute now."
