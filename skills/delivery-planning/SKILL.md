---
name: delivery-planning
description: Builds a delivery timeline from an existing task board. Sequences work by dependency, derives a critical path, produces a Gantt or milestone view, and answers "when will this be done" with ranges, avoiding false precision. Use this skill whenever the user asks for a timeline, schedule, Gantt chart, milestone plan, release date, critical path, capacity or velocity estimate, or asks when something will be ready, including casual forms like "how long will this take" or "can we make it by the end of the month". Do not use this skill for what to work on next, what is blocked, or the order of upcoming work, since task-driven-development answers those from the board's queue. The discriminator is whether the answer needs a date, a duration, or a critical path. Pairs with task-driven-development, which produces the board this skill reads.
---

# Delivery planning

You derive a schedule, you do not author one. Order, duration, milestones, and the critical
path all come from data already on the tasks. When that data is missing, name what is
missing. Do not fill the gap with plausible numbers.

## First: is a timeline worth building?

Planning apparatus has a cost, and it gets paid on every board change. Say so plainly when
the cost outweighs the benefit.

**Not worth it** when one person is doing the work, the horizon is a few weeks, and an
ordered Ready column already answers "what next" and "roughly when". A Gantt over six tasks
is ceremony. Offer the ordered queue instead: same information, no maintenance.

**Worth it** when work runs in parallel across people or workstreams, when an external date
exists such as a launch, a client commitment, or an event, when several dependency chains
interleave and the sequencing is no longer obvious by eye, or when someone other than the
person doing the work needs to see the plan.

If the request really means "when will this specific thing be done", answer that directly
from the dependency chain leading to it. That takes five minutes and needs no chart.

## Prerequisites

A timeline needs four inputs. Check each against the board before building anything.

**Dependency links**, meaning structured relations between tasks. A mention in the body does not count: without a structured link there is no sequence and no critical path, only a list sorted by guess.

**A coarse size per task**, in days or buckets such as half a day, a day, a few days, or a
week. Precision is not the goal, and false precision does harm. A task that resists any size
estimate is not atomic yet, so send it back for decomposition instead of estimating the
unknown.

**A persisted order**, held in the board's ranking or an order field, never only in a conversation.

**Capacity**, meaning how much time per week really goes to this project. People forget this
input, and it dominates most answers. Two days of work at one day a week takes two weeks.

### When only some inputs exist

Answer at the level the data supports, and say which level you are at. Priority and
ordering are not sizes: they tell you what comes first, never how long it takes.

- **Dependencies and order, no sizes.** Produce a sequence: what happens in what order,
  what blocks what, which chain is longest. No dates. This answers "what is the plan"
  honestly and refuses "when will it be done".
- **Sizes as well.** Add durations, and the sequence becomes a timeline.
- **Capacity as well.** Add calendar dates, as ranges.

Most boards sit at the first level, and a sequence is often all the owner needed. Offer the
step up instead of assuming it. Backfilling sizes across twenty tasks takes about twenty
minutes, and it converts an unanswerable question into an answerable one.

When something is missing, name which input it is and what supplying it would take.

## Building the plan

1. **Take the task set.** A milestone, an epic, or everything not yet Done. State which
   one, because a timeline over an unbounded backlog means nothing.
2. **Build the dependency graph** and check it for cycles. A cycle means two tasks each
   claim to need the other, which is a decomposition error. Fix it on the board instead of
   breaking it silently in the chart.
3. **Sequence** by working out the earliest start for each task given its dependencies and
   capacity.
4. **Find the critical path**, the chain whose length sets the end date. Everything off it
   has slack, and delays there cost nothing until the slack runs out. This matters more than
   the chart.
5. **Mark blocked tasks as schedule risk.** A blocker on the critical path is a date problem,
   not a task problem. Raise it at once instead of drawing it as one more bar.
6. **Add a visible buffer** as its own line, sized against how uncertain the estimates are.
   Never pad individual tasks: nobody sees hidden padding accumulate, and nobody sees it
   spent.

## Presenting it

Give a range instead of a single date. "Between the 12th and the 19th, most likely the 15th" is information.
"The 15th" is a promise nobody agreed to make.

Lead with what determines the answer: the critical path, and the two or three tasks whose
estimates dominate everything else. If one task's uncertainty swings the finish by a
fortnight, that belongs in the first sentence and the rest of the chart is detail.

Name the assumptions inline, including capacity per week, what counts as done, and which
external dependencies are assumed to arrive on time. An unstated assumption becomes a
broken promise later, and nobody reading the chart will reconstruct it.

Say this is a forecast from current estimates. Readers trust charts
far past their accuracy, and someone will screenshot your Gantt, forward it, and quote it
back months later as something you agreed to.

For the artefact itself, prefer whatever the team already uses. A milestone list with date
ranges is often more useful and far cheaper to maintain than a chart, and it avoids the
illusion of day-level precision. Build a real Gantt when you need to communicate parallel
tracks and their overlaps, which is what the format does well.

## Keeping it current

Re-derive from the board and never maintain a parallel plan document. A plan you update by
hand diverges within two weeks, and everyone who reads it after that gets the wrong picture.

Once several tasks are Done, compare real completion against estimate and use the measured
throughput for the rest of the forecast. Three finished tasks tell you more about the pace
than any amount of up-front sizing.

Re-forecast only when the estimate moves by a lot. Churning the dates on every task trains
people to stop reading the plan.

When a date slips, report it as soon as it is known, with the cause and the options: cut
scope, add time, or add capacity. A slip surfaced early gives someone a decision to make.
The same slip surfaced on the deadline is only bad news.

## What not to do

- Do not invent durations for tasks that have no estimate. Mark them as unestimated and
  show what the answer depends on.
- Do not build a chart to look responsive when the real answer is that three of these are
  blocked on the owner, and until they clear, any date is fiction.
- Do not treat a roadmap document as a schedule. Roadmaps express intent, unsequenced and
  unestimated, so drawing one as a timeline invents commitments nobody made.
- Do not let the plan become the source of truth. The board holds that role; the plan renders
  it.
