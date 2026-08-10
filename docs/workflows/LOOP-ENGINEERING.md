# Roadmap Loop Engineering Workflow

## Purpose and scope

This workflow turns the concept and the design of a product or a feature into
executable GitHub issues. It then reflects each completed result, refines the
next development item, and keeps advancing the roadmap.

Higher-level policy follows [the operating model](../core/OPERATING-MODEL.md).
Issue and approval rules follow
[the GitHub tracker guide](../integrations/TRACKER-GITHUB.md). Execution
responsibility follows [the orchestrator guide](../integrations/ORCHESTRATOR.md).
Each development cycle executes through
[the plan and dispatch workflow](PLAN-AND-DISPATCH.md) and
[the review and close workflow](REVIEW-AND-CLOSE.md) unchanged.

This workflow covers product and technical design, repository onboarding,
roadmap management, refinement of the next issue, and the repetition of the
development cycle. It does not cover a per-product design form, the actual
commands of an orchestration tool, repository creation automation, the
implementation of scheduled execution, dispatch without approval, or the
implementation rules of a target repository. The conditions a scheduled wake
must satisfy are a contract rather than an implementation, so
[continuous execution triggers](#continuous-execution-triggers) defines them.

The central project tracks every issue this workflow executes, so the
[fast path](../core/OPERATING-MODEL.md#fast-path) does not apply.

## Design and execution readiness

- Do not design the whole product in detail up front. Specify only up to the next verifiable vertical slice.
- Do not write the product, technical, and operations documents in one batch at the end. Update each at the step that decided it.
- Per-product design items follow the target repository instructions and its domain documents. A game, for example, can include the concept, the core loop, the content structure, and the design direction.
- Split execution issues into independent pieces of work that carry observable completion criteria and a verification method.
- Settle with the user the criteria that affect later scope, then write the first execution plan. Those criteria are the product goal, the first vertical slice, the technology stack, the architecture, and the overall completion criteria.

### New service

1. Define the product purpose, the user value, the core behavior, and the excluded scope.
2. Decide the design items the domain needs and the first verifiable vertical slice.
3. Identify the target repository. Get explicit user approval before creating a repository that does not exist. Do not infer repository creation permission from permission to start loop engineering.
4. Get a separate onboarding approval, prepare the root `AGENTS.md` and the document entry point of the target repository, then record the onboarding method and the verification evidence in the project profile in the coordination repository.
5. Verify the [common activation criteria](#common-activation-criteria) and set the project profile to `Active`. Create a target repository issue only after `Active`.
6. Settle the product documents, the technology stack, the architecture, the test baseline, the deployment baseline, and the operations baseline with the user. Record the initial decisions in the roadmap management issue.
7. Write the issues for the baseline document updates and the first development batch, then add them to the central project.
8. Move to `Ready` only the issues that meet the [ready entry criteria](../integrations/TRACKER-GITHUB.md#ready-entry-criteria), then start the development cycle.

Record in the roadmap management issue the initial product definition and the
repository creation decision made while no target repository exists yet. After
the repository becomes `Active`, reflect the product, technical, and roadmap
source documents into the target repository through approved issues and pull
requests. Keep only links and progress state in the roadmap management issue
after that.

### Onboarding an existing repository

Use this route when the repository exists but has no project profile in the
coordination repository, or has one that is not `Active`. Do not repeat
onboarding for a repository that is already `Active`. Move it to the features
and improvements route instead.

1. Identify the canonical repository, the default branch, the ownership, and whether it is archived.
2. Investigate the root `AGENTS.md` of the default branch, its linked documents, the actual code structure, the build and test commands, the deployment and operations baselines, the existing roadmap, and the open issues. Keep this investigation read-only.
3. Separate the gaps that block a safe dispatch from the gaps that roadmap development can close later.
4. Create the project profile in the coordination repository as `Blocked`, and record the repository identifier, the default branch, the verification evidence, and the blockers.
5. Close a blocker that requires a target repository change only inside a bootstrap scope that a separate onboarding approval covers.
6. Verify the [common activation criteria](#common-activation-criteria) again on the default branch, then set the project profile to `Active` when they are met.
7. Settle with the user the goal, the current milestone, the overall completion and stop criteria, and the follow-up issue creation scope, based on the current product state, the existing roadmap, and the existing issues.
8. Create the roadmap management issue. Reuse and refine only the existing issues the next verifiable vertical slice needs, or register new issues, then enter the development cycle of an existing service.

A request that states both registering an existing repository and starting the
loop includes permission for the read-only investigation and for creating the
project profile in the coordination repository. It does not expand into
permission to change target repository files, to bulk-register existing issues
into the central project, to write an execution plan, or to dispatch.

Use the existing issues and the backlog as evidence of the current state only.
Do not import all of them into the central project. Register only the items the
next development batch of the current milestone needs, after a duplicate check.
When no trustworthy canonical roadmap exists, record the initial goal in the
roadmap management issue, then create the roadmap source document of the target
repository through the first approved issue and pull request.

### Common activation criteria

The project profile is the document `docs/projects/<owner>--<repo>.md`. Its
single `Dispatch eligibility` field manages the onboarding state. The field
takes only two values, `Active` and `Blocked`. When another document says the
project profile is `Active`, it means this field. This field is onboarding
metadata in a document. It shares a name with the issue `Status` in
[work item states](../core/OPERATING-MODEL.md#work-item-states) but is a
different value.

Set `Dispatch eligibility` to `Active` for a new or an existing repository only
when all of the following conditions hold.

1. The canonical repository and the default branch are reachable, and the repository is neither inactive nor archived.
2. The root `AGENTS.md` of the default branch describes the real development rules and the required document entry points accurately.
3. The architecture, verification, and operations documents that safe work needs exist and match the current repository state.
4. The worker creation and workspace management instructions do not conflict with the orchestrator's responsibility boundaries.
5. The required build and test commands, and the operational verification method for what the change affects, can be identified.
6. The coordinator rereads the actual content of the default branch and records the verification evidence in the project profile.

A missing product description, roadmap, or backlog does not block activation by
itself, as long as a safe work scope and a verification method can be defined.
Close a missing baseline document through the first approved issue and pull
request after `Active`.

### Features and improvements for an existing service

1. Read the `AGENTS.md`, the product and technical documents, and the current roadmap of the target repository.
2. Define the change goal, the affected scope, and the excluded scope.
3. Reuse the existing baselines and design only the product, technical, and operations items that change.
4. Refine the existing issue for the next verifiable slice, or register a new issue.
5. Start the same development cycle once the ready entry criteria are met.

Reinforce the related baselines and the completion criteria when feature work
produces a new deployment unit, a new data store, a new authentication
boundary, a new public API, or a new operational responsibility. Void the
existing approval and get approval again when the approved scope or the
completion criteria change.

## Roadmap management issue

Keep one roadmap management issue in the coordination repository for each
roadmap loop engineering effort. This issue is neither a central project item
nor a work item task. It preserves the following content.

- The target repository or the creation candidate, and links to the canonical roadmap documents
- The goal of the product or the feature, and the included and excluded scope
- The current milestone, and the overall completion and stop criteria
- The approved scope within which follow-up issues may be created
- Links to the completed execution plans and issues, the current development batch, and the next candidates
- The current blockers, the main risks, and the user decisions

When an explicit start request arrives and no open roadmap management issue
carries the same goal, a new management issue may be created in the
coordination repository after all of the start conditions below are confirmed.
Reuse the existing issue rather than creating a new one when an issue with the
same goal exists. A start request does not expand into permission to add the
management issue to the central project or to create target repository issues.

The coordinator confirms the following conditions when the user states the
start of roadmap loop engineering, either in the roadmap management issue or in
conversation.

- The target repository or the creation candidate is identified.
- The goal is separated from the included and excluded scope.
- A current milestone and observable overall completion criteria exist.
- The stop criteria and the follow-up issue creation scope are clear.

Do not record start permission and do not begin a development cycle while any
one of them is unidentified. Record the start permission and its scope in a
comment on the roadmap management issue once all of them are confirmed.

After the start, the coordinator may create follow-up issues or refine
`Backlog` issues inside the approved roadmap scope. This permission does not
replace repository creation, target repository file changes, per-plan dispatch
approval, scope change approval, pull request merge, or the `Done` gate.

Record the user decision in the roadmap management issue before changing the
goal, the scope, the overall completion criteria, or the follow-up issue
creation scope of the roadmap.

## Development cycle

```text
confirm the current state and the completed results
-> update the product and technical documents and the roadmap
-> select the next goal of the roadmap
-> reuse and refine an existing issue, or create a follow-up issue
-> confirm the ready entry criteria
-> write the execution plan and get user approval
-> execution batch, work item task, dispatch
-> implement, test, review, merge
-> record the completion evidence and the remaining risks
-> confirm the overall completion criteria
-> return to the next development cycle when work remains
```

Approve each development batch as a separate execution plan under the plan and
dispatch workflow. Close each batch independently under the review and close
workflow. Return to the next development cycle when one execution plan ends and
the overall completion criteria of the roadmap management issue still remain.

## Refining the next issue

The coordinator decides the next development item in the following order.

1. Read the current milestone and the completion criteria of the roadmap.
2. Confirm the result just completed, the tests, the user feedback, and the new risks.
3. Search the central project and the target repository for an existing issue with the same goal.
4. Reinforce the scope, the completion criteria, and the verification method of an existing `Backlog` issue rather than creating a duplicate.
5. Register a new issue under [roadmap follow-up issues](../integrations/TRACKER-GITHUB.md#roadmap-follow-up-issues) when no existing issue exists and the work is inside the approved roadmap scope.
6. Move to `Ready` only the issues the next execution batch needs, after confirming the ready entry criteria.
7. Leave the later candidates in `Backlog` or in the roadmap source document, and do not detail them in advance.

A scope or completion criteria change after `Ready` follows the existing state
and re-approval rules. Record an idea outside the roadmap in the roadmap
management issue as a candidate with its rationale only. Do not include it in
the issue creation scope or in an execution plan before a user decision.

A worker performs product and technical document changes in the target
repository, inside an approved issue and pull request. The coordinator does not
modify a target repository file without a separate dispatch. The coordinator
updates only the central project state and the links, decisions, and progress
state in the roadmap management issue.

## Completion scope

When the work creates a new operational service, the overall completion
criteria include the actual deployment, the monitoring, the rollback and
recovery verification, the operations documents, and the responsibility
handover. For features and improvements to an existing service, add to the
completion criteria only the operational items the change affects.

When the completion boundary differs, as for a non-operational prototype or an
investigation, state the expected result and the excluded operational
conditions in the roadmap management issue. The `Done` judgment of an
individual issue follows the merge and verification gate of the review and
close workflow regardless of the completion scope.

### Operational deployment contract

To use the operational completion criteria above, the following contract must
be findable in the target repository or in the project profile.

- The deployment environments and the promotion order
- The permission to run a deployment and the approving party
- The pre-deployment and post-deployment checks and the observed metrics
- The minimum observation window and the success judgment
- The rollback trigger conditions, the execution method, and the responsible party
- The contact and handover path for an incident
- Links to the operations documents, the dashboards, and the runbooks

When the contract cannot be confirmed, implementation and merge can still
complete, but the operational service as a whole is not treated as complete.
Record the contract items that could not be confirmed and the resume conditions
in the roadmap management issue.

For features and improvements to an existing service, verify again only the
parts of the contract the change affects, matching the completion criteria
scope above.

The target repository owns the contract source. Do not create a new contract
field in the project profile. Keep only a link to the source there. Do not copy
the deployment commands, the paths, or the credentials of the target repository
into this repository.

## Waiting, pausing, and resuming

The following situations are not an end to the whole procedure. Each is a wait
that leaves resumable evidence in GitHub.

- Waiting for execution plan approval
- Waiting for an external dependency or a user decision
- Insufficient orchestrator capacity
- A missing required verification environment or credential
- Only work outside the approved scope remains

Continuing does not mean keeping one agent or one execution batch alive.
The active coordinator settles one development cycle, then prepares the next
issue and execution plan. When the session ends, the next coordinator reads the
roadmap management issue, the target issues, the execution plans, and the pull
requests in GitHub and continues from there.

This policy provides no dispatch without approval. Use a scheduled wake only
inside the execution automation scope the user approved. Do not use it when no
approved scope exists. A scheduled wake is a means of waking a coordinator and
is not an execution permission. The conditions it must satisfy follow
[continuous execution triggers](#continuous-execution-triggers).

Record the closing evidence and the last state in the roadmap management issue
and close it when one of the following holds.

- The overall completion criteria are met.
- The user explicitly stopped the work.
- The target repository became inactive or archived.
- The user decided that a blocker which cannot be advanced safely ends the work.

Do not end roadmap loop engineering merely because one execution plan closed or
one worker terminated.

## Continuous execution triggers

This section applies only when `automation.approved` in
[the environment file](../env/ENVIRONMENT.example.md) is `yes`. When
`automation.approved` is `no`, a scheduled wake is not used, and the user starts
each session directly.

This section is the contract that an approved execution automation must satisfy.
It is not an implementation of the execution layer. The exclusion list in
[the operating model scope](../core/OPERATING-MODEL.md#scope) owns the rule that
this repository builds no scheduler, no bot, no webhook, and no GitHub Actions
workflow, and that exclusion stands unchanged. This repository defines only what
a trigger may wake and what it may not.

### Resume-worthy events

The following events are grounds for waking a coordinator again.

- A valid execution plan approval
- A dispatch question, an escalation, or a completion report
- A change in a pull request review, check, or merge state
- A resolved blocker and a met resume condition
- The arrival of a scheduled state reconciliation point

### Trigger limits

- A trigger can only wake a coordinator. It creates no new permission to create an issue, to approve an execution plan, to dispatch, to merge a pull request, or to set `Done`.
- A woken coordinator still passes the existing approval gates unchanged. A trigger firing is not an approval record.
- Do not create a new execution while a valid coordinator and a valid orchestrator state exist for the same execution plan. Duplicate-execution judgment and the handling of unclear ownership follow [coordinator supervision](../integrations/ORCHESTRATOR.md#coordinator-supervision).
- When `orchestrator.name` is `none`, that judgment rests only on the occupancy record in GitHub, as defined in [no-orchestrator mode](../integrations/ORCHESTRATOR.md#no-orchestrator-mode).

### Transport and cadence

- Scheduled execution and a pre-query are used. `automation.mechanism` in [the environment file](../env/ENVIRONMENT.example.md) defines the mechanism that runs them. Do not implement a webhook receiver or a dedicated daemon.
- `automation.interval` in [the environment file](../env/ENVIRONMENT.example.md) defines the cadence of the state reconciliation point. Its baseline value is ten minutes. A cron expression such as `*/10 * * * *` is one example of that baseline, not the definition of the cadence. Treat a cadence change as a user decision inside the execution automation scope.
- The pre-query judges on state. Do not use a time-based condition such as `updated:>=`, because the coordinator's own comment counts as an update and creates a self-triggering loop.
- The judgment condition is whether unhandled work remains in GitHub. The following cases qualify.
  - An open execution plan carries a valid comment whose text is exactly the phrase defined by `github.approval_phrase`, and its target issue is not yet `In Progress`.
  - A target issue is `In Progress` and its linked pull request is merged.
  - The blocking cause of a `Blocked` item is resolved.
- Do not judge from the mere existence of an open execution plan. Confirm the approval and the target issue state together.
- The pre-query command must not exit with code zero when no resume candidate exists. A query command succeeds even on an empty result, so wrap the judgment explicitly.
- When the host slept through a scheduled interval, run that interval once on wake.
- Do not replay more than one missed interval.
- When the next scheduled interval has already arrived on wake, skip the missed interval and run only the next one.
- The automation definition is a local setting on the executing host. Do not commit it to this repository.

### Occupancy check

An occupancy comment is an audit record, not an atomic lock. Two coordinators can
read the same empty state and then both write a comment. Run no more coordinators
than `automation.max_coordinators` in
[the environment file](../env/ENVIRONMENT.example.md) allows, and keep that number
at one while GitHub comments are the only occupancy record.

- A woken coordinator checks the execution plan issue for occupancy by another agent before executing.
- The coordinator records the occupancy in a comment on the execution plan issue when it decides to continue.
- GitHub is the single reference for the occupancy record. Use orchestrator state as cross-check evidence only.
- An agent session started outside the orchestrator does not appear in orchestrator state. Do not treat empty orchestrator state as evidence that no occupancy exists.

## Exit verification

1. The roadmap management issue carries the goal, the scope, the current milestone, the overall completion and stop criteria, and the issue creation permission.
2. Repository creation permission and target repository file change permission were not inferred from the loop start permission.
3. Only the next execution batch was refined, and later candidates were not detailed in advance.
4. The product and technical criteria that affect scope were settled with the user before the first execution plan.
5. A duplicate search ran and the approved scope was confirmed before the roadmap management issue and each follow-up issue were created.
6. Every execution plan was approved separately and passed the existing dispatch gates.
7. The completed results and the remaining risks are reflected in the product and technical documents, the roadmap, and the management issue.
8. No work outside the scope was added to an issue or to a dispatch scope before a user decision.
9. The next development cycle, a wait, or an ending was decided from the overall completion criteria after each execution plan closed.
10. The closing evidence and the last state were recorded and the roadmap management issue was closed at the end.
