# Claude Code: 16 tips with prompts

The companion checklist for [16 Research Backed Tips That Make Claude Code 10x Better (& Cheaper!)](claude-code-5-levels.md).

Hand this file to your agent:

```
Read https://raw.githubusercontent.com/aishwaryanr/awesome-generative-ai-guide/main/youtube/claude-code-16-tips.md

Go through all 16 tips. Tell me which ones we already do, which ones apply to this
project, and which ones do not. For the ones that apply and we are not doing,
propose the specific change and wait for me to approve each one before you make it.
```

Levels build on each other. Level 5 on a project with no Level 1 gives you an
automated way to produce the wrong answer faster.

---

### Level 1: Context audit and setup

**Tip 1. Audit your context before you trust it.** Stale and conflicting files
produce confident wrong answers, because the agent cannot tell which version is
current.

```
Audit these files before I rely on any of them. List everything that conflicts, is
duplicated, is out of date, or is missing, and point to the exact file. Do not fix
anything yet. Then we go through the list together and fix them one at a time,
based on my calls.
```

**Tip 2. Set up an identity file, and write it yourself.** A short brief the agent
reads at the start of every session. `CLAUDE.md`, `AGENTS.md`, or your tool's
custom instructions. Keep it tight: it loads on every session, so bloat costs
tokens on every task before you type anything.

A working example:

```markdown
# Working with me

## Who I am
I run Acme Analytics. We sell a lightweight analytics tool to early-stage founders.

## How I like to work
- Plain, direct language. No corporate filler, no exclamation points.
- Show me the option you'd pick and why, then the alternatives. Don't make me choose blind.
- Match the writing examples in ./examples when I ask for anything customer-facing.

## Hard nos
- Never invent a number, a date, or a customer name. If you're unsure, ask.
- Never send anything external (email, Slack, social) without showing me first.

## Gotchas
- "Revenue" here always means recognized revenue, not bookings.
- Current pricing lives in pricing_current.md. Ignore any older pricing file.
```

Test for every line: delete any sentence that would not change the agent's
behaviour if it were missing.

**Tip 3. Show it what good looks like.** Point at real examples instead of
describing what you want.

```
Connect to my Gmail, pull the cold emails I wrote to [X, Y, Z], and write this new
one in the same style.
```

Make it reusable:

```
Look at those emails and build me a style template for cold emails to potential clients.
```

Give it a range, one straightforward example and one harder one, so it picks up the
intent instead of copying a surface pattern.

**Tip 4. Turn off the tools you are not using.** Every connected tool loads its full
description into context on every message, including the ones you never call. More
visible tools also means more ways to pick the wrong one.

---

### Level 2: Prompt optimization

**Tip 5. Let it interview you.** Models usually detect an ambiguous request. They
rarely stop to ask, and guess instead.

```
Before you write anything, ask me questions one at a time until you have everything
you need. Do not start until I say go.
```

**Tip 6. Make it cite its sources.** When it is working from a document, make it
point at the line.

```
Using only the document above, answer [question]. Quote the exact line behind every
claim. If the answer is not in the document, say "not stated".
```

You can skip "think step by step". Current models reason through problems without
being told to.

**Tip 7. Context first, question last.** Working from one large document with many
questions, paste the document once at the top and stack questions underneath. A
stable block at the top gets cached and reused, so each follow-up is faster and
cheaper than re-pasting.

```
[paste the document]
Using only the document above, [your question].
```

```
Next, from the same document: [another question].
```

---

### Level 3: Task isolation

**Tip 8. Fresh chat per task.** Everything from the last task stays in context,
competing for attention and costing tokens. Open a new session when the task
changes.

**Tip 9. Give it only what matters.** Across 18 models, the same fact in a short
focused prompt beat the same fact buried in a large one. Fitting inside the window
and being used well are different things.

**Tip 10. Compact your progress into a file.** For anything spanning more than one
session.

```
Write a compact progress file called progress.md with exactly this: the goal, the key
decisions we have made and why, what is still open, and the single most important
thing to do next. Keep it tight, no filler.
```

Automatic compaction decides for you what survives. Writing it yourself means you
choose. Partway through a long task, this shows you what it actually held on to:

```
Summarize everything you currently have in context.
```

---

### Level 4: Failure recovery

**Tip 11. Rewind to the last good point.** One slightly-off instruction that
everything got built on top of. Correcting it in place piles more mess on top.

In Claude Code, double-tap Escape or type `/rewind`, pick the message right before it
went sideways, and everything after that drops out of context. Then re-prompt from
that clean point with what you now know. Codex has a lighter version, editing an
earlier message, which does not undo file changes.

**Tip 12. If the whole thread is rotted, hand off and restart clean.** Reset, but
keep the decisions.

```
Write a handoff into progress.md: keep [the specific things worth keeping], and drop
everything else.
```

Open a fresh session, load that file, keep going. Trigger: having to correct the same
thing more than twice.

---

### Level 5: Loop engineering

A loop needs all 4 of these. Missing any one is how loops spin, stall, or run up a bill.

**Tip 13. Give it a goal it can check itself.** "Keep an eye on the competition"
never completes. "Check all 5 competitors and report what changed since yesterday"
does. If you cannot write a checkable goal, the task is not ready to be a loop.

**Tip 14. Give it a check separate from the doing.** Whatever decides pass or fail
is separate from whatever produced the work. An agent grading its own output is
biased toward passing.

**Tip 15. Add a cap and a gate.** The cap stops it running forever and running up
cost. The gate limits what it can touch. Set both in the tool, not only in the
prompt, because a rule that lives only in the prompt is one the model can talk
itself out of.

**Tip 16. Give it a memory it learns from.** A file it reads before starting and
updates when it finishes, so each run knows what the last one saw.

All 4 in one instruction:

```
Every morning, check these 5 competitors: [their pricing pages, blogs, and LinkedIn].
Compare each against the snapshot in progress.md and flag only what actually changed,
then save the updated snapshot back to that file. Post a short summary to my Slack:
what changed, and for which competitor. Stop once you've been through all five, and
don't take any action beyond reading and posting that summary.
```

Watch the first run. Sit through one or two cycles before letting it run unattended,
and confirm it checks the real thing instead of gaming the check to declare itself
done.
