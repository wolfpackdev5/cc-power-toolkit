# CC Power Toolkit — Prompt Coach

**Write better prompts before you spend a single token.**

A Claude Code plugin that reviews every prompt the instant you submit it — using
fast, local heuristics with **no API call and no added cost** — and gives you
feedback on cost traps and clarity issues *before* the prompt ever reaches Claude.

---

## The problem this solves

- You fire off "rewrite the whole thing" or "read every file" and watch your token
  bill (and your context window) balloon — for a result you didn't even want.
- You paste a wall of text inline, and it gets re-sent on every single turn after.
- You ask a vague "fix this" and burn three round-trips just clarifying what you meant.
- You almost run a `git push --force` you'll regret.

Prompt Coach catches all of these at the moment you hit Enter.

## What Prompt Coach does

It's a single `UserPromptSubmit` hook. When you submit a prompt, it classifies it:

### 🛑 High cost / high risk → paused for revision
The submit is **blocked** and your prompt is handed back with coaching (plus a
copy you can edit), so you can fix it *before* spending tokens or doing something
destructive. Caught patterns:

| Pattern | Why it's flagged |
|---------|------------------|
| **Huge inline paste** (≥ 6,000 chars) | Re-sent every turn — save it to a file and use `@path` |
| **Full rewrite from scratch** | "rewrite the whole codebase…" multiplies cost and regresses working code |
| **Full-repo read** | "read every file…" floods context — name the 2–3 files that matter |
| **Destructive command** | `rm -rf`, `git push --force`, `drop table`, `truncate`, etc. |
| **Unscoped mega-build** | "build a complete production-ready app" with no spec → sprawling, off-target output |

### 💡 Minor clarity issues → advised, but sent anyway
A standout note appears alongside your prompt; the prompt still goes through.
Caught patterns:

| Pattern | The nudge |
|---------|-----------|
| **Vague target** | "fix this" / "make it better" with nothing named |
| **Symptom without details** | "it's broken" with no error text |
| **Many tasks in one prompt** | several bundled asks that blur focus and grow context |

A clean prompt passes silently — no noise.

## Install

This plugin is distributed via a private marketplace for paying customers.

1. Purchase at [gumroad link] — you'll get access to the plugin repository.
2. Add the marketplace, then install.

### Claude Code (CLI)

```
/plugin marketplace add https://github.com/wolfpackdev5/cc-power-toolkit-plugin
/plugin install cc-power-toolkit@cc-power-toolkit-plugin
```

### Claude Code desktop app (Mac / Windows)

Open **Settings → Plugins**, add the marketplace
`https://github.com/wolfpackdev5/cc-power-toolkit-plugin`, then install
**cc-power-toolkit** from the list. Hooks run in the desktop app exactly as they
do in the CLI — same plugin, same behavior. (The desktop plugin UI installs from
marketplaces only, which is how this plugin ships, so you're covered.)

That's it — no setup. Prompt Coach activates on your very next prompt on either surface.

> **Where it runs:** Prompt Coach is a Claude Code *hook*, so it runs anywhere
> Claude Code runs — the CLI, the desktop app, and the VS Code / JetBrains
> extensions (they all share the same config). It does **not** run in the
> standalone Claude chat app (claude.ai or the Claude Desktop chat app), because
> hooks only execute inside Claude Code.

## Using it effectively

**Let it interrupt you — that's the point.** When the coach pauses a prompt, don't
fight it: the 10 seconds you spend scoping "rewrite everything" down to "change
`parseConfig` in `src/config.ts`" is the difference between a cheap, on-target
edit and an expensive, sprawling one.

**Turn vague into specific.** The coach is teaching a habit. Instead of:
- ❌ "fix the login, it's broken"
- ✅ "login throws `TypeError: cannot read 'token'` in `auth.ts:42` on submit — expected a redirect to /dashboard"

**Reference files, don't paste them.** When you're tempted to paste a big file,
use `@path/to/file` instead — Claude reads it on demand instead of carrying it in
context every turn.

**Split bundled work.** If you catch the "many tasks" note, send each task as its
own prompt. Sharper focus, smaller context, cheaper turns.

### Bypass (when you mean it)

Include the token `[[send]]` anywhere in a prompt to skip all checks for that one
submit — useful when you genuinely do want the big operation.

### Configuration

Tune the coach in natural language (ask Claude to "configure the prompt coach")
or edit `~/.cc-power-toolkit/config.json` directly:

```json
{
  "prompt_coach": {
    "enabled": true,
    "block_high_risk": true,
    "advise_minor": true,
    "block_char_threshold": 6000
  }
}
```

| Key | Default | Meaning |
|-----|---------|---------|
| `enabled` | `true` | Master switch for the coach |
| `block_high_risk` | `true` | Block high cost/risk prompts. Set `false` to only warn |
| `advise_minor` | `true` | Show non-blocking clarity notes |
| `block_char_threshold` | `6000` | Prompt length (chars) treated as a huge paste |

## A note on "real time"

Claude Code hooks fire on **submit**, not on every keystroke — there's no
character-by-character event exposed to plugins. Prompt Coach therefore reviews
each prompt the instant you submit it, before it reaches Claude. That's the
earliest point a plugin can intervene, and it's where the cost decision is made.

## Price

$29 one-time. No subscription. Free updates.

[Buy now →](your-gumroad-link)

---

Built for developers who use Claude Code seriously.
