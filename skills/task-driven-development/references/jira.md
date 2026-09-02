# Jira

## Discover the project's actual workflow first

Never assume status names. Two Jira projects in the same company rarely share a
workflow, and transitioning to a status that does not exist fails silently in some
integrations.

1. Get the project's issue types. A project may not have `Epic` enabled, or may use a
   team-managed variant with different names.
2. For the issue being worked on, fetch its **available transitions**. Jira only allows
   moving along defined transitions, so a status that exists on the board may still be
   unreachable from the current one, even if it appears in the full list of statuses.
3. Map intent to the closest transition. Starting work maps to `In Progress`,
   `In Development`, or `Doing`. Ready for review maps to `In Review`, `Code Review`,
   `Peer Review`, or `Review`.
4. Tell the user which transition was used when the name differs from the canonical one.

## Hierarchy

Jira has two distinct mechanisms; they are not interchangeable.

**Epic → Story/Task** is the parent link, the `parent` field in modern Jira and previously
"Epic Link". Children are first-class issues, separately assignable, estimable,
schedulable, and visible on the board. Use this when pieces are independently valuable.

**Task → Sub-task** is a separate issue type. Sub-tasks do not appear as independent
cards on most boards and cannot be moved between sprints on their own. Use this for
steps of a single deliverable.

Do not nest an epic under an epic. Work that large is an initiative, and it needs a
conversation with the user instead of a ticket.

For the epic sweep in `references/blockers.md`, a JQL search on `parent = EPIC-1` (or
`"Epic Link" = EPIC-1` on older Jira) returns every child with its status in one query,
so counting open children costs nothing extra beyond the usual read.

## Creating issues

Three fields are required in practice: `project`, `issuetype`, and `summary`. Everything
else depends on the project's field configuration, and a required custom field will reject
the create call. When a create fails, read the create metadata for the project instead of
retrying blind.

Summary: imperative, no trailing period, under ~80 characters.

Description formatting depends on the API version. Jira Cloud REST v3 expects Atlassian
Document Format; a plain markdown or wiki-markup string produces an
issue with an empty description. Use the API version the available tool expects. When in
doubt, create one issue and read it back to confirm the description rendered.

## Linking

- Blocking relationships → issue links of type `Blocks` / `is blocked by`
- Duplicates and related work → `Duplicates`, `Relates to`
- Follow-up tasks discovered during work → create, then link with `Relates to` and
  mention the link in the review comment

## Review handoff

Post the summary as a comment on the issue; chat alone leaves no record on the board. Include
the branch or PR URL if one exists. Many Jira workflows auto-transition on PR events, so check the
issue status after opening a PR instead of transitioning it a second time.
