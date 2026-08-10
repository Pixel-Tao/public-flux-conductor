# GitHub Tracker Guide

## Purpose and scope

The central project is the permanent single reference for work across several
repositories. This document defines the fields, the states, the approval
records, the pull request links, and the exception rules for an issue that the
central project tracks.

Higher-level policy follows [the operating model](../core/OPERATING-MODEL.md).
This document does not cover the detailed procedure of execution batches, work
item tasks, and dispatches.

## Managed items

The central project is defined by `github.project_url` in
[the environment file](../env/ENVIRONMENT.example.md) and is owned by the
account named in `github.owner`. The repositories named in
`github.managed_scope` are the default target scope. Only an issue explicitly
added to the central project is a managed item.

The [fast path](../core/OPERATING-MODEL.md#fast-path) creates no central
project item, no target issue, and no execution plan issue. Do not apply the
fast path to an issue the central project already tracks. A commit or a pull
request produced on the fast path follows the user request and the target
repository instructions.

- The user or the coordinator adds an issue to the central project directly.
- Register an issue from a repository outside `github.managed_scope` one at a time, and only while its [project profile](../workflows/LOOP-ENGINEERING.md#common-activation-criteria) is `Active`.
- Do not use a repository auto-add workflow.
- Do not treat an issue as an execution target merely because it exists in a repository.
- Do not add a pull request as a separate project item. Track it through the linked pull request relation of its issue.
- Do not use a draft item as an execution target. Create the needed work as a target repository issue first, then add it.

## Field contract

The project manages two work fields directly, `Status` and `Priority`. Use
project local fields rather than organization issue fields, so that issues from
several organizations are handled consistently.

### Status

Use the values defined in
[work item states](../core/OPERATING-MODEL.md#work-item-states) unchanged.

```text
Backlog
Ready
Planned
In Progress
Review
Blocked
Done
```

### Priority

| Value | Meaning |
|---|---|
| `P0` | An outage, a security issue, or a data risk that needs an immediate response |
| `P1` | Work to handle first in the next execution batch |
| `P2` | Ordinary work, and the default for a new item |
| `P3` | Work that is explicitly deferred |

Register the options in `P0` to `P3` order. Use the existing GitHub fields for
repository, assignee, labels, linked pull request, parent issue, and sub-issue
progress. Do not create an `Agent`, `Run`, `Risk`, or `Estimate` field.

## Default views

### Queue

- Layout: table
- Filter: `is:issue status:Backlog,Ready,Planned`
- Sort: `Priority` ascending
- Purpose: triage of new items, selection of execution candidates, and review of items waiting for an execution plan

### Execution

- Layout: board
- Filter: `is:issue status:Ready,Planned,"In Progress",Review,Blocked`
- Group: `Status`
- Sort: `Priority` ascending
- Purpose: review of the current execution flow and its bottlenecks

Do not create a view dedicated to completed items. Query them with a project
filter when needed.

## Automation

The only permitted built-in automation sets the default `Status` of a new item.

- When an item is added to the project, set `Status=Backlog`.

`Priority` carries a field default rather than a built-in automation. Set `P2` as
the default value of the `Priority` single select field. Set `Priority=P2` during
the issue registration procedure only when an existing project cannot carry that
default.

Do not use the following automations.

- Repository auto-add
- `Status=Done` when an issue closes
- `Status=Done` when a pull request merges
- Automatic issue close when `Status` changes to `Done`
- Automatic archiving

An issue close or a pull request merge alone cannot guarantee that the
completion criteria are met, so the coordinator sets `Done` after verification.

## Natural language issue registration

### Registration intent and boundaries

Only a natural language request that explicitly asks for an issue grants
permission to create one. Examples are `register this as an issue`,
`create a GitHub issue for this`, and `leave this as an issue`. Judge an
equivalent phrase in the language named by `language` in
[the environment file](../env/ENVIRONMENT.example.md) the same way. What matters
is that the request explicitly asks for an issue, not the exact words.

- Do not create an issue for a request that only describes a problem or an idea, or that only asks for an opinion.
- Do not infer permission to create an issue from `fix this`, `add this`, or `implement this` alone.
- Handle an implementation request without an issue when it meets the [fast path](../core/OPERATING-MODEL.md#fast-path) conditions.
- Ask the user whether to register an issue when an implementation request belongs to the standard workflow and no issue exists.
- When the user asks for registration and execution together, register the issue and then follow the standard workflow rather than the fast path.
- When the user asks for registration only, report the project registration result and stop without implementation, execution plan writing, or dispatch.

### Target repository

The agent determines the target repository in the following order.

1. An `owner/repository` name or a GitHub URL the user stated
2. A repository named unambiguously in the current request
3. The single candidate repository in the conversation and project context

When there is no candidate or more than one, ask only about the target
repository before creating the issue, and do not guess. The target repository
must meet the managed item conditions in this document. When the repository
does not exist, is archived, has issues disabled, or grants no write
permission, create neither the issue nor the project item, and report the
reason.

When one request contains several outcomes that can be completed
independently, do not create several issues on your own initiative. The agent
presents a split proposal with the goal of each issue and creates them after
user confirmation. Keep a tightly coupled change that one set of completion
criteria verifies as a single issue.

### Type

The agent judges the type from the desired outcome.

- `bug`: expected existing behavior fails, or an error or regression appears
- `feature`: a new capability or user flow that does not exist yet is added
- `improvement`: existing behavior changes, or performance, refactoring, documentation, or operations improve

When the type choice changes neither the scope nor the completion criteria,
classify an ambiguous request as `improvement` and do not ask. Ask the user
only when the requirements differ by type. Apply an existing label of the
target repository that carries the same meaning, but do not create a new label
or a new issue type. When the target repository has its own issue template and
writing rules, that repository's rules win.

### Duplicate check

Before creating an issue, search the open issues of the target repository for
the same problem, goal, and desired outcome.

- Reuse the existing issue and create no new issue on a clear duplicate.
- Show the candidate links and ask whether to create a new issue when the match is similar but unclear.
- Add an existing issue that is not in the central project, then confirm `Status=Backlog` and `Priority=P2`.
- Report to the user without changing the current state or priority when the existing issue is already in the central project.
- Report the failure and create no new issue when the duplicate search fails.

### Title and body

Write the title as one sentence that shows the outcome to be achieved, with no
type prefix.

Put the following as a short list at the top of the body.

- Type
- Problem or purpose
- Desired outcome
- Completion criteria
- Verification method

After that, record the background, the current state, the included and excluded
scope, the reproduction steps, the impact, and the rationale, using only
information the request actually contains. For a bug, separate the actual
behavior from the expected behavior. For a feature, center the body on the user
problem to solve and the desired behavior. Do not guess an item with no
information, and do not leave an empty section.

Register the issue as `Backlog` even when it does not meet the ready entry
criteria, and record the missing information as specific questions in a
`needs confirmation` list. Ask before creation only about information that could
make the issue itself wrong, such as the target repository or the goal of the
request.

### Creation and project registration

1. Confirm the registration intent and the target repository.
2. Search the open issues for a duplicate.
3. Write the title and the body from the natural language request, then create the issue in the target repository or reuse the existing issue.
4. Add the new issue, or the existing issue that is not tracked yet, to the central project.
5. Confirm `Status=Backlog` and `Priority=P2` on a new project item, and set them directly when the automatic default did not apply. Keep the current fields of an existing project item.
6. Report the issue link, the judged type, the project state, and the remaining confirmation items to the user.

When issue creation fails, create no project item. When the project addition
fails after the issue was created, neither delete the created issue nor create
a duplicate, and report the link and the cause of the failure. When a project
field setting fails, keep the issue and the project item, and report the
confirmed current state and the correction needed.

## Roadmap follow-up issues

Ordinary issue creation requires an explicit natural language registration
request from the user. As an exception, when the user has explicitly started
[roadmap loop engineering](../workflows/LOOP-ENGINEERING.md) and the permitted
scope is recorded in the roadmap management issue of the coordination
repository, the coordinator may create follow-up issues and refine `Backlog`
issues within that scope.

An explicit start request includes permission to create one roadmap management
issue that passed the duplicate check and the start condition check. Do not add
the roadmap management issue to the central project, and reuse an open issue
with the same goal when one exists.

Target repository determination, type judgment, duplicate check, title and
body, and project registration for a follow-up issue follow the natural
language issue registration procedure in this document unchanged. Leave a newly
created issue, or one tracked for the first time, at `Status=Backlog` and
`Priority=P2`, and keep the current fields of an existing project item.

Move to `Ready` only the issues the next execution batch needs, after checking
the ready entry criteria. A scope or completion criteria change after `Ready`,
follow-up work outside the roadmap, and execution all follow the existing state
transitions, the re-approval rule, and the execution plan approval gate.
Permission to create follow-up issues alone does not start implementation or
dispatch.

## Ready entry criteria

Confirm the following before moving an issue to `Ready`.

- The goal is stated in one sentence.
- The included scope and the excluded scope are separated.
- Observable completion criteria exist.
- A verification to run or a manual check to perform exists.
- `Priority` is set.
- No unresolved blocking dependency remains.
- The target repository and the instructions to apply are confirmed.

Keep the issue in `Backlog` when a criterion is missing, and record what is
missing in an issue comment. Link a native GitHub issue dependency when the
issue waits on another issue. Split an issue into sub-issues when it cannot
finish as one independent piece of work.

## Execution plans and approval

Record an execution plan as one issue in the coordination repository defined by
`github.coordination_repo` in
[the environment file](../env/ENVIRONMENT.example.md). Put a summary in the
following format at the top of the body, regardless of the body length.

```markdown
## At a glance

- Goal: ...
- Target issues: ...
- Execution order and parallel groups: ...
- Completion criteria: ...
- Main risks: ...
```

Include the following detail after the summary.

- The execution goal and the overall completion criteria
- Links to the included target issues and the reason each was selected
- Issues excluded or deferred, and why
- Dependencies and the execution order
- Groups that can run in parallel
- Possible conflicts per repository, and what must be serialized
- Expected risks and how to check them
- The required verification and the pull request requirement for each issue

Move the target issues included in an execution plan to `Planned`.

A valid approval record is a comment left on the execution plan issue after the
last edit of its body, whose text is exactly the phrase defined by
`github.approval_phrase` in
[the environment file](../env/ENVIRONMENT.example.md). The user leaves this
comment on GitHub directly, or instructs the agent with exactly
`approve execution plan #<number>`. Any other wording is not an approval
instruction.

An agent that receives the conversational instruction confirms the following
conditions.

- The number points at an issue in the coordination repository.
- That issue is an execution plan whose target issues and execution scope are identifiable.
- The included target issues are in `Planned` and carry no new blocker.

When all conditions hold, the agent leaves a comment whose text is exactly the
phrase defined by `github.approval_phrase` and starts the existing dispatch
procedure. When any condition fails, the agent performs neither the comment nor
the dispatch, and reports the reason to the user. This conversational
instruction does not bypass the approval gate. It is a shortcut that produces
the same approval comment.

When the body, the target issues, the scope, or the completion criteria change
after approval, void the approval, return the related items to `Ready`, and get
approval again.

## State transitions

| Current state | Next state | Reason | Actor |
|---|---|---|---|
| New item | `Backlog` | Added to the project | GitHub automation |
| `Backlog` | `Ready` | Ready entry criteria met | User or coordinator |
| `Ready` | `Planned` | Included in an execution plan | coordinator |
| `Planned` | `In Progress` | Valid approval and a successful dispatch | coordinator |
| `Planned` | `Ready` | Execution plan rejected, changed, or cancelled before dispatch | coordinator |
| `In Progress` | `Review` | Completion report and linked pull request confirmed | coordinator |
| `In Progress` | `Ready` | Approval voided by an approved scope or completion criteria change | coordinator |
| `Review` | `Done` | Pull request merged and required verification confirmed | coordinator |
| `Review` | `In Progress` | An implementation fix inside the approved scope is needed | coordinator |
| Any non-`Done` state | `Blocked` | Dependency, repeated failure, or a pending user decision | coordinator |
| `Blocked` | Last valid state | Blocking cause resolved | coordinator |
| `Blocked` | `Ready` | Blocked by an approved scope or completion criteria change | coordinator |
| `Done` | `Ready` | Issue reopened and criteria reviewed again | coordinator |

When a failure happens between a GitHub state change and execution, reflect
only the steps actually confirmed. When the failure happens before the dispatch
is created, keep `Planned`, or cancel the execution plan and return the items to
`Ready`.

## Pull requests and completion

- Link the target issue from the pull request description with a closing keyword.
- Use the full `OWNER/REPOSITORY#NUMBER` form when the pull request resolves an issue in another repository.
- Confirm the linked pull request relation manually when the pull request does not target the default branch and the closing keyword does not work.
- A completion report is evidence for entering `Review` only. It is not evidence for `Done`.
- Confirm the issue completion criteria, the linked pull request, the required checks, the pull request merge, and the record of remaining risks before `Done`.

## Exception handling

- Required information missing: keep `Backlog` and record what is missing in a comment
- New dependency found: link a native dependency and set `Blocked`
- Execution plan changed: void the approval and return the related items to `Ready`. When a user decision is needed, keep `Blocked` until the decision, then return to `Ready`
- Pull request closed without a merge: set `In Progress` when it can be fixed, or `Blocked` when a decision is needed
- Issue closed as `not planned`: do not set `Done`, and archive the item from the project
- Issue reopened: review the completion criteria again, then set `Ready`
- Orchestrator state lost: change no GitHub state, and recover the new execution state from the approved execution plan and the issues

## Creating the central project

Every step below needs the `project` token scope, which `gh auth login` does not
grant by default. Run `gh auth refresh -s project` before starting.

Follow these steps when no central project exists yet.

1. Create the project under the account named in `github.owner`.
2. Register the `Status` single select field with the seven options listed in the field contract.
3. Register the `Priority` single select field with its four options, in `P0` to `P3` order, then set `P2` as its default value.
4. Create the Queue view with the layout, the filter, and the sort that the default views section defines.
5. Create the Execution view with the layout, the filter, the group, and the sort that the default views section defines.
6. Enable the built-in automation that sets `Status=Backlog` on a newly added item.

Record the resulting project URL as `github.project_url` in
[the environment file](../env/ENVIRONMENT.example.md).

## Setup verification

Confirm the following after the project is configured.

1. Adding a trial issue sets `Status=Backlog` and `Priority=P2`.
2. An issue that was not added to the project does not appear.
3. An issue close alone does not produce `Done`.
4. A linked pull request merge alone does not produce `Done`.
5. The project manages exactly two work fields directly, `Status` and `Priority`.
6. The Queue and Execution views use the defined filter, layout, and sort.

## Official references

- [Adding items to your project](https://docs.github.com/en/issues/planning-and-tracking-with-projects/managing-items-in-your-project/adding-items-to-your-project)
- [About single select fields](https://docs.github.com/en/issues/planning-and-tracking-with-projects/understanding-fields/about-single-select-fields)
- [Using the built-in automations](https://docs.github.com/en/issues/planning-and-tracking-with-projects/automating-your-project/using-the-built-in-automations)
- [Filtering projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/customizing-views-in-your-project/filtering-projects)
- [About issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/learning-about-issues/about-issues)
- [Linking a pull request to an issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)
