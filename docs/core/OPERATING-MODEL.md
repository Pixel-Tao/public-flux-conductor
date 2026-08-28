# Operating Model

## Purpose

This repository is a central instruction repository that plans and coordinates
agent work across several GitHub repositories in one consistent way. It stores
policy, procedure, and per-project exception documents only. It stores no
executable product code.

## Scope

- Work management through GitHub issues and the central project defined by `github.project_url` in [the environment file](../env/ENVIRONMENT.example.md)
- The procedure that runs approved work as execution batches, work item tasks, and dispatches
- Application of the design, planning, implementation, review, and verification procedures each agent harness provides, within the [worker capability contract](../integrations/ORCHESTRATOR.md#worker-capability-contract)
- Records of the extra constraints and operational exceptions of each target repository

The following items are out of scope for this repository.

- Product code and deployment code
- Implementations of a scheduler, a bot, a webhook, or a GitHub Actions workflow
- Copies of a target repository's own instructions
- Automatic dispatch that the user has not approved
- Real-time incident response and runtime-only state changes that leave no Git artifact

The central project tracks planned development changes. A change that produces no
commit and no pull request, such as a service restart, a rollback of a running
deployment, or an operational flag flip, is not a managed item. Record in an issue
the decision that such an action produced when it changes scope, priority, or
completion criteria, and run the action itself outside this workflow.

## Source of truth and responsibility

| Layer | Responsibility | Persistence |
|---|---|---|
| GitHub | Issues, priority, approval records, pull requests, final work item state | Permanent |
| Coordination repository documents | Shared policy, execution procedure, per-project exceptions | Permanent |
| Orchestrator | Execution batches, work item tasks, dispatches, messages, agent lifecycle | While running |
| Agent harness | Design, planning, test-driven development, review, and verification procedures | Procedural, per platform |
| Target repository | Implementation rules and that repository's `AGENTS.md` | Permanent |

GitHub is the single source of truth for the state of every work item the
central project manages. Do not close a GitHub work item on orchestrator state
alone. When orchestrator state is lost, recover execution from the GitHub
record. The [fast path](#fast-path) is not managed in the central project. When
the fast path produces a commit or a pull request, that Git record is the
permanent evidence.

The current operating model assumes a single operator. The logins listed in
`github.approvers` in [the environment file](../env/ENVIRONMENT.example.md) are
the only approvers, and a comment by any other login is not an approval record.
Add role-based permissions as a separate policy when real collaborators appear.

The approval gate is an audit record rather than a security boundary. The agent
itself writes the approval comment on the `approve execution plan #<number>`
shortcut, so the gate holds only as far as the agent follows it. Read the record
as evidence of intent, not as proof that a separate party approved.

## Instruction boundaries

- Apply the shared policy of this repository to coordination and to the execution lifecycle.
- Apply the target repository's `AGENTS.md` and its existing development rules to actual code changes.
- Record only differences in a per-project document. Do not copy a shared rule into it. Do not override a target repository rule from it.
- Stop execution and ask the user to decide when the two scopes conflict or when the applicable scope is unclear.
- Record in a GitHub issue every decision that changes the scope, the completion criteria, or the priority of a work item the central project manages.

## Answer format

This section applies to conversational answers an agent sends to the user.
Issue bodies, pull request descriptions, and comment formats follow
[the tracker guide](../integrations/TRACKER-GITHUB.md).

### Common style

- Write answers in the language named by `language` in [the environment file](../env/ENVIRONMENT.example.md).
- Use the polite form when that language distinguishes politeness levels. The instruction documents in this repository stay in the plain declarative form.
- Open the answer with the result, the conclusion, and the next actions. Put the process, the evidence, and the detail below them.
- Keep one sentence within two lines. Put one thing in one sentence.
- Write three or more listed items as a list. Write a comparison of several items on the same attribute as a table.
- Expand each term and abbreviation once, the first time it appears in an answer. Example: `dispatch` (handing one execution to a worker).
- Do not force a fixed title or a fixed section layout. Choose the shape that fits the situation once the answer carries the needed content.

### Answers that report performed work

An answer that actually performed something carries every item below. Changing
a file, running a command, changing an issue or a pull request, and dispatching
all count as performing something. Write `none` for an item that does not
apply.

Write the following at the top of the answer.

- The result. State it as one of `complete`, `partially complete`, or `stopped`.
- The next action, or the decision needed from the user

Write the following in the body below them.

- What was done and what changed. Include file paths, issue numbers, and pull request numbers.
- Problems, constraints, and assumptions made
- Verification performed and its result. For verification that could not run, write the reason and the impact.

## Fast path

The standard workflow of issue, execution plan approval, dispatch, and pull
request is the default route for work that needs management. A clear, low-risk,
single piece of work that the central project does not track may instead run on
the fast path.

The agent confirms all of the following conditions.

- One agent can produce one clear outcome in one repository.
- Scope, completion criteria, and verification method carry no meaningful ambiguity.
- The reason for the change is evident from the diff and the existing documents.
- The central project does not already track an issue for this work.
- The change is small and easy to revert.
- The change affects none of security, permissions, secrets, personal data, data loss, schema, migration, deployment, infrastructure, dependencies, or public API compatibility.
- The work needs no parallel execution, no external coordination, no separate approval, and no multi-step sequential execution.
- An existing command in the target repository or a direct check verifies the work immediately.

When the user writes `standard workflow:`, use the standard workflow even for
simple work. When the user writes `fast path:`, or when the user specifies
nothing and every condition above holds, use the fast path. Writing
`fast path:` does not bypass the conditions above.

The fast path creates no issue, no execution plan, no project state change, no
execution batch, no work item task, and no dispatch. It also creates no
separate brainstorming, design approval, or implementation plan artifact. The
agent reads the applicable repository instructions and the directly related
files, states the scope in one sentence, then makes the minimum change and runs
the smallest relevant verification.

A fast path instruction approves the requested file changes only. Perform a
commit, a push, a pull request, a deployment, or any other external state
change only on a user request or under an explicit rule of the target
repository.

When investigation or execution reveals scope, risk, a dependency, or ambiguity
outside those conditions, stop making further changes and report the reason for
switching to the standard workflow. Do not discard changes already made on your
own initiative. Get a user decision before a commit or a push. Do not skip a
failed verification. Do not treat a failed verification as a success.

## Research and advisory answers

An opinion request, a comparison, a technical investigation, and a fact check
are read-only by default. Read-only means the work changes no tracked file and
no work item state in an external service. It does not cover non-persistent
state a platform manages on its own, such as a search tool's temporary cache.

A research request is a separate route from the [fast path](#fast-path). The
fast path is defined around a file change, and research changes neither tracked
files nor external state, so a research request is not a fast path candidate at
all. Apply the rules of this section to a research request without testing the
fast path conditions.

The following scope is allowed without separate approval.

- Reading the repository and linked documents
- Public web search and page reading
- Page reading through an existing login session the user already has access to
- Read-only lookups and comparisons made to produce the result

An opinion or information request alone does not allow the following actions.

- Logging in, creating an account, entering a credential, or changing an authentication method
- Posting, commenting, reacting, sending, uploading, purchasing, or changing a setting
- Changing an issue, a pull request, a project, a calendar, or any other external state
- Bypassing an access restriction, or reading information the user has no permission to see
- Saving research material as a tracked file in the repository

Use a source that requires a login only when an existing authenticated session
and an approved read-only tool are both available. When they are not available,
do not work around the restriction. Report the missing source and its impact on
the answer instead. Use private information only within the scope the request
needs.

Check a current source for information that changes over time or where accuracy
matters. Prefer official documentation, the original text, the repository, and
the actual state. Do not conclude from a search result title alone.

Present the answer in the following order.

1. The conclusion and the summary for the question
2. The reasoning and the detail the answer needs
3. Source links and the time each was checked
4. Facts, estimates, conflicting evidence, and parts left unconfirmed

This order is not forced on a simple single fact. Always open with the conclusion
in an answer that carries a detailed comparison, decision support, multi-source
research, or authenticated information. Repeat a short summary at the end only
when the answer runs long enough that the opening has scrolled away. Sentence
length, list and table use, and term expansion follow
[answer format](#answer-format).

When research finds that a file change or an external state change is needed,
do not perform it before a separate request. Only after a separate request does
that change become a fast path or standard workflow candidate.

When research runs as a lightweight read-only worker, additionally apply the
permission enforcement, the source citation, and the default worker
verification conditions in
[worker model routing](../integrations/ORCHESTRATOR.md#worker-model-routing).

## Natural language requests and issue registration

When the user states issue registration in natural language, the agent confirms
the target repository, then creates an issue or reuses a duplicate issue as
described in
[natural language issue registration](../integrations/TRACKER-GITHUB.md#natural-language-issue-registration).
Do not infer permission to create an issue from a consultation request or an
implementation request alone.

Add a newly created issue, and any issue the central project does not yet
track, to the central project with `Status=Backlog` and `Priority=P2`. Keep the
current state and priority of a duplicate issue that is already tracked. When
the user asks for registration only, do not start implementation, do not write
an execution plan, and do not dispatch. When the user asks for registration and
execution together, follow the existing ready entry criteria and the execution
plan approval workflow rather than the fast path.

## Roadmap loop engineering

The [loop engineering workflow](../workflows/LOOP-ENGINEERING.md) repeats
product and technical design, refinement of the next issue, and reflection of
execution and verification results in every development cycle. When the user
states the start, the agent gains permission to create and refine follow-up
issues within the recorded roadmap scope. That permission does not extend to
repository creation, to target repository file changes, or to dispatch.

Each execution plan is approved separately in the existing way. The roadmap
management issue, the target issues, the execution plans, and the pull requests
in GitHub are the permanent evidence, so the next coordinator continues the
development cycle after an execution batch or an agent session ends.

## Document structure

```text
.gitignore
LICENSE
AGENTS.md
CLAUDE.md
SETUP.md
README.md
README.ko.md
docs/
├─ core/
│  ├─ INVARIANTS.md
│  └─ OPERATING-MODEL.md
├─ env/
│  ├─ ENVIRONMENT.example.md
│  └─ ENVIRONMENT.md      # untracked
├─ integrations/
│  ├─ ORCHESTRATOR.md
│  └─ TRACKER-GITHUB.md
├─ workflows/
│  ├─ LOOP-ENGINEERING.md
│  ├─ PLAN-AND-DISPATCH.md
│  └─ REVIEW-AND-CLOSE.md
└─ projects/
   └─ <owner>--<repo>.md
archive/
└─ en/
```

- `.gitignore` excludes the local artifact paths a harness or an optional skill writes, and `docs/env/ENVIRONMENT.md`, which records one host's values.
- `LICENSE` carries the license text of this repository.
- `AGENTS.md` is the entry point. It carries the repository purpose, the prohibitions, and the document navigation paths only.
- `CLAUDE.md` imports `AGENTS.md`, so a CLI that reads only `CLAUDE.md` gets the same entry point.
- `SETUP.md` carries the initial setup interview that produces `docs/env/ENVIRONMENT.md`.
- `README.md` and `README.ko.md` carry the introduction and the getting started steps.
- `core` carries the operating principles that apply unchanged to every project. `INVARIANTS.md` is the one-page card of the rules that hold in every mode.
- `env` carries the environment values of one installation. `ENVIRONMENT.example.md` is the contract, and `ENVIRONMENT.md` is the filled copy that Git does not track.
- `integrations` carries the usage rules and the state mapping for each external tool.
- `workflows` carries the execution procedure of each work stage.
- `projects` carries only the differences of a project actually onboarded.
- `archive/en/` carries the English reference copy, and it exists only after a language switch.
- Add `templates` only when the same form repeats.

Do not create a document or a directory before it is needed.

## Work item states

The central project uses the following state flow.

```text
Backlog -> Ready -> Planned -> In Progress -> Review -> Done
Planned -> Ready (execution plan rejected, changed, or cancelled)
In Progress -> Ready (approval voided by a scope or completion criteria change)
Review -> In Progress (an implementation fix inside the approved scope is needed)
Any non-Done state -> Blocked -> previous valid state
Blocked -> Ready (approved scope or completion criteria changed)
Done -> Ready (issue reopened)
```

- `Backlog`: work that is not organized or not prioritized yet
- `Ready`: work whose goal and completion criteria are clear enough to enter an execution plan
- `Planned`: work included in an execution plan but not dispatched yet
- `In Progress`: work dispatched under an approved execution plan
- `Review`: work whose implementation result and pull request are under review and verification
- `Done`: work whose pull request is merged and whose required verification is complete
- `Blocked`: work that cannot proceed because of an external dependency, repeated failure, or a pending user decision

Return an item of a rejected execution plan, or of one that needs changes, to
`Ready`. Return an item whose `Blocked` cause is resolved to the last valid
state before it stopped. Return an item that became `Blocked` because the
approved scope or the completion criteria changed to `Ready` instead, because
the existing approval is void. Return a `Done` item to `Ready` only after the
issue is reopened and the completion criteria are reviewed again.

## Execution plans and approval

The agent analyzes the `Ready` items of the central project and proposes
execution batches. The agent creates one execution plan issue in the
coordination repository defined by `github.coordination_repo` in
[the environment file](../env/ENVIRONMENT.example.md) for each execution batch.
The `at a glance` format and the detail items of an execution plan body are
defined in
[execution plans and approval](../integrations/TRACKER-GITHUB.md#execution-plans-and-approval).
The full procedure from writing a plan to dispatching it is
[plan and dispatch](../workflows/PLAN-AND-DISPATCH.md).

The user approves in one of two ways. An approver leaves a comment on the
execution plan issue whose text is exactly the phrase defined by
`github.approval_phrase` in
[the environment file](../env/ENVIRONMENT.example.md). Alternatively, the user
instructs the agent in conversation with exactly
`approve execution plan #<number>`. Any other wording is not an approval
instruction. An agent that receives the conversational instruction confirms
that the number is an execution plan issue in the coordination repository and
that the plan is currently executable. Only when both checks succeed does the
agent leave a comment with exactly that phrase and then execute. When a check
fails, the agent performs neither the comment nor the dispatch. When the scope
changes before approval, update the execution plan and get approval again. When
the scope changes after approval, void the existing approval, record the change
in GitHub, and get approval again.

## Tracker and orchestrator mapping

| GitHub | Orchestrator | Rule |
|---|---|---|
| Approved execution plan issue | Execution batch | One execution batch per execution plan |
| Target repository issue | Work item task | One work item task per issue that can be completed independently |
| One agent's attempt at the work | Dispatch | A retry creates a new dispatch |
| Issue dependency | Work item task dependency | Reflects the approved execution order exactly |
| Issue or pull request result | Completion report | Includes links and verification evidence |

When an issue is too large to finish as one work item task, split it into child
issues in the target repository before approval. Do not bypass the GitHub work
scope by adding work item tasks inside the orchestrator only.

## Separation of concerns

- The orchestrator owns execution batches, work item tasks, dispatches, worker creation, messages, waiting, retries, and termination.
- The worker's harness owns brainstorming, plan writing, test-driven development, code review, and verification before completion, within the required verification and the target repository instructions.
- When a harness procedure calls for a parallel agent or a sub-agent, perform the actual worker creation and dispatch through the orchestrator.
- When work needs isolation, judge the need for isolation from the nature of the work, and let the orchestrator manage the workspace lifecycle.
- A worker reads the target repository instructions first, then applies its harness procedures.
- A worker sends the completion report only after finishing the required verification.
- A completion report closes a dispatch. It is not an approval to set the GitHub state to `Done`.

When `orchestrator.name` in
[the environment file](../env/ENVIRONMENT.example.md) is `none`, a single agent
performs the execution lifecycle directly, as defined in
[no-orchestrator mode](../integrations/ORCHESTRATOR.md#no-orchestrator-mode).

## Execution flow

1. The agent reviews the `Ready` items of the central project.
2. The agent writes an execution plan based on dependencies and possible conflicts, then moves the items to `Planned`.
3. The user leaves the approval phrase as a comment on the execution plan issue, or instructs the agent to do so, which produces a verified approval comment.
4. The coordinator creates an execution batch and a work item task graph from the approved execution plan.
5. The coordinator dispatches the independent work item tasks first and manages questions and progress through orchestrator messages.
6. The worker implements, verifies, and opens a pull request under the target repository rules and its harness procedures.
7. The coordinator confirms the completion report, the pull request, and the check results, then moves the item to `Review`.
8. When the pull request is merged and the completion criteria are met, the coordinator moves the item to `Done` and summarizes the result on the execution plan issue.

Steps 1 to 5 are detailed in
[plan and dispatch](../workflows/PLAN-AND-DISPATCH.md). Steps 7 and 8 are
detailed in [review and close](../workflows/REVIEW-AND-CLOSE.md).

## Recoverable understanding

A later coordinator must be able to reconstruct a change from the permanent
record alone: what changed, why it was done this way, which alternative was
rejected when one was, and what must stay true afterwards. A conversation, an
orchestrator message, and an untracked note are not part of the permanent
record.

- Record a design decision settled with the user before dispatch on the target issue, with its reason.
- Record a non-obvious decision made during implementation in the pull request.
- Record long-lived architecture and domain knowledge in the target repository documents.
- Write no separate record for a change whose reason the diff and the existing documents already show.
- Treat a change whose obviousness could be judged either way as non-obvious.
- Leave understanding that cannot be repaid inside the approved scope as a follow-up issue candidate. Do not expand the approved scope to repay it.

### Understanding support

On a user request, or before the separate user confirmation that a high-risk
merge requires, the coordinator may present the change as an understanding
briefing: the content of the permanent record, restated in plain language with
visual aids such as diagrams and flowcharts. Build the briefing from the
permanent record only. When the briefing cannot be built from that record, the
record is insufficient, so repair the record first.

The support takes one of two forms, and the user chooses the form.

- `briefing`: the explanation and the visuals alone. The user reads them and then confirms through the existing gates.
- `check`: the explanation followed by one question per step. The completion screen reveals a confirmation phrase, and the user returns that phrase to the coordinator. The coordinator records the passed check on the execution plan issue for a pre-approval check, or on the target issue or the pull request otherwise, then proceeds through the existing gates.

The following rules apply to both forms.

- Present the explanation before any question. Do not present questions alone.
- The user may switch a `check` to a `briefing` at any time.
- The confirmation phrase must differ from `github.approval_phrase`, and the user returns it in conversation only, never as a GitHub comment.
- Recording a check result is a comment, not a scope or completion criteria change. It does not void an approval.
- Neither form replaces the approval record, the merge confirmation record, or any other existing gate.
- The generated page or file follows [temporary artifacts](../integrations/ORCHESTRATOR.md#temporary-artifacts) and is not committed.

## Decision and message records

- Record transient progress, heartbeats, and worker questions in the orchestrator.
- Record every decision that changes scope, priority, completion criteria, or retry strategy in GitHub as well.
- Record a design decision settled with the user before dispatch, as defined in [recoverable understanding](#recoverable-understanding).
- When a worker question requires a scope change, the coordinator gets a user decision and updates GitHub first.
- When work outside the execution plan is discovered, do not expand the plan on your own initiative. Leave it as a new issue or a candidate for the next execution plan.

## Failure and recovery

- A wait timeout in the orchestrator is not a work failure.
- Retry a failed dispatch only after recording the cause and the new strategy in the GitHub issue.
- Do not repeat an identical retry whose cause and strategy are unchanged.
- Move the item to `Blocked` when the orchestrator's circuit breaker threshold is reached, or when the work cannot proceed without a user decision.
- Do not move an item to `Done` while a pull request check fails, while the pull request is unmerged, or while verification evidence is missing.
- When recovering an execution batch, reread the approved execution plan and the current state of the target issues, then first check whether the existing execution state is recoverable.
- Build a replacement execution state only after confirming that the previous state is terminated or unrecoverable, and record the reason for the replacement in GitHub.
- Do not assume a past dispatch completed during recovery.

## Completion and verification

Before reporting completion, a worker runs the smallest relevant test, lint, or
build the target repository requires. Record verification that could not run,
with its reason and impact, in the pull request and in the completion report.

The coordinator confirms all of the following conditions before moving the
GitHub item to `Done`.

- The completion criteria of the target issue are met.
- The related pull request is linked.
- The required checks pass, or an explicitly approved exception covers them.
- The pull request is merged.
- Remaining risks and follow-up work are recorded.
- The reason for a non-obvious change is recoverable from the pull request, the target issue, or the target repository documents, as defined in [recoverable understanding](#recoverable-understanding).

The completion summary includes the execution plan issue, the target issue, the
pull request, the verification results, and the remaining risks.

## Document quality standards

- Each document states its scope, its inputs, its procedure, and its exit conditions.
- Define a shared rule in one document only, and link to it from the others.
- Leave no placeholder, no undecided decision, and no conflicting rules.
- Every relative link points at a real document inside the repository.
- Add a new document only when a real repetition or a project onboarding need appears.
