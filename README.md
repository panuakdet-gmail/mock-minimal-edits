# minimal-edits

A skill for [Claude Code](https://claude.com/claude-code) that makes it build **exactly what you asked for, at the simplest fidelity that satisfies it** — and then stop.

Ask for a countdown timer in the window, and you get `05:03` drawn in a corner. You do not get a clock widget with themes, plugins, a settings pane, and forty minutes of questions about the font.

The second half matters as much as the first. Left alone, a coding assistant will ask you where the timer goes, what colour it should be, whether it should pause on blur, and what happens at zero — three hundred small questions about details that no user will ever notice. This skill tells it to guess. If it can work out your likely answer with better than 60% confidence, it picks the sensible option, does the work, and tells you in one line what it assumed. Reasonable-and-changeable beats perfect-and-blocked.

It is not a licence to be sloppy. Tests still get written, existing conventions still get followed, the suite still gets run. Think of it as *a hack, but better* — economical with your time, still holding the line on correctness.

> **Claude Code** is Anthropic's coding assistant that runs in your terminal. A **skill** is a set of instructions you can hand it on demand, by typing a `/` command.

## The skill

| Command | What it does |
|---|---|
| `/mock-minimal-edits` | Applies the minimal-edits discipline to whatever you are building next: smallest correct diff, no speculative abstraction, no drive-by refactors, and questions only when guessing wrong would be genuinely costly. |

It is **manual only**. It will not switch itself on during ordinary work — you decide when a task deserves it.

## What it actually changes

Eight rules, in short:

1. Build what was asked, at the simplest fidelity that satisfies it.
2. Prefer editing something that exists over adding something new.
3. No speculative generality — the second caller earns the abstraction, not the first.
4. Don't touch what you weren't asked to touch. Note it instead.
5. Follow the code that's already there, even where you'd have written it differently.
6. Keep the diff reviewable. A growing diff means you drifted into building a system.
7. Still do the engineering — tests, real error cases, run the suite.
8. Handle real cases, skip imagined ones.

Plus the **60% rule**: guess rather than ask, unless the answer changes the shape of the work *and* guessing wrong throws the work away.

And a fixed report at the end — what changed, how it was verified, what was assumed, what was deliberately left undone. No victory lap.

## What you need

- [Claude Code](https://claude.com/claude-code) — Anthropic's assistant for your terminal. Install it first; nothing here works without it.

That's the whole list. No other tools, no accounts, no configuration.

## Installing

### Option 1 — ask Claude Code to do it

The easiest route, and it needs no terminal knowledge at all. Open Claude Code in any folder and paste this:

```
Install the Claude Code skill from https://github.com/panuakdet-gmail/mock-minimal-edits
by adding it as a plugin marketplace, then install the "minimal-edits" plugin from it.
```

Claude Code will ask your permission before it changes anything.

### Option 2 — the built-in commands

Inside Claude Code, run these two:

```
/plugin marketplace add panuakdet-gmail/mock-minimal-edits
/plugin install minimal-edits@minimal-edits-skills
```

The `plugin@marketplace` form is how Claude Code names a plugin: `minimal-edits` is the plugin, `minimal-edits-skills` is the collection it came from.

### Option 3 — by hand

Copy the skill folder into your personal skills directory:

```bash
git clone https://github.com/panuakdet-gmail/mock-minimal-edits.git
cp -R mock-minimal-edits/skills/mock-minimal-edits ~/.claude/skills/
```

Skills are read when a session starts, so restart Claude Code afterwards.

## Using it

Type the command, then describe the work:

```
/mock-minimal-edits add a countdown timer to the editor window
```

Before touching anything it tells you, in two or three lines, what it plans to change and what it guessed instead of asking:

```
Changing: src/editor/StatusBar.tsx, plus a test.
Approach: format the remaining seconds as mm:ss and render it in the
existing status bar's right slot — no new component.
Assumed: bottom-right, same muted text colour as the line/column readout.
```

Then it builds it, and closes with:

```
What changed
  src/editor/StatusBar.tsx  — countdown readout in the right slot
  src/editor/StatusBar.test.tsx — formatting at 0s, 63s, and 3600s

How it was verified
  Added 3 tests, all passing. Full suite: 214 passed, 0 failed.

Assumed
  Bottom-right placement; counts down to 00:00 and stops there.

Left undone
  StatusBar re-renders once a second now. Fine at this size — say so if
  you want it memoised.
```

If you disagree with a guess, say so and it changes — that costs one sentence, where answering the question up front would have cost the whole conversation.

## When not to use it

When the small version would actually be wrong. The skill has an escape hatch for this: if the minimal change leaves a real correctness, data-loss, or security problem, it says so and does the version that isn't broken. But if you are designing a system meant to be extended from day one, don't reach for this — you want the general solution, and this skill is built to talk you out of it.

## Related work

[andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) by forrestchang covers adjacent ground — its *Simplicity First* and *Surgical Changes* sections land on several of the same points, and it is derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on where LLMs go wrong when coding.

This skill was written independently, from its author's own notes, and shares no text with it. Mock, the author, would guess that Karpathy's version is probably the better general-purpose choice for most people — it is broader, better known, and pitched at everyday coding. This one is narrower and more opinionated, and exists because in some situations that suits him better.

The one place they genuinely disagree is worth knowing before you pick. Where that skill says to stop and ask when something is unclear, this one says the opposite: guess when you are better than 60% sure, do the work, and state the assumption. If constant clarifying questions are what wear you down, this is the one for you. If you would rather be consulted, take theirs.

## Licence

MIT — see [LICENSE](LICENSE). It covers the text and code in this repository. It says nothing about the ideas the skill implements; "change as little as possible" is older than all of us.
