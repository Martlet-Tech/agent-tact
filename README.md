# agent-tact

**EN** — Teaches AI coding agents OS platform awareness and social restraint around git operations. Reduces wasted tokens by preventing wrong-command errors and premature prompts.

**CN** — 指导 AI 编程助手识别操作系统平台差异(Windows/Linux)，并在 git 操作上保持分寸。通过避免命令用错重试和过早的 commit 提示来节省 token。

---

## How It Saves Tokens / 省 token 原理

**EN** — Every wrong-command retry costs an extra API round-trip (hundreds of tokens). Without platform awareness:

**CN** — 每个错误命令都会触发一次额外的 API 往返（几百 token）。没有平台意识就会：

| Mistake / 错误 | Consequence / 后果 |
|----------------|-------------------|
| `python3 script.py` on Windows | error → retry with `python` → **2 calls instead of 1** |
| `"C:\Users\name"` (unescaped) | unicode escape error → fix to `"\\"` → retry |
| Wrong path separator / 路径分隔符错误 | file not found → retry with correct slash |

**EN** — The git rule saves smaller but more frequent waste: every unprompted "shall I commit?" is a useless exchange.

**CN** — Git 规则节省的是频繁的小浪费：每次没来由的"要我提交吗"都是一个无效对话。

**Core principle / 核心原则：prevent errors, don't recover from them.（预防错误，而不是补救错误。）**

## Rules / 规则

1. **Detect platform at session start / 对话开始时检测平台** — Windows vs Linux, pick the right shell and conventions
2. **Adapt commands / 适配命令** — `python` not `python3` on Windows, correct env var syntax, etc.
3. **No premature git prompts / 不提前催 git** — wait for user to confirm before asking about commit
4. **Path separator awareness / 路径分隔符意识** — `\\` on Windows, `/` on Linux

## Installation / 安装

### Global install (recommended / 推荐)

**EN** — Installs to user-level skills directory, active across all projects:

**CN** — 安装到用户级 skills 目录，所有项目生效：

```bash
npx skills add Martlet-Tech/agent-tact@agent-tact -y
```

### Manual install / 手动安装

**EN** — Clone or symlink into your Claude Code user skills directory:

**CN** — 克隆或软链接到 Claude Code 用户 skills 目录：

```bash
# Clone / 克隆
git clone https://github.com/Martlet-Tech/agent-tact.git ~/.claude/skills/agent-tact

# Or symlink / 或软链接 (Windows needs admin)
# From ~/.claude/skills/: mklink /D agent-tact D:\path\to\agent-tact
```

**EN** — The skill is loaded by default in every session — no extra config needed.

**CN** — 每次对话默认加载此 skill，无需额外配置。
