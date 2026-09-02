---
name: task-driven-development
description: Use this skill whenever the user asks to build, implement, fix, refactor, or change anything in a project that has a board in GitHub Projects, Jira, or Notion, including casual phrasings like "add a login button", "fix this bug", or "let's start on the export feature" that never mention a task, ticket, epic, or board. Use it too when the user asks what to work on next, what is blocked, what state the work is in, or in what order upcoming work should happen, since those answers come from the board's queue. Runs a board-first development cycle where every change starts from a tracked task, large work is decomposed into atomic tasks under an epic or parent, board status moves automatically (In progress on start, In review when done), work happens on a per-task branch, and merging or deploying waits for the owner's explicit go-ahead. Hand a question to delivery-planning only when answering it needs a date, a duration, or a critical path.
---

# Task-driven development

Work starts when a task exists on the board, is atomic, and has been moved to In progress.
Writing code does not start it. The board is the shared source of
truth, so anything implemented outside it stays invisible to everyone else, cannot be
reviewed, and cannot be planned around.

Follow this cycle for anything that changes the project: code, config, schema,
infrastructure, or content.

## The cycle

1. Read the project's own instructions. They override everything here.
2. Find the task on the board.
3. Named task has a blocker? Say so and wait for the owner before starting.
4. No task? Create it. There are no exceptions for "quick" changes.
5. Not atomic? Decompose, show the breakdown, then create the children.
6. Move to **In progress** and cut a branch, without asking permission.
7. Implement exactly that task's scope.
8. Push the branch, move to **In review**, report. Do not merge.
9. On explicit approval: merge, move to **Done**, close the task, promote what it unblocked.

## 1. Read the project's instructions first

Project-level files (`CLAUDE.md`, `README`, `CONTRIBUTING`, `.github/`) hold the board
identifiers, status names, branch conventions, WIP limits, and the pre-"done" checklist
for that specific project. Read them before touching the board.

Where they conflict with this skill, the project wins. This skill describes the shape of
the cycle. The project describes its actual parameters.

## 2. Find the task

Search the board before anything else. The owner often has a task already and refers to
it by feature name instead of number.

Match on keywords, then scan open epics for a parent that covers the request. If several
candidates fit, list them and ask which one applies. Logging work against the wrong task
is harder to unwind than asking.

## 3. Check its blockers before starting

When the owner names a task to work on, read its blockers before doing anything else. If
it has one, say so and wait. Unlike a WIP limit, which you mention and then work through,
a blocker stops the pickup until the owner answers.

Say which kind it is, because they resolve differently:

- **Waiting on a task.** Name the blocking task and its current column: "#46 is blocked by
  #41, still in Ready." The owner may want #41 first, or may know the dependency no longer
  holds.
- **Waiting on input.** Quote what the task says you are waiting for: "#46 is waiting for
  the final Italian copy from you." The owner is often the one who owes it, and asking at
  the moment they reached for the task is the cheapest possible time to ask.

Three ways it goes from there. The owner supplies what was missing, so clear the blocker,
record it in the task, and start. The blocker stands, so leave the task where it is and
offer the top of Ready instead. Or the owner says start anyway, which means working the
unblocked part only, stopping at the boundary, and putting two options to them there. Read
`references/blockers.md` before doing that, and before any sweep for cleared blockers.

## 4. If no task exists, create one

Create it first, then work. Say so: "There's no task for this, so I'll create one, break it
down, and then start."

```
Title: <imperative, one outcome> — "Add rate limiting to /api/export"

## Context
Why this is needed. Link the parent epic or related task.

## Scope
- What is included

## Out of scope
- What is explicitly excluded

## Acceptance criteria
- [ ] Verifiable statement
- [ ] Verifiable statement

## Notes
Constraints, affected files or services, open questions.
```

Write the task in the language the owner writes in, headings included. The English
headings above show the shape only: on a Russian board the whole template is
Russian. Match the existing tasks when the board already has some.

Acceptance criteria must be checkable by someone who did not write the code. "Works
correctly" fails that test. "Returns 429 after 100 requests in 60s" passes it.

Place the task per "Where a new task lands", and record why it landed there.

## 5. Decompose

A task is atomic when all of these hold:

- One outcome, expressible in a sentence without "and".
- Completable in one focused session.
- Reviewable on its own, without waiting for a sibling.
- Touches one concern: one endpoint, one component, one migration.

If any of them fails, split it. There are two shapes, and choosing between them matters.

**Subtasks under a parent** suit pieces that only make sense together and ship as a unit.
The parent stays the deliverable and the children are steps toward it.

**An epic with child tasks** suits pieces that are independently valuable, that may ship
or be scheduled separately, or that number more than roughly six. An epic with three
trivial children is bureaucracy. A parent with fifteen subtasks is a roadmap in disguise.

Order children by dependency, and record blockers using the tracker's own link or
relation field. A person can read "blocked by #12" in a
description, but the board cannot, and a structured link lets you surface blocked work
without re-reading every task. Recording it during decomposition costs nothing, since the
order has just been worked out.

Show the breakdown before creating it. Decomposition encodes design assumptions, and
correcting it in conversation is far cheaper than cleaning up a board full of tickets.

Of the children, only those with no unmet dependency go to Ready. The rest wait in
Backlog. From a breakdown of eight that is usually one or two, which puts the dependency
order on the board instead of leaving it buried in the breakdown.

## 6. Start work

Move the task to **In progress** immediately, without asking. A status change is
bookkeeping. Asking about it each time adds friction and trains the owner to rubber-stamp.

Before moving anything into In progress, list what is already sitting in that column,
beyond this task's own status. Nothing earlier in the cycle reads this: step 3 checks only
the task's own blockers, leaving who else is already working unexamined, so this read has
to happen here or it happens nowhere. If that list is at or past the limit, say so using `When a column is over
its limit` below, then move the task anyway once you have said it.

Hold one task at a time. The column may legitimately hold a few, since the owner has work
in flight too, but one worker juggling two finishes neither. If a second must start
before the first is reviewed, say so and confirm.

Cut a branch per task and never commit to the production branch. Follow the project's
branch naming. For an epic, one branch with a commit per coherent section reviews far
better than a single enormous commit.

Commit in meaningful chunks, with imperative messages, in the project's language.

## 7. Implement in scope

Implement what the acceptance criteria describe and nothing more. When something adjacent
and clearly broken turns up, note it, finish the current task, and propose a separate
one. Scope that grows without anyone noticing is the main reason review slows down and
estimates stop meaning anything.

If the work turns out much larger than the task described, stop and re-decompose.

Run the project's pre-"done" checklist before claiming completion: build passes, no broken
paths, mobile width checked, whatever it lists. Skipping it moves the failure to the
reviewer.

## 8. Hand off for acceptance

Review here means the owner checking the result by trying it, testing it, and deciding
whether it is what they asked for. It is not a code review. Write for someone who will
never open the diff.

Push the branch. **Do not merge.** Move the task to **In review** and report:

- What now works, described as a visible outcome
- Where to see it: the exact URL, page, or screen. "On the branch" does not count
- What to click or check, step by step, including the case that used to fail
- What deliberately did not change, with a link to any follow-up task

Write in the owner's language and at their level of detail. Implementation notes belong
in commit messages. The task comment is a testing instruction.

State anything that cannot be verified from the outside, such as a config change, a
schedule, or a limit, since the owner cannot discover it by looking. If verification
needs anything unusual, like a specific browser width, a hard refresh, or a preview URL
that must be assembled, say so. Otherwise they will look at the wrong thing and report it
broken.

Check off each acceptance criterion explicitly. Leave a box unchecked when it should be:
an unchecked box is a useful signal, while a falsely checked one destroys trust in the
board.

Then stop and wait. Acceptance belongs to the owner, and the task stays In review until
they respond, however finished the work looks.

## 9. The shipping gate

Do not merge to the production branch, deploy, publish a release, run migrations against
a shared environment, or trigger a pipeline that does any of those, unless the owner asks
in this session.

"The task looks done" is not permission to ship. Report that it is in review, state what
the next step would be, and wait.

After an approved merge, move the task to **Done**, then close it with a comment linking
the merge commit. If the board closed it automatically, leave the same comment on the
closed task. That is the automation working, and there is nothing to route around.

Then check what depended on this task. Any task whose last unmet dependency just cleared
moves to Ready, and say which ones moved. See `references/blockers.md`.

Then check the task's parent, if it has one. Closing a task never closes its epic, and
nothing else in this cycle revisits an epic once its children stop needing attention, so a
finished epic can sit in whatever column it was last left in indefinitely with nobody
noticing. If this closed the epic's last open child, say so and ask whether to close the
epic too. If children remain, say how many, so the epic's own card reflects real progress
instead of looking untouched since the day it was opened.

Never force-push and never rewrite published history.

### One yes covers the whole decision

Approval to merge already covers closing the task, moving it to Done, and commenting.
Asking again for each one reads as noise, and it makes the genuine questions easier to
wave through.

Here is the line. Ask about anything **irreversible or externally visible** that the owner
has not already approved: merging, deploying, publishing, notifying reviewers, changing
shared settings. Do not ask about bookkeeping that follows from an approval already given.

## The board

Column names differ per project, but the roles do not. Map the project's columns onto
these, and decide by entry condition, ignoring the label.

**Backlog** holds everything captured but not yet selected: ideas, bug reports, requests,
half-formed features. No commitment, no ordering, and no requirement to be atomic.
Anything may land here, including a one-line note. Nothing is picked up from here
directly.

**Ready** holds actionable work: atomic, acceptance criteria written, nothing blocking it.
The test is whether someone could start it right now without asking anyone anything.

**In progress** holds what is being worked on at this moment, with a branch in existence.
It is not a queue of intentions.

**In review** is the owner's acceptance step. The work is pushed and unmerged, criteria
are checked off, and a person is trying it out. The reviewer is the customer who asked for
the work, so a task belongs here only once someone can inspect it. A change nobody can
see or click needs a way to be verified first. This column is never a parking spot for
stalled work: if work stopped, the task moves back instead of forward.

**Done** means merged and closed, and nothing else.

### Where a new task lands

Default to Backlog. Route there when any of these hold:

- An unresolved blocker: a missing decision, missing content, missing access, or a
  dependency that does not exist yet.
- Waiting on information from the owner, a client, or a third party.
- The owner signalled it can wait: "not important", "later", "low priority", "some time".

Route to **Ready** when the task is actionable, meaning no blocker, criteria written, and
nothing to wait for. A signal from the owner such as "this is the priority" or "next we
work on X" does not gate entry. It sets **position**, so the task goes near the top
instead of merely into the column.

An open question counts as a blocker. If it is unclear whether something is blocked, that
uncertainty answers the question: Backlog, with the question written down.

Readiness and priority do different jobs. Readiness decides whether a task is in the
column at all, and priority decides where in it. An urgent task with an unresolved blocker
still sits in Backlog, because urgency does not clear a blocker. A low-priority actionable
task sits at the bottom of Ready. One exception: work the owner has deferred goes to
Backlog even when perfectly actionable, since "not important, some other time" is a
decision.

### Ready is an ordered queue

Membership says a task *can* be started. Position says *when*. Keep the column ordered top
to bottom, because that ordering is the plan in rough form, and it makes "what do I take
next" answerable by looking at the board instead of by discussion.

Order comes from dependencies first, the owner's stated preference second, and size last.
A short task that unblocks three others outranks a long one that blocks nothing.

### The priority field

Where the board has one, read the project's convention before setting anything. Some
projects flag only the highest priority, others only background work that must not be
picked up by accident. Either way the field earns its meaning by staying mostly empty. Set
on every task, it says nothing at all.

Set it from what the owner said. Given no signal, leave it unset and do not guess a middle
value.

### Record why, alongside where

A task sitting in Backlog with no explanation looks the same as one nobody has read.
Whenever placement or priority follows from something specific, write it into the task:

- **Blocked by a task.** Use the tracker's relation or link field instead of prose.
- **Blocked on input.** Label it so the two kinds stay distinguishable, and name both the
  blocker and what would clear it: "blocked: waiting for the final Italian copy from the
  owner". That second half is the test you apply later. See `references/blockers.md`.
- **Deferred.** Quote the owner's decision and date it, as in "2026-08-19, owner: not
  important, after the gallery work".
- **Prioritised.** Say what made it next, especially when it jumped the queue.

This is what makes the board readable weeks later, and what lets a blocked task be
promoted the moment its blocker clears. Without it nobody remembers what the task was
waiting for, and it sits there for months.

### Moving backwards

Review found problems, so the task returns to In progress. Do not open a new task for
fixes to work that has not shipped, because the task is not finished and splitting it hides
that. A new task is right only when review revealed new scope, in which case the original
ships as is and the new part gets its own task. An external blocker is the other case where
splitting is right, and it works differently. See `references/blockers.md`.

Blocked mid-work, so the task returns to Backlog with a comment naming the blocker and
what would clear it. It comes back to Ready only once the blocker is gone and the owner
still wants it next, since a blocked task parked in Ready misrepresents what is available
to pick up. Leaving it In progress is worse still, because anyone reading the board then sees work in
flight that nobody is doing.

## When a column is over its limit

WIP limits are soft wherever they exist, and they are not there to stop an agent from
working. Never refuse a task because a column is full. Say something, then do the work.

**Say it once per task.** Mention the limit when you pick up or hand off a task, and not
again until the next one. Tie it to the task itself: a session has no
edges an agent can observe, and after the context is compacted you will not remember
warning anyone. Repeat it every turn and the owner stops seeing it. After two ignored
warnings, nobody reads the third.

**Be specific.** "In review has six items" gives a number. "These three have been sitting
since the 12th, and merging them clears the queue" gives the owner something to act on.
Name the oldest items, and skip the count.

**In progress at the limit.** Something already open needs finishing. Before starting
anything new, mention what is in flight and suggest closing one out. If
the owner wants the new task anyway, take it. They may know something about priority that
the board does not.

**In review at the limit.** This one is worth raising properly, because the queue belongs
to the owner and every extra item makes the eventual merge harder for everyone. List what
is waiting, oldest first, with what each needs from them, which is usually a look and a
yes. Offer to walk through them, and mention that the branches keep diverging while they
wait, since that cost is invisible from the board.

**Well over the limit.** Say so once, in stronger terms, and name the two real options: clear the queue, or accept that the limit no longer describes how the project
works and change it deliberately. Never raise a limit unilaterally. A number the owner nudges upward every time
they hit it has stopped being a limit.

Skip the reminder when the owner is dealing with something urgent. A hotfix at midnight is
the wrong moment for process observations. Note it and raise it when the work is calm.

## Answering "what's next?"

There are two forms, depending on what was asked.

**Handing off one task.** When a task leaves In review, do not ask an open "what would you
like next?". Propose the top of Ready by name, give one line on why it is first, and
mention the runner-up. An open question forces the owner to re-derive a decision already
encoded on the board, which is the work the board exists to avoid. If they pick something
else, that tells you something about priority: move it up and record why, so your next
proposal improves.

**Asked about the state of the work.** Answer in three tiers, in this order. Each tier
answers a different question, and merging them destroys all three.

1. **Can be started now.** Ready, in queue order, top first. Name each task and give one
   line on why it sits where it does, especially what it unblocks. Three to five is
   plenty; past that the list stops being a recommendation and becomes a dump.

2. **Blocked, and on whom.** What is waiting, what for, and who can clear it. Surface this
   even though none of it can be worked on, because most blockers resolve to something the
   owner personally supplies: a decision, a piece of content, an approval, an access
   credential. Naming them early turns a dead list into a request, and gives the owner a
   week to clear a blocker, instead of the morning the task is reached. Order by how soon
   the blocker will hurt. Never by task size.

3. **Later.** Actionable but deferred: low priority and future work. One line each, or
   just a count once the list grows, as in "eleven more in Backlog, none blocked". This
   tier exists so the owner can see nothing was silently dropped, and not to be read in
   full.

Never mix tier 2 into tier 1. A blocked task listed among startable ones reads as
available, gets picked, and the blocker surfaces after work has begun, which is the
failure the tiering prevents.

The tiers double as a health check. A fat tier 2 means the owner is holding up the plan. A
fat tier 3 with a thin tier 1 means grooming has fallen behind, so triage before writing
any code.

## Board configuration

Board APIs address fields and options by internal ID instead of display name, and looking
them up on every run is slow and failure-prone. Record them once in the project's
instruction file, including project ID, status field ID, and the ID of each status option,
then read them from there.

Keep those identifiers in the project's file and never in this skill. Skills are shared
and may be published, while identifiers belong to one board.

If the recorded IDs stop working, someone reconfigured the board. Re-read it and update the
project file. Do not fall back to guessing by name.

For tracker specifics, read only the file for the tracker in use:

- GitHub Projects → `references/github-projects.md`
- Jira → `references/jira.md`
- Notion → `references/notion.md`

Blocker handling beyond the check in step 3, including starting blocked work, sweeping
for cleared blockers, and sweeping open epics for children that closed without anyone
revisiting the parent, lives in `references/blockers.md`.

If the needed tool or connector is missing from the session, do not fall back to working
without a task. Name the missing connector and offer to draft the task text for pasting.

## Common situations

**"Just a quick fix, skip the ticket."** One-line changes still get a task, since it costs
seconds and keeps history intact. If the owner insists, comply, because it is their board,
but say once that the change will be untracked.

**Bug found mid-task.** Give it a separate task. Do not fold it into the current one, even
if the fix is two lines.

**The request spans several existing tasks.** Point that out and ask which comes first
instead of working across all of them.

**Investigation or spike.** Create a timeboxed research task whose acceptance criterion is
a written finding. Never a code change.

**A roadmap document exists.** Treat it as reference material. Never a work queue. Work only
on what the owner names in the current session, and never start roadmap items unprompted.
