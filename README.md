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

Options 1 to 3 are for Claude Code. Option 4 is for other agents such as Codex or Gemini CLI. Pick one.

### Option 1 — ask Claude Code to do it

This is the easiest way, and you do not need a terminal. Open Claude Code and paste this:

```
Please install the Claude Code skills from https://github.com/panuakdet-gmail/mock-minimal-edits.

1. Download the repository, with its full history, to a temporary folder.
2. For each folder inside its skills/ folder, look for a folder with the same name in ~/.claude/skills/.
   - If there is none, copy the folder there.
   - If there is one, and every file in it matches a version that was once published in this repository, replace it with the new version.
   - If there is one, and it matches no published version, I have edited it. First move my copy to ~/.claude/skill-backups/<folder name>-<today's date>/. Then install the new version, show me what I had changed, and offer to merge my changes into it.
3. Delete the temporary download and tell me what you installed or updated.
```

Claude Code asks your permission before it changes anything. When it has finished, restart Claude Code.

**To update later**, paste the same prompt again. If you changed the skill yourself, your version is saved first, and Claude Code offers to merge your changes into the new one.

### Option 2 — install as a plugin

Choose this if you want Claude Code to handle updates for you. Inside Claude Code, run:

```
/plugin marketplace add panuakdet-gmail/mock-minimal-edits
/plugin install minimal-edits@minimal-edits-skills
```

To update later, run `/plugin marketplace update minimal-edits-skills`. Do not also use Option 1, otherwise the skill is installed twice.

### Option 3 — copy the folder yourself

In a terminal:

```bash
git clone https://github.com/panuakdet-gmail/mock-minimal-edits.git
mkdir -p ~/.claude/skills
cp -R mock-minimal-edits/skills/mock-minimal-edits ~/.claude/skills/
```

Then restart Claude Code. To update, delete the downloaded `mock-minimal-edits` folder and run the same commands again. This replaces the installed folder, so any changes you made to it are lost.

### Option 4 — ask another agent to do it

Open Codex, Antigravity, Gemini CLI, or whatever agent you use, and paste this:

```
Please install the skills from https://github.com/panuakdet-gmail/mock-minimal-edits for this agent.

1. Download the repository, with its full history, to a temporary folder.
2. Each folder inside its skills/ folder is one skill. Install each folder whole, including subfolders such as assets/, because the instructions use those files.
3. The skills were written for Claude Code, so adapt only the packaging: register each one the way this agent handles reusable skills or commands, so I can run it by name. Do not change the instructions inside each SKILL.md.
4. Before you install each skill, check whether I already have it.
   - If I do not, install it.
   - If I do, and its files match a version that was once published in this repository, replace it with the new version. Ignore packaging changes that an agent made when it installed the skill.
   - If I do, and it matches no published version, I have edited it. First copy my version to a backup folder outside the place this agent loads skills from, and tell me where it is. Then install the new version, show me what I had changed, and offer to merge my changes into it.
5. Delete the temporary download. Then tell me what you installed or updated, and how to run each skill here.
```

**To update later**, paste the same prompt again.

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
