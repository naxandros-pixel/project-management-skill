# Notion

## The board is a database

A Notion board is a view over a database, and its columns are the options of one select or
status property. That has two consequences.

- Read the database schema before writing anything. Property names and their exact
  option strings are project-specific, and Notion rejects values that are not existing
  options. It never creates a new option on the fly.
- The property driving the board may be called `Status`, `State`, `Stage`, or anything
  else. Identify it by which property the board view groups by, or by which one has type
  `status`.

Match option strings exactly, including capitalisation and any emoji prefix. `🚧 In
progress` and `In Progress` are different values.

## Hierarchy

Notion has no built-in epic concept, so projects implement it in one of three ways.
Detect which one is in use before creating anything:

1. **A self-relation**, where the tasks database relates to itself, usually as
   `Parent task` and `Sub-tasks`. This is the closest analogue to subtasks, so use it for
   steps of one deliverable.
2. **A separate Projects or Epics database**, with tasks relating to a row there. Use this
   for epic-level grouping when it exists.
3. **Nested pages**, where children live inside the parent page body. This works, but the
   children never appear on the board, so avoid creating work this way unless the project
   clearly already does.

If none exists and grouping is needed, ask before adding a property. Schema changes affect
every view and every teammate, which makes it a bigger decision than creating a task.

For the epic sweep in `references/blockers.md`, a self-relation or a separate Projects
database is queryable, so filter the related rows by status directly. Nested pages are
not: nothing indexes them, so the only way to count open children is opening the epic
page and reading its contents, which is exactly why option 3 said to avoid it unless the
project already works that way.

## Creating a task

Page properties carry the structured fields; the page body carries the description.
Write the task template (context, scope, out of scope, acceptance criteria, notes) into
the body as blocks, using `to_do` blocks for acceptance criteria so they can be checked
off during review.

Fill whatever the database defines, such as `Assignee`, `Priority`, `Sprint`, or `Due`, but
do not invent properties. A missing field is a question for the user, never a schema
edit.

## Status transitions

Updating status is a page property update, and only the status property should change. A
partial page update that omits other properties leaves them untouched, while sending an
empty value for a property clears it, so be precise about what is included.

After moving to In Review, add the summary as a comment on the page instead of
appending it to the body. Body edits mix with the task definition and make the original
scope harder to read later.

## A caution about deletion

Notion's API "delete" archives a page. The page is recoverable, but it disappears from every
view immediately and can look like data loss to the team. Do not archive tasks as part of
this workflow. Completed work moves to a done status and stays there.
