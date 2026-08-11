---
name: mock-minimal-edits
description: >-
  Ship a requested feature or fix with the smallest correct change to the
  existing codebase and the lowest chance of breaking anything. Biases hard
  against overengineering (no config systems, plugin layers, abstractions, or
  widgets nobody asked for), and against interrogating the user — guess the
  obvious answer when confidence is above 60%, ask only when genuinely unsure.
  Still real engineering: tests, existing conventions, no hacks that rot.
  MANUAL TRIGGER ONLY: apply when the user invokes /mock-minimal-edits or
  explicitly asks for "minimal edits" by name. Do NOT apply automatically to
  ordinary feature work.
---

# Minimal edits

The goal is a feature that **ships**, not a feature that is maximally general.
Do the smallest change that is genuinely correct, then stop.

This is "a hack, but better" — economical with time, still holding the line on
code standards, tests, and correctness. Not lazy: deliberate.

## The rules

1. **Build exactly what was asked, at the simplest fidelity that satisfies it.**
   Asked for a countdown timer in the window? Draw `05:03` in a corner. Do not
   build a clock widget with themes, plugins, custom fonts, and a settings pane.
2. **Prefer editing over adding.** Extend an existing function, file, or
   component before creating a new one. A new file needs a reason.
3. **No speculative generality.** No config option, no abstraction layer, no
   plugin hook, no "so we can swap it later" interface — unless the request
   itself needs it *today*. Second caller earns the abstraction, not the first.
4. **Don't touch what you weren't asked to touch.** No drive-by refactors, no
   renames, no reformatting, no dependency bumps, no "while I was in there".
   Note them in one line at the end instead of doing them.
5. **Follow the code that's already there.** Match its patterns, naming, error
   handling, and comment density even when you'd have written it differently.
   Consistency is cheaper than correctness-of-taste.
6. **Keep the change reviewable.** If the diff is getting large, that's the
   signal you drifted into building a system. Back out to the small version.
7. **Still do the engineering.** Tests for the new behavior (TDD where the repo
   already does TDD), handle the error cases that can actually happen, run the
   existing test suite before declaring done. Minimal ≠ untested, and ≠ leaving
   a known-broken path behind.
8. **Handle real cases, skip imagined ones.** Guard the inputs the feature will
   actually see. Don't add defensive code for scenarios the codebase can't
   produce.

## The 60% rule for questions

Before asking the user anything, ask yourself: *can I guess their preferred
answer from this skill's philosophy and what they've already said?* If yes with
**better than 60% confidence — don't ask.** Pick the minimal correct option,
do it, and state the assumption in one line when you report back.

Ask only when both are true:

- The answer genuinely changes the shape of the work (not just a detail), **and**
- Guessing wrong means throwing the work away or doing something unsafe/irreversible.

Almost always guess: colors, placement, wording, format, file location, naming,
padding, default values, which of two equivalent libraries already in the repo.
Reasonable-and-changeable beats perfect-and-blocked.

Batch anything that truly must be asked into a single round of multiple-choice
questions with a recommended default first — never a drip of one question at a
time, never a list of 20.

## Before you start

State, in two or three lines max:

- what you're going to change (files/functions),
- the deliberately simple approach you chose,
- any assumption you guessed instead of asking.

Then build it. Don't turn this into a design document.

## When the minimal version is genuinely wrong

If the small change would leave something actually broken — a real
correctness, data-loss, or security problem — say so in one or two sentences,
do the version that isn't broken, and explain why in the summary. The bias is
toward small, not toward shipping something you know is defective.

## When you're done

Report in this shape:

- **What changed** — one line per file.
- **How it was verified** — tests added, suite run, manual check.
- **Assumed** — the guesses you made instead of asking (if any).
- **Left undone** — things you noticed but deliberately didn't touch (if any).

No victory lap, no summary of the philosophy, no next-steps menu unless asked.
