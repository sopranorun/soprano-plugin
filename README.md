# Soprano plugin

Soprano inside your Claude Code (ADR-0040): see your queue, claim work with
your role's contract loaded, record briefs/proposals/plans — while gates,
approvals and trails stay in the Soprano cloud.

## Install

No token to mint or export — identity is born in a browser handshake
(ADR-0046); the bundled `.mcp.json` carries no secret. It points at
`https://app.soprano.run/api/mcp` unless `SOPRANO_MCP_URL` is set in the
environment Claude Code starts from — a developer's local server, say.

Install it ONCE, at user scope, so every session on this machine carries it —
a session opened from a link or a plain `claude` in any directory included.
The marketplace is the public mirror `sopranorun/soprano-plugin`; the source
lives in the Soprano monorepo and is published there on every merge.

```
claude plugin marketplace add sopranorun/soprano-plugin
claude plugin install soprano@soprano
```

Then call any `soprano` tool (or `/soprano:queue`). The first one opens a
browser: approve once and the connection is recorded. Updates:
`claude plugin marketplace update soprano && claude plugin update soprano@soprano`
(restart the session or run `/reload-plugins` to apply). A release is a version bump in
`.claude-plugin/plugin.json` — without it nobody receives the update.

**Working on the plugin itself?** Point a skills-directory plugin at the
checkout and edit live — it loads as `soprano@skills-dir` in every session:

```
ln -s "$(pwd)/packages/soprano-plugin" ~/.claude/skills/soprano
```

An installed `soprano@soprano` takes precedence over the symlink; keep one.
The plugin brings its own `soprano` MCP server — a separate `claude mcp add`
entry for it (user or project scope) makes two servers with the same tools
and two logins; remove it: `claude mcp remove soprano`.
`claude --plugin-dir <path>` is session-only — a one-off test, never an
install: a session opened any other way (a link, a plain `claude`) does not
carry it, and `/soprano:*` reads "No commands match".

Revoke it any time from **app.soprano.run → Ayarlar → Bağlantılar**; the
engine is cut off at its next call and reconnecting is one more approval.

## Commands

| Skill | What it does |
|---|---|
| `/soprano:queue` | Your work across the studio's projects, grouped by role |
| `/soprano:start <n> [repo]` | Shows the issue and asks once, then claims: worktree, branch, role contract, work, PR |
| `/soprano:rework <n> [repo]` | Continue on the same branch with the review feedback |
| `/soprano:brief [customer \| customer:<id> \| project:<id>]` | Digest raw input into the work's five-heading living summary — a customer's or an internal project's |
| `/soprano:proposal [customer \| customer:<id>]` | Ask round A's price-changing questions, then draft a phased proposal from the brief — pricing stays yours |
| `/soprano:discovery` | Discovery: the pending-item package in both kinds of project; round B from the scope draft's open questions in customer projects |
| `/soprano:scope [repo]` | Write SCOPE as a draft PR — from the brief, the proposal and discovery's answers; offered ready when nothing is open — an internal project settles its open questions in the same conversation; a sealed scope changes as its next version |
| `/soprano:breakdown` | Break a milestone into tasks — parks at the plan gate |
| `/soprano:triage [request \| request:<id>] [repo]` | Evaluate a customer request against the scope — parks at the request gate |

The repo argument is optional and last: the skill resolves it from wherever you
are standing (`git remote get-url origin`), and asks only when the directory has
no origin or the argument contradicts it.

Output language follows the project's communication language; skill names and
bodies are English keys (ADR-0017).
