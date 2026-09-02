---
name: project-bootstrap
description: Sets up a new project for task-driven work. Creates a Kanban board with the standard Backlog / Ready / In progress / In review / Done columns, sets WIP limits, adds a priority field and issue template, records the board's identifiers in the project instruction file, and seeds the first tasks from whatever the owner described. Use this skill whenever a project is being started or a repository has no board yet, and whenever the user says something like "set up the board", "create a project", "how should I organise this work", "start a new repo", or "add WIP limits", or begins describing work on a project where no tracker exists. Pairs with task-driven-development, which runs the cycle this skill creates.
---

# Project bootstrap

A project without a board has nowhere to put work, so the cycle collapses into whatever the
last conversation happened to remember. Setting one up takes a few minutes at the start.
Retrofitting it after forty untracked changes takes an afternoon.

## Check before creating

Look for an existing board first, in the tracker's project list, the `README`,
`CONTRIBUTING`, `.github/`, or any project instruction file. Teams often have a board that
the repository never mentions.

If one exists, do not create a second. Read its columns, map them onto the roles in
`task-driven-development`, and record the identifiers. A parallel board is worse than having
none: work splits across both, and nobody can trust either.

If the owner works inside a larger organisation, ask which tracker the team uses before
creating anything. Choosing one unilaterally has a long tail.

## The default board

Create five columns, in this order:

```
Backlog → Ready → In progress → In review → Done
```

Create them without interviewing the owner about their preferred workflow. Someone setting up
a first board does not yet know what they want, and the question produces a shrug or a copy
of whatever they last saw at work. Set the default, then say in one line
that the columns can be renamed or changed later. An experienced owner will adjust them
within a week, and a beginner gets something that works today.

If asked why these five:

- **Backlog and Ready stay separate** because "everything we might do" and "what can be
  started now" are different questions. Merging them is the most common mistake, and it is
  what makes a board stop being useful past about twenty tasks.
- **In review stays separate from Done** because acceptance belongs to someone else. Drop
  it and work becomes either invisible or prematurely declared finished.
- **Five is a floor.** Extra columns feel organised
  and cost real work, since every one of them adds a decision on every task. Add a sixth only
  when the owner can name a specific thing the board cannot show them today.

The column meanings, entry conditions, and routing rules live in
`task-driven-development`. Do not restate them here. This skill only creates the
structure.

Column names may be translated into the project's language, but the five roles should not
multiply along the way. Having both "In review" and "Testing" usually means the same state
written twice.

## Fields, labels, template

**Priority.** Add the field and leave it empty by default. Decide with the owner which end it
marks: some projects flag only the urgent, others only background work that nobody should
pick up by accident. Either convention works. Using both at once means nothing. Write down
the one you chose.

**Labels.** Keep to a minimal set: type (`bug`, `feature`, `chore`) and `epic`. Do not
invent a taxonomy at bootstrap. Labels nobody applies become noise, and the useful ones
become obvious after a month of real work.

**Issue template.** Commit one matching the task shape used by `task-driven-development`:
context, scope, out of scope, acceptance criteria as checkboxes, notes. A template makes
the acceptance-criteria habit stick, and that habit carries more of the cycle's value than
anything else in it.

**Automation.** None at bootstrap, beyond what the tracker enables by itself. Automatic
transitions are convenient once the workflow is stable and confusing before then, because
cards move for reasons the owner has not yet learned to predict.

## WIP limits

Nothing needs disabling, since limits are off by default in every tracker. The question is
only where to set them and whether the tracker supports it.

Defaults:

| Column | Limit | Why |
|---|---|---|
| Backlog | none | A holding queue. Capping it makes people delete cards instead of finishing work. |
| Ready | none | Same reasoning. Its health shows up in the ordering; size alone says little about it. |
| In progress | 3 | Parallel work in flight across everyone touching the project. |
| In review | 6 | Above this, unmerged branches drift apart and merging turns into conflict resolution. |

Adjust to the project. These numbers are not universal. A rough sizing rule for a
team is two to three items per person in a stage. For one owner working with an agent, 3
and 6 are already generous.

The In review number follows from mechanics. Every task sitting in
review is a branch that keeps diverging from the main line while it waits. Six unmerged
branches cost you a bad afternoon. Twelve cost a week nobody planned.

That column tells you more than any other. It fills up because acceptance is slow, regardless
of how fast work itself moves, so a full In review points at the owner as the bottleneck.
More coding will not clear it.

**Support by tracker:**

- **Jira** has native column constraints. Set them.
- **GitHub Projects** supports per-column limits on the board layout, set from the column
  menu and shown as a count pill that highlights when exceeded. The limit is soft, so cards
  can still be dropped past it, which is the right behaviour: a hard block stops work at
  the worst possible moment. If the API in use does not expose the setting, do not skip it.
  Give the owner the two-click path and confirm afterwards.
- **Notion** has no native limits. There the limit lives in the project instruction file as
  a convention, enforced by whoever moves the cards.

Whichever the tracker, write the chosen numbers into the project instruction file. A limit
that exists only in the board UI is invisible to an agent moving cards through the API, and
it will be exceeded without anyone noticing.

## Record the identifiers

This is the step that makes everything else work, and the easiest one to skip.

Write into the project's instruction file (`CLAUDE.md` or equivalent):

- Tracker, project name, project number, owner
- Project ID, status field ID, and the ID of every column option
- Priority field ID and its option IDs, plus the chosen convention
- WIP limits per column, and whether the tracker enforces them or they are convention only
- Branch naming convention and the production branch
- Where preview builds appear, and how to construct the URL if the host does not show it
- The pre-"done" checklist for this project, if one is known yet

Board APIs address fields by internal ID, so without this file every session spends calls
rediscovering them and fails a different way each time. Keep the IDs in the project file,
never in a skill: skills get shared and published, and identifiers belong to one board.

**Add a pointer to the cycle itself, beyond its parameters.** A skill's one-line
description competes for the router's attention against every other skill installed in
the project, and a busy project can install dozens. Tested against a real project with
24 other skills installed, `task-driven-development`'s description lost that competition
on casual requests such as "fix this bug", even when the phrasing matched its own trigger
examples almost exactly, and the model went straight to editing code with no board check
at all. `CLAUDE.md` does not compete for a routing slot. It loads in full on every
session, so it is the one place an instruction is guaranteed to be seen, instead of
merely eligible to be picked.

Write a short paragraph near the top of the project's `CLAUDE.md`, ahead of anything else,
naming the skill and covering three situations, beyond the change case:

```
## Board first, then anything else

Invoke the task-driven-development skill first, before any other tool, in each of these
situations:

- a request to fix, add, or change anything, even without the word "task" or "issue", even
  a small one
- a request that already names an issue by number ("take #119", "close #37"). Reading that
  issue directly instead skips the blocker check, the column limit, and the report format
  that only the skill knows, and a number in the request does not make those unnecessary
- a question about board state or what to do next ("what's next", "what's the plan this
  week"). This counts as part of the cycle too

It decides whether an issue already exists for this work, whether to create one, how to
check blockers and limits, and how to work the board.
```

Translate the heading and body into the project's own language, matching the rest of the
file. This does not replace the skill body. It only guarantees the skill gets loaded, and
the skill's own steps still decide what happens next.

**Add a hard gate, beyond a pointer.** Measured on a real project (igorski-site,
24 other skills installed): with the paragraph above already in place, `Skill(task-driven-
development)` still fired in only 10 of 14 independent sessions (≈71%), with no reliable
pattern by phrasing. A vague fix request missed twice in a row, while a request naming an
issue by number and a status question both fired every time they were tried. A paragraph
loaded into context is a strong bias on which tool gets reached for. It is not a rule the
model is forced to follow, and nothing written into `CLAUDE.md` can make it one.

Write a `PreToolUse` hook into the project's shared `.claude/settings.json`
(`.local.json` stays personal to whoever ran bootstrap) that blocks `Edit`, `Write`,
`Bash`, and `PowerShell` until `task-driven-development` has run once in the
session, plus a matching `PostToolUse` hook on `Skill` that records the one time it does:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Skill",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json,sys,os,tempfile\nd=json.load(sys.stdin)\nif d.get('tool_input',{}).get('skill')=='task-driven-development':\n    sid=d.get('session_id','')\n    if sid:\n        open(os.path.join(tempfile.gettempdir(), 'claude-tdd-gate-'+sid), 'w').close()\n\""
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Edit|Write|Bash|PowerShell",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json,sys,os,tempfile\nd=json.load(sys.stdin)\nsid=d.get('session_id','')\nmf=os.path.join(tempfile.gettempdir(), 'claude-tdd-gate-'+sid)\nif not os.path.exists(mf):\n    print(json.dumps({'hookSpecificOutput':{'hookEventName':'PreToolUse','permissionDecision':'deny','permissionDecisionReason':'Board-first: invoke Skill(task-driven-development) before editing or running commands. See CLAUDE.md.'}}))\n\""
          }
        ]
      }
    ]
  }
}
```

Both commands parse the hook's JSON payload with `python3`, avoiding both `jq` and a
`grep`/`sed` pipeline. `jq` is not guaranteed present, and was in fact missing on the machine this was
written and tested on. Plain POSIX text tools fared worse than expected too: on that same
machine, piping the hook's own stdin through `cat` silently returned nothing, while `wc -c`
and `python3` read the identical payload correctly, so `grep`/`sed` is not a safe fallback
either, even though POSIX tools are usually assumed to work everywhere. `python3` is not
guaranteed present on every machine this runs on. Confirm it exists in the project before
handing this out, and fall back to `node` (rewrite both commands using its own JSON parsing)
if it does not. The marker lives in the OS temp directory, named by session ID, so it never
touches the project's own files and needs no cleanup step of its own: a stray empty file per
session is the entire cost, and the OS clears its temp directory on its own schedule.

The gate blocks only the four tools capable of an actual change. Reading and searching stay
open, so the model can look around before deciding the request needs the skill at all. Once
`task-driven-development` has run once, the gate stays open for the rest of that session.
A gate that fired on every message would train the owner to work around it instead of
through it.

Merge this into `.claude/settings.json`, do not replace the file: a project that already has
hooks or permission rules loses them if this block replaces the file instead of merging into
it. Test it once in the project it was written for before moving on, the same way it was
verified while writing this skill: send a request that never asks for the skill by name and
confirm the block fires with the reason pointing at the skill instead of a generic permission
error, then send a follow-up in the same session asking for the skill by name and confirm
the previously blocked action now goes through.

## Seed the first tasks

A board with zero cards teaches nothing. Turn what the owner has already described into
real tasks before finishing:

1. Capture everything mentioned so far as Backlog items, even roughly.
2. Groom the first two or three properly: atomic, with acceptance criteria.
3. Move only those to Ready, ordered.
4. Leave the rest in Backlog. Do not groom the whole list; most of it will change.

Then show the board and name the first task. Seeing one card in Ready and eleven in Backlog
communicates the system faster than any explanation of it.

## Repository scaffolding

Create only what the project needs: a `README` stating what this is and how to run it, a
`.gitignore` fitting the stack, and the project instruction file. Skip anything speculative.
Directories created "for later" get filled with the wrong thing.

Set the production branch and confirm whether pushing a non-production branch can affect
anything live. If deployment already exists, verify that isolation before the first real
task begins.

## What not to do

- Do not create a board when the team already has one somewhere else.
- Do not build an elaborate workflow for a project with four tasks.
- Do not ask a first-time owner to design their process. Give them the default and let them
  discover what they would change.
- Do not skip recording the identifiers because the board is visible right now. It will not
  be, in the next session.
