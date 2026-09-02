# Blockers

Step 3 of the cycle covers the check itself: read a task's blockers before starting, name
which kind it is, and wait for the owner. This file covers what happens next, plus the
related case of an epic that outlives its children (see "When to sweep" below), which is
not a blocker itself but gets caught by the same reconciliation habit.

Two kinds behave differently throughout, so keep them distinguishable on the board.

**Blocked by a task** uses the tracker's relation or link field. It clears by arithmetic
when the other task reaches Done, and only a structured link lets you work that out without
reading every description.

**Blocked on input** carries a label such as `blocked:input`, plus a written note naming
the blocker and what would clear it, as in "blocked: waiting for the final Italian copy
from the owner". That second half is the test you apply later. Without it, any new comment
looks like a resolution, and "the client still hasn't sent it" would promote the task by
mistake.

## Starting a task that is still blocked

"Start anyway" means working the unblocked part. It does not cancel the blocker and it does
not license guessing at the missing piece.

Before touching anything, work out where the boundary runs: which acceptance criteria you
can satisfy with what exists, and which ones need the missing input or the other task. Say
that before starting, so the owner learns now that half the task is reachable, and not at
handoff.

Keep the request standing. Note in the task that work began while blocked, leave the ask in
place, and repeat it when you hand off. A blocker does not stop being a blocker because
someone worked around it for an afternoon.

When you reach the boundary, stop and put two options to the owner.

**Split.** Move the blocked criteria into a new task, link it to whatever it waits on, and
ship the finished half on its own. Use this when the delivered half stands up by itself:
the owner can see it, test it, and accept it without knowing about the other half.

**Park.** Leave the task in progress, or move it back with a comment, and pick something
else up. Use this when the halves only make sense together, because shipping one of them
means the owner accepts something that does not yet do what the task promised.

The test between them is whether the finished half is honestly acceptable on its own. If
splitting exists to get a card into review, it is the wrong call. Note the difference from
review finding problems, where splitting is always wrong: there the work is incomplete
against its own criteria, while here an external dependency moved the boundary.

When splitting, move the blocked acceptance criteria into the new task instead of deleting
them. A task arriving in review with criteria removed without a note looks complete and is not.

## Clearing a blocker

Nothing promotes itself. A task whose blocker cleared last week still sits in Backlog
unless someone checks, and by then the board is telling the owner that work is blocked when
it is free.

**Blocked by a task** resolves by arithmetic. Whenever a task reaches Done, look at what
linked to it, and promote any dependant whose last unmet dependency just cleared. Do this
while closing the task in step 9, with the links already in hand, and say which tasks moved
up.

**Blocked on input** resolves by reading. Sweep the input-blocked tasks for new comments,
and for each one apply the test written in the task: does this comment supply what was
named, or not? A comment saying "the client still hasn't sent it" updates the record
without resolving the blocker, and promoting on it would push a still-blocked task into
Ready.

Sweep the In review column at the same time. The owner may have approved in a board comment
instead of in the session, and nothing pushes that back into the conversation. A task
sitting in review with an approving comment on it has been accepted, so treat it that way
and finish step 9. Missing this costs nothing permanent, since the next sweep catches it,
but a stale In review makes the second tier of "what's next" report work as waiting on
someone who already replied.

## When to sweep

Three moments, and not more often, since each sweep costs a read per blocked or in-review
task:

- At the start of a session, before proposing anything.
- When answering "what's next", since tier 2 is wrong if the data is stale.
- When the owner names a specific task, which is step 3 of the cycle.

When a sweep promotes something, say so and say why: "#41 is unblocked, you sent the copy
on the 19th." The owner has forgotten what they supplied, and the promotion looks arbitrary
without the reason.

On a large board, sweep only what is marked blocked plus the In review column, never the
whole backlog. If the board has hundreds of blocked tasks, that is the finding worth
reporting, and the sweep is not the problem to optimise.

Add open epics to the same sweep, at the same three moments. Nothing else in the cycle
revisits an epic once its children stop needing attention, since closing a child only
checks what depended on it, leaving what it belonged to unexamined. An epic can end up
fully done, or long since abandoned, while still sitting in In progress because nobody
happened to look back at the parent card. For each open epic, check its children: if all
are closed, say so and ask whether to close the epic; if some are still open, note how many
so the epic's own status reflects real progress instead of the day it was opened. This
costs one read per
open epic, same as a blocked task, so it stays cheap even added to every sweep.
