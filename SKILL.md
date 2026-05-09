---
name: agent-tact
description: >
  Teaches agents OS platform awareness (Windows vs Linux shell/path conventions)
  and social restraint around git operations. Avoids wrong-command retries and
  premature commit/push prompts.
triggers:
  - platform
  - windows
  - linux
  - shell
  - path
  - separator
  - commit
  - push
  - git
  - cross-platform
  - os
---

## Purpose

Reduce wasted tokens from two common agent mistakes:

1. **Wrong-platform commands** — assuming the wrong shell, path separator, or binary name
2. **Premature git prompts** — suggesting commit/push before the user has confirmed things work

## Rule 1: Detect Platform at Session Start

Before running any shell command, determine the OS:

| Platform | Shell | Path separator | String escaping |
|----------|-------|---------------|-----------------|
| Windows  | `powershell` or `cmd` | `\` | Use `\\` in code strings |
| Linux    | `bash` | `/` | No special escaping needed |

**Do:**
- Check `uname -s` or equivalent at conversation start to confirm platform
- Use the correct shell for the detected platform
- On Windows: use `python` (not `python3`); if it fails, try `python.exe`

**Don't:**
- Assume Linux/bash by default (~50% failure rate on Windows)
- Write `\` in Windows paths without escaping in string literals

## Rule 2: Adapt Commands to Platform

| Action | Linux | Windows |
|--------|-------|---------|
| Run Python | `python3 script.py` | `python script.py` (or `python.exe`) |
| Environment vars | `export VAR=val` | `$env:VAR = "val"` (PowerShell) |
| Path in code | `"path/to/file"` | `"path\\to\\file"` |
| List directory | `ls` | `ls` (PowerShell) or `dir` (cmd) |

## Rule 3: Don't Rush Git Operations

After modifying code, do NOT proactively suggest commit, push, or PR creation.

The user may be testing the changes. Wait for them to indicate success.
Only ask about committing if the user's message clearly signals they are done
(e.g. "it works", "ship it", "all good").

**Do:**
- Keep changes local until the user indicates readiness

**Don't:**
- "Shall I commit these changes?" unprompted after every edit
- Suggest push or PR creation unless the user brought it up
- Create commits without explicit user request

## Rule 4: Path Separator Awareness

When writing paths in code or shell commands:
- **Windows**: use `\\` in string literals (e.g. `"C:\\Users\\name"`)
- **Linux**: use `/` (e.g. `"/home/user"`)
- **Git Bash on Windows**: uses `/` as path separator, but targets the Windows filesystem

## Examples

**Wrong:**
```
Agent runs `python3 script.py` → error on Windows
Agent writes `"C:\Users\zt\file"` → unicode escape issues
Agent: "I've made the changes, shall I commit them?" → user hasn't tested yet
```

**Right:**
```
Agent detects Windows → uses `python script.py`
Agent writes `"C:\\Users\\zt\\file"` → correct escaping
Agent makes changes, stays quiet → user tests and says "works", then agent asks
```
