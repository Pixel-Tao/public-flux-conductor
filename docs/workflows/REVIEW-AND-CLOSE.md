# Review and Close Workflow

## Purpose and scope

This workflow defines the procedure from receiving the completion report of a
worker to judging `Done` for each target issue and closing the execution plan
issue.

Higher-level policy follows [the operating model](../core/OPERATING-MODEL.md).
State and pull request rules follow
[the GitHub tracker guide](../integrations/TRACKER-GITHUB.md). The execution
responsibility boundary follows
[the orchestrator guide](../integrations/ORCHESTRATOR.md). Approval and the
first dispatch follow [the plan and dispatch workflow](PLAN-AND-DISPATCH.md).
Refining the next development item of a roadmap-managed target follows
[the loop engineering workflow](LOOP-ENGINEERING.md).

This workflow starts when the completion report of the target work item task
arrives. Each target issue is reviewed and completed independently, so an issue
that meets the completion gate can be merged and moved to `Done` even while
another issue of the same execution plan fails or waits.

Every managed issue must have a linked pull request and a merged result. A
documentation or a configuration change is no exception. Do not move an issue
that has no pull request to `Review` or to `Done`.

This document covers neither the actual commands of an orchestration tool or of
the GitHub CLI, nor automatic merging and state automation, nor a redefinition
of the target repository rules.

## Review entry gate

The coordinator compares the completion report with the actual GitHub state and
confirms the following conditions.

- The target issue matches the approved execution plan, the work item task, and the dispatch.
- The issue is currently `In Progress`.
- The implementation result is described for the approved scope and for each completion criterion.
- The main changed files and a change summary exist.
- The commit and the branch are identified.
- A pull request that links the target issue exists, and the linked pull request relation is confirmed.
- The tests, lints, builds, or manual verifications that were run, and their results, exist.
- The reason and the impact of a verification that could not run exist.
- The remaining risks, the follow-up work, and the unresolved questions are recorded.

When any one of them is missing, keep `In Progress` and request the missing
evidence. Create a new dispatch when a further code change or a verification run
is needed. Move the project item to `Review` only after every condition is met.

A completion report is the evidence that a dispatch ended and that the review
for `Review` entry can begin. It is not sufficient evidence for `Review` or for
`Done`, as defined in
[pull requests and completion](../integrations/TRACKER-GITHUB.md#pull-requests-and-completion).
The contents of the report follow
[completion reports](../integrations/ORCHESTRATOR.md#completion-reports).

## Review and independent review

The coordinator reviews in the following order.

1. Compare the commit, the branch, the pull request, and the verification results of the completion report with the actual values in GitHub.
2. Confirm that the pull request changes correspond to the approved scope and to the issue completion criteria.
3. Apply the `AGENTS.md`, branch, review, and check rules of the target repository.
4. Confirm correctness, regression risk, and verification evidence with the review method of the workflow baseline.
5. Judge whether an independent review is needed, and record the result on the pull request or on the issue.

Use an independent review worker whenever the target repository requires one, or
whenever the work falls into one of the following high-risk categories.

- Authentication, permissions, secrets, or personal data protection
- Data loss, data integrity, a database schema, or a migration
- A public API or backward compatibility
- An operational deployment or production infrastructure

Assign an independent review to a worker other than the implementer, as a new
dispatch of the same issue and work item task. Create no new GitHub issue and no
hidden work item task. The review worker reports findings and evidence only, and
changes neither the approved scope nor the code on its own initiative.

When `orchestrator.name` is `none`, the same agent performs the independent
review as a review step separated from the implementation, as defined in
[no-orchestrator mode](../integrations/ORCHESTRATOR.md#no-orchestrator-mode).
That agent records on the target issue that the review ran as a separate step.

Record a blocking finding on the pull request or on the issue. Return the item
to `In Progress` and create a new implementation dispatch when an implementation
fix inside the scope is needed. A non-blocking finding inside the scope may be
recorded as a remaining risk. Separate follow-up work outside the scope into a
new issue, and do not expand the current pull request scope.

## Merge and Done gate

The coordinator may merge a pull request inside the approved execution plan
scope without a separate merge approval, except in the high-risk case below.
Confirm all of the following before the merge.

- The completion criteria of the issue are met.
- The linked pull request targets the correct target repository and the merge target branch that the repository rules define.
- The reviews and the checks that the target repository requires pass.
- The required independent review is complete, and its blocking findings are resolved.
- The evidence of the required tests, lints, builds, or manual verifications is confirmed.
- The change does not leave the approved scope.
- The remaining risks and the follow-up work are recorded.
- The reason for a non-obvious change is recoverable from the permanent record, as defined in [recoverable understanding](../core/OPERATING-MODEL.md#recoverable-understanding).

Get a separate user confirmation before merging when the change falls into one of
the high-risk categories in
[review and independent review](#review-and-independent-review). The execution
plan approval covers the work rather than the merge for those categories, and the
same agent performs the independent review when `orchestrator.name` is `none`.
Record the confirmation on the pull request.

Recognize an exception to a required check or verification only when the user
explicitly approved the exception target, the reason, and the impact on the
target issue or on the pull request. Keep `Review` and do not work around a
repository protection rule that blocks the merge.

After the merge, the coordinator queries again whether the pull request was
actually merged into the target branch. Move the project item to `Done` only
after confirming the merge commit and its time, the linked pull request, and the
completion evidence, then close the target issue when it is open. Do not skip
the same completion verification when another actor merged first or when the
issue closed automatically. The `Done` criteria follow
[completion and verification](../core/OPERATING-MODEL.md#completion-and-verification).

## State handling

| Situation | Handling |
|---|---|
| A completion report, a linked pull request, or required evidence is missing | Keep `In Progress` and request the missing evidence |
| A check or a review result is pending | Keep `Review` |
| A code fix inside the scope is needed | Return to `In Progress` and create a new implementation dispatch |
| The scope or the completion criteria must change | `Blocked`, record the change in GitHub, void the existing approval, and get approval again |
| The pull request closed without a merge and can be fixed | Return to `In Progress` and create a new implementation dispatch |
| The pull request closed without a merge and a user decision is needed | `Blocked` |
| The pull request merged but the completion evidence is missing | Keep `Review` |
| The issue closed as `not planned` | Do not set `Done`, and archive the item from the project |
| A `Done` issue is reopened | Review the completion criteria again, then `Ready` |
| Every completion gate is met | `Done` |

An item that became `Blocked` because of a scope change returns to `Ready` after
the user decides, and is included in a corrected or a newly written execution
plan that is approved again. Do not start a new dispatch on the existing
approval.

The state values follow
[work item states](../core/OPERATING-MODEL.md#work-item-states), the transition
actors follow
[state transitions](../integrations/TRACKER-GITHUB.md#state-transitions), and
the exception cases follow
[exception handling](../integrations/TRACKER-GITHUB.md#exception-handling). A
new implementation dispatch after a failure follows
[failure and retry](../integrations/ORCHESTRATOR.md#failure-and-retry).

## Permanent records

Leave the following in the completion comment of the target issue.

- The execution plan issue and the target issue
- The work item task and the final dispatch
- The linked pull request, the merge commit, and the merge time
- The confirmation result for each completion criterion
- The checks that were run and their results, and the approved exceptions
- The review evidence, when an independent review was required
- The remaining risks and the follow-up issues

Record a transient worker message and a heartbeat in the orchestrator only.
Record in GitHub the decisions that a recovery needs, such as the scope, the
completion criteria, the exceptions, and the handling of incomplete work.

## Closing the execution plan

The coordinator handles the completion of each issue independently of the state
of the other included issues. Before the execution plan issue is closed, every
included issue must be settled as one of the following.

- `Done` after meeting the completion gate
- An incomplete result that states the current GitHub state, the reason it is incomplete, the follow-up action, and the conditions for the next execution

The final comment of the execution plan summarizes the result and the link of
each issue, the complete and incomplete counts, the main risks, and the
follow-up issues. Do not treat an incomplete result as `Done`, and do not change
it to `Done`. Get a new or a corrected execution plan approved to continue the
remaining work.

Close the execution plan issue after confirming the permanent records, then let
the orchestrator end and clean up the execution batch, the dispatches, and the
workspaces, as defined in
[coordinator supervision](../integrations/ORCHESTRATOR.md#coordinator-supervision).
The execution plan, the issues, the pull requests, and the completion comments
in GitHub must be enough to recover the result even when orchestrator state is
lost.

## Returning to the development cycle

When the execution plan is linked to a roadmap management issue, the coordinator
records the following on that issue after closing the execution plan and
cleaning up the orchestrator resources.

- The completed target issues and their pull requests, and a link to the final execution plan result
- The completion criteria and the verification evidence
- The remaining risks, the blockers, and the follow-up work
- The result achieved for the current milestone, and the conditions for starting the next one

A change to the product, technical, or roadmap source documents of the target
repository must be performed inside an approved issue and a worker pull request.
The coordinator does not modify a target repository file without a separate
dispatch, and confirms only whether the change is reflected and what its link
is.

When overall completion criteria remain, prepare the next development cycle by
refining an existing `Backlog` issue or by registering a follow-up issue inside
the approved scope, following the roadmap loop engineering workflow. Waiting for
an approval, an external dependency, or a user decision is not treated as an
ending, so record the resume conditions on the roadmap management issue.

Close the roadmap management issue only when one of the following is confirmed.

- The overall completion criteria are met.
- The user explicitly stopped the work.
- The target repository is inactive.
- The user decided that a blocker ends the work.

Record the closing evidence and the last state before that close.

## Exit verification

Confirm the following before ending the workflow.

1. The completion report, the linked pull request, and the verification evidence match the actual GitHub state.
2. Every entry gate was met before `Review`.
3. The independent review that the target repository rules or a high-risk category required is complete.
4. Waiting, an implementation fix, and a scope change are reflected in the states of the decision table.
5. The completion criteria, the reviews, the checks, the verification, the scope, and the risks were confirmed before the merge, together with the separate user confirmation a high-risk change requires.
6. A verification exception, if any, carries the evidence of an explicit user approval.
7. The item moved to `Done` only after the actual merge of the pull request was confirmed again.
8. The pull request, merge, verification, review, and risk evidence is recorded on the target issue.
9. Every included issue of the execution plan carries a completion result or an explicit incomplete result.
10. The orchestrator execution resources were cleaned up after the final execution plan summary was left in GitHub.
11. The execution result, the verification, the risks, and the milestone result are reflected in the management issue when the target is roadmap-managed.
12. The next development cycle, an explicit wait, or an ending was decided according to the overall completion criteria.
