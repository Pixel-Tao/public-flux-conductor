# Plan and Dispatch Workflow

## Purpose and scope

This workflow reviews the `Ready` issues of the central project, groups them
into an execution plan, and converts the plan into execution batches, work item
tasks, and dispatches after the user approves it.

Higher-level policy follows [the operating model](../core/OPERATING-MODEL.md).
State and approval follow
[the GitHub tracker guide](../integrations/TRACKER-GITHUB.md). The execution
responsibility boundary follows
[the orchestrator guide](../integrations/ORCHESTRATOR.md). Refining the next
development batch from the roadmap, and reflecting execution results back into
it, follow [the loop engineering workflow](LOOP-ENGINEERING.md).

This workflow starts at the `Ready` review and ends once the results of the
first dispatch are recorded. The coordinator may analyze `Ready` items on its
own initiative and draft an execution plan, but does not dispatch before a valid
approval. The [fast path](../core/OPERATING-MODEL.md#fast-path) does not enter
this workflow. This document covers neither the actual commands of an
orchestration tool nor the procedure after `Review`.

## Entry conditions

The coordinator confirms the following for each candidate issue.

- The issue is explicitly added to the central project.
- The issue is currently `Status=Ready`.
- The issue still meets every criterion in [ready entry criteria](../integrations/TRACKER-GITHUB.md#ready-entry-criteria), except as refined below.
- A remaining dependency is resolved, or a preceding issue in the same execution plan satisfies it.
- The issue is small enough to finish as one independent work item task.

Move an issue whose information is insufficient to `Backlog`. Move an issue with
an external dependency that the execution plan cannot resolve to `Blocked`.
Record the reason on the issue. Split an issue that is too large into sub-issues
of the target repository before approval.

An issue refined by roadmap loop engineering is a candidate only when it is a
`Ready` item that meets every condition above. This workflow adds no separate
entry exception for it.

## Candidate selection and batching

Use `Priority` as the default order, and do not treat it as an absolute order.

| Judgment factor | Rule |
|---|---|
| User instruction | Reflect it first, within the range that satisfies the entry conditions |
| Blocking dependency | Select the preceding issue first, and place the dependent issue in a later step or in the next execution plan |
| Shared goal | Group the issues that produce one reviewable outcome |
| Change conflict | Adjust the order or split the execution plan when the issues can touch the same file, branch, migration, or shared resource |
| `Priority` | Select in `P0` to `P3` order when the other factors are equal |

- One execution plan can include issues from several repositories.
- Group only the items that share a goal, that have a direct dependency, or that carry a possible conflict which must be coordinated together.
- Set no fixed limit on the number of issues.
- Split the execution plan when it is hard to review at once, or when one completion goal cannot describe it.
- Leave an excluded candidate in `Ready` and record the exclusion reason in the execution plan.
- Do not create an empty execution plan issue when no executable included item remains.

## Writing and approving the execution plan

The coordinator creates one execution plan issue in the coordination repository
defined by `github.coordination_repo` in
[the environment file](../env/ENVIRONMENT.example.md). The `at a glance` format
and the detail items of the body follow
[execution plans and approval](../integrations/TRACKER-GITHUB.md#execution-plans-and-approval).

Move the included project items to `Planned` and keep that state while approval
is pending. The state values follow
[work item states](../core/OPERATING-MODEL.md#work-item-states).

Approve an execution plan as a whole only. Show the current approval text by
itself and tell the user to enter it exactly. The user leaves that text as a
GitHub comment or enters it in conversation. An agent that receives the
conversational instruction verifies the conditions in
[execution plans and approval](../integrations/TRACKER-GITHUB.md#execution-plans-and-approval)
and, only when the verification succeeds, leaves a comment with exactly that
text on the issue and continues this workflow. When the verification fails, the
agent performs neither the comment nor the dispatch, and reports the reason.

Before approving, the user may request an understanding briefing or check of
the execution plan, as defined in
[understanding support](../core/OPERATING-MODEL.md#understanding-support).

A valid approval record is a comment by an approver whose text exactly matches
the current approval text and which was left after the last edit of the
execution plan body.
Immediately before creating the execution batch, confirm again the approval
route, the comment author, the exact comment text, the time of the last body
edit, the included issues, the `Planned` state, and the absence of a new blocker.

Compare the last update time of each included target issue with the time of the
approval comment. A target issue updated after the approval comment is treated as
changed, and the approval is void until the change is confirmed to leave the
scope and the completion criteria untouched. Get approval again when either
moved.

An approval is void when the execution plan is rejected, or when its body, its
included issues, its scope, or its completion criteria change. Return the
related items to `Ready`, update the body, and get the whole execution plan
approved again.

## Converting to orchestrator work

The correspondence between GitHub items and orchestrator execution units follows
[tracker and orchestrator mapping](../core/OPERATING-MODEL.md#tracker-and-orchestrator-mapping).

Do not add a work item task inside the orchestrator without an approved issue.
When new work turns out to be needed, leave it as a new GitHub issue and do not
expand the scope of the current execution plan.

| Condition | Handling |
|---|---|
| A blocking work item task exists | Do not dispatch until the preceding task completes |
| Different repositories, with no dependency and no shared resource | Parallel dispatch is possible within the actual capacity of the orchestrator |
| The same repository, but isolated workspaces and no overlapping change range | Parallel dispatch is possible only when the conflict evidence has been checked |
| The issues can change the same file, branch, migration, lockfile, or deployment resource | Decide an order and serialize the dispatches |
| Orchestrator capacity is insufficient | Wait in `Planned` and do not record it as a failure |

Set no fixed number of parallel tasks.

When `orchestrator.name` is `none`, this table selects no parallel group.
Execution is serial and the limit is one, as defined in
[no-orchestrator mode](../integrations/ORCHESTRATOR.md#no-orchestrator-mode).

### Execution limits and budget

The parallel decision table judges the conflict and the order of each individual
dispatch. This section defines the limits of the whole execution environment
that those judgments use. The insufficient capacity row of that table is the
state handling for a dispatch that hits a limit defined here, and this section
does not define that handling again.

The execution environment must be able to state the following limits.

- The number of concurrent execution batches and dispatches, overall and per repository
- The number of changes that can run at once in the same repository
- The time, token, and cost limits per work item task and per execution plan
- The retry count, and the threshold that requires a new user confirmation
- The spare capacity reserved for `P0` work, and the safe waiting criteria for existing work

Actual limit values differ by machine and by the nature of the work, so record
them in a local environment record rather than fixing them in this repository.
That record is an untracked local file the operator keeps outside this
repository, because it holds host paths and other values a public repository
must not carry.
The coordinator confirms the values in the current execution environment
immediately before a dispatch.

When no limit exists or a limit cannot be confirmed, use the conservative rule
of running only one changing dispatch in the same repository. This is a lower
bound that applies until the values are confirmed, and it sets no new fixed
number of parallel tasks.

When `orchestrator.name` is `none`, execution is serial and the limit is one,
whatever the confirmed values are.

Apply the following when a limit is reached.

- Do not treat budget exhaustion as completion or as failure. Stop new dispatches, record the results so far, the remaining scope, and the resume conditions on the execution plan issue, and leave an issue that could not start waiting in `Planned`, the same as the insufficient capacity case in [partial failure and state handling](#partial-failure-and-state-handling).
- Do not force a live worker to terminate to free spare capacity, even when priorities change. Worker termination and release follow [coordinator supervision](../integrations/ORCHESTRATOR.md#coordinator-supervision).

## Dispatch procedure

1. Confirm the approval gate again, together with the [dispatch preconditions](../integrations/ORCHESTRATOR.md#dispatch-preconditions-and-worker-handoff), including the settled [UI/UX mockup](../integrations/ORCHESTRATOR.md#uiux-mockup-decision) when the work changes a screen.
2. Create one execution batch for one execution plan.
3. Create one work item task per target issue, and deliver the issue link, the scope, the completion criteria, the repository instructions, and the verification method.
4. Build the work item task graph from the GitHub dependencies and the approved execution order.
5. Choose the first dispatch targets with the parallel decision table.
6. Assign an isolated workspace and a worker to each work item task and dispatch it. Worker selection follows [worker capability routing](../integrations/ORCHESTRATOR.md#worker-capability-routing).
7. Move only the issues whose dispatch creation is confirmed to `In Progress`.
8. Record the execution batch, the issue to work item task mapping, the work item task to dispatch mapping, and the start results as a comment on the execution plan issue. In no-orchestrator mode, record the issue to work item task mapping and the start results only.

When `orchestrator.name` is `none`, step 5 selects one work item task at a time
in dependency order, because execution is serial and the limit is one.

## Partial failure and state handling

| Situation | Handling |
|---|---|
| Execution batch creation fails | Keep every included issue in `Planned` and record the cause on the execution plan issue |
| Work item task creation fails | Keep an issue whose task was not created in `Planned`, and keep the tasks that were created independently |
| Dispatch creation fails | Keep that issue in `Planned`, and keep an issue that started independently in `In Progress` |
| A preceding dispatch fails | Do not dispatch the dependent work item task, and keep it in `Planned` |
| Orchestrator capacity is insufficient | Do not treat it as a failure, and wait in `Planned` |
| A new external dependency is found | Link the native GitHub dependency and move that issue to `Blocked` |
| The approved scope needs to change | Stop the dispatches that have not started, return the related items to `Ready`, and get approval again |

Do not cancel a successful independent work item task because another task
failed to start. Reflect in GitHub only the steps actually confirmed, as defined
in [state transitions](../integrations/TRACKER-GITHUB.md#state-transitions).

## Permanent records

Leave the following in the start comment of the execution plan issue.

- A reference to the valid approval comment
- The execution batch identifier or link. Not applicable in no-orchestrator mode.
- The work item task identifier or link for each target issue
- The identifier or link of each created dispatch. Not applicable in no-orchestrator mode.
- The `In Progress`, `Planned`, or `Blocked` start result of each issue
- The reasons for waiting, failure, and serialization, and the conditions for the next start

Add no separate project field. Record a transient heartbeat and a worker message
in the orchestrator only.

This workflow converts only the approved current issues into work item tasks,
and creates no task from an estimated future development item. When the
execution result passes [the review and close workflow](REVIEW-AND-CLOSE.md) and
overall completion criteria remain, refine the follow-up issues in the next
development cycle of roadmap loop engineering.

## Exit verification

Confirm the following before ending the workflow.

1. The approval comment exactly matches the current approval text, and it was left after the last body edit.
2. One execution plan corresponds to one execution batch. Not applicable in no-orchestrator mode.
3. The included issues and the work item tasks correspond one to one.
4. The work item task graph preserves the GitHub dependencies and the approved order.
5. Parallel work item tasks are isolated, and the tasks that can conflict are serialized. Not applicable in no-orchestrator mode.
6. Only the issues whose dispatch succeeded are `In Progress`.
7. An issue that could not start carries a `Planned` or a `Blocked` reason.
8. The execution plan issue records the execution batch, work item task, and dispatch mapping, and the start results. In no-orchestrator mode it records the work item task mapping and the start results only.
