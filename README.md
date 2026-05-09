# agent-tact

Teaches AI coding agents OS platform awareness and social restraint around git operations. Reduces wasted tokens by preventing wrong-command errors and premature prompts.

## How It Saves Tokens

The core insight: **every wrong-command retry costs a round-trip**. Without platform awareness:

1. `python3 script.py` → error on Windows → retry with `python` → **2 calls instead of 1**
2. `"C:\Users\name"` → unicode escape error → fix to `"C:\\Users\\name"` → retry
3. Wrong path separator → file not found → retry with correct slash

Each of these is an extra API round-trip — hundreds of tokens per retry. Over a session they add up fast.

The git rule saves smaller but more frequent waste: every unprompted "shall I commit?" is a wasted exchange the user must dismiss.

**principle: prevent errors, don't recover from them.**

## Rules

1. **Detect platform at session start** — Windows vs Linux, pick the right shell and conventions
2. **Adapt commands** — `python` not `python3` on Windows, correct env var syntax, etc.
3. **No premature git prompts** — wait for user to signal readiness before asking about commits
4. **Path separator awareness** — `\\` on Windows, `/` on Linux

## Installation

### Global install (recommended)

Installs to user-level skills directory, active across all projects:

```bash
npx skills add Martlet-Tech/agent-tact@agent-tact -y
```

### Manual install

Clone or symlink into your Claude Code user skills directory:

```bash
# Clone
git clone https://github.com/Martlet-Tech/agent-tact.git ~/.claude/skills/agent-tact

# Or symlink (Windows needs admin or developer mode)
# From ~/.claude/skills/: mklink /D agent-tact D:\path\to\agent-tact
```

The skill is loaded by default — no extra config needed.
