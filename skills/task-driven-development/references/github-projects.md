# GitHub Issues + Projects (v2)

## Where status lives

An issue has two native states, open and closed. Columns like In progress and In review
live in a single-select field on the **project item**, separate from the issue itself. So
"move the task to In progress" means updating a project field, and the issue does not
change at all.

Consequences worth internalising:

- An issue not added to the project has no status at all. Add it on creation.
- Closing an issue does not move its card; moving a card to Done does not close the issue.
  Both steps are needed, in that order.
- Some boards have a built-in workflow that auto-closes on Done, or auto-moves to Done on
  close. When that fires, a second attempt errors with "already closed". That is the
  automation doing its job, so leave the intended comment with
  `gh issue comment` and move on.

## Record the IDs once

Project v2 mutations take node IDs, never names. Store these in the project's instruction
file:

```
Project:      number, owner, project id (PVT_...)
Status field: field id (PVTSSF_...), and the option id of every column
Priority:     field id and option ids, if the board uses one
```

Reading them back on every session costs several API calls and breaks when the network
is flaky. Re-read them only when a mutation fails, since that failure means the board was
reconfigured and the file needs updating.

`gh project` covers listing and simple item operations, while setting a single-select
field generally needs `gh api graphql` with `updateProjectV2ItemFieldValue`. Either is
fine. Prefer whichever the project's permission allowlist already permits, since an
unallowed command stalls the run waiting for approval.

## Hierarchy

In order of preference:

1. **Sub-issues**, the native parent/child link. Children stay full issues with their own
   status while the parent shows progress. This covers both the epic and the subtask
   pattern, so distinguish them with an `epic` label instead of a different mechanism.
2. **A task list in the parent body**, using `- [ ] #42` checkboxes that render as tracked
   items. This works everywhere, but the parent needs editing whenever children change.
3. **A milestone**, which groups issues by release. Do not use it as an epic substitute.

Blocking relationships have no native type, so state them in the body as `Blocked by #12`.

For the epic sweep in `references/blockers.md`, counting children depends on which of the
three is in use. Sub-issues expose a `subIssues` connection in the GraphQL API, queryable
directly. A task list has no query, so open the parent body and read the checkbox state.
Confirm which mechanism an epic uses before assuming the query works. An epic
created before sub-issues existed on the repository, or one whose children were linked by
hand in prose, returns an empty `subIssues` list even with real children elsewhere.

## Creating an issue

Body in GitHub-flavoured markdown, acceptance criteria as `- [ ]` so they render as a
progress bar.

If `.github/ISSUE_TEMPLATE/` exists, read and follow the matching template. A team that
wrote templates expects them to be used, and its automations often depend on the fields
inside.

Apply labels the repository already uses; read the label list instead of inventing
near-duplicates of existing ones.

## Branch, push, review

Use one branch per issue, named so the issue number stays visible. For an epic, one branch
with a commit per coherent section reviews far better than one squashed lump.

At handoff: push the branch, do not merge, move the card to In review, comment on the
issue with the change summary.

Open a PR only if the project works that way, and open it as a draft without requesting
reviewers. Marking a PR ready notifies people, which makes it an externally visible action.

Note that review does not happen in the PR. The gate is the owner trying the change on a
preview, and the acceptance report belongs in an issue comment they will read. A PR here
serves as a merge mechanism and a diff record, nothing more.

Closing keywords such as `Fixes #12` close the issue **on merge**. Including them is fine,
and merging remains the owner's call.

## Preview environments

If the host builds a preview per branch, include the URL in the review comment. A reviewer
who has to construct it themselves usually will not.

Some hosts do not surface preview URLs in their UI and require assembling them from a
deployment or version ID. Record the URL pattern and where to find that ID in the
project's instruction file, so it does not have to be rediscovered each time.

After pushing a non-production branch, confirm production is untouched if the host has any
history of misconfigured build settings. A push that redeploys the live site without
warning is the expensive kind of surprise.
