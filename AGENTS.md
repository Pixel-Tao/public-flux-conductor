# Agent Instructions

## Role

This repository is the coordination repository that plans and coordinates agent
work across several GitHub repositories. It implements no product code and no
execution automation.

## Start sequence

1. Check whether `docs/env/ENVIRONMENT.md` exists. When it is absent, read [SETUP.md](SETUP.md#when-to-run) and propose the initial setup before handling any other request.
2. Read [the operating model](docs/core/OPERATING-MODEL.md) before any other work.
3. Read only the existing `docs/integrations/`, `docs/workflows/`, and `docs/projects/` documents that relate to the request.
4. Read a target repository's `AGENTS.md` and its existing development rules before changing that repository.

## New session notice and routing

Include the following notice once in the first answer of a new session. Do not
repeat it when the first request already states `fast path:`,
`standard workflow:`, or issue registration.

- A clear, low-risk, single piece of work runs on the fast path automatically.
- Tracked, high-risk, or composite work follows the standard execution plan workflow.
- State your intent with `fast path:`, `standard workflow:`, or a request to register the work as an issue.

The notice above is written in English here. Deliver the actual notice in the
language named by `language` in
[the environment file](docs/env/ENVIRONMENT.example.md).

- Decide the route by [the fast path conditions of the operating model](docs/core/OPERATING-MODEL.md#fast-path) unless the user specifies otherwise.
- A research request is not subject to the fast path decision. It follows [research and advisory answers](docs/core/OPERATING-MODEL.md#research-and-advisory-answers) instead.
- When the route is the fast path, state the scope in one sentence and proceed without a separate approval question.

## Issue registration

- When the user states issue registration in natural language, create the issue in the target repository as described in [natural language issue registration](docs/integrations/TRACKER-GITHUB.md#natural-language-issue-registration).
- Do not infer permission to create an issue from a consultation request or an implementation request alone.
- When the user asks for registration only, do not start implementation, do not write an execution plan, and do not dispatch.

## Repository boundaries

- Git tracks only `.gitignore`, `LICENSE`, `AGENTS.md`, `CLAUDE.md`, `SETUP.md`, `README.md`, `README.ko.md`, `docs/**/*.md`, and `archive/**`.
- `docs/env/ENVIRONMENT.md` is excluded from tracking. It records one host's values and belongs on that host. Commit it only on an explicit operator decision, and never in a public fork.
- The skill baselines may keep local plans and work records under the path `.gitignore` excludes. Do not stage them and do not commit them.
- `archive/en/` holds the English reference copy. Only the setup procedure creates or removes it. Do not modify its contents, and treat it as authoritative when a translation and its original conflict.
- Do not commit product code, execution scripts, configuration, GitHub Actions workflows, generated files, or runtime state.
- Do not create a document, a directory, or a template before the need is confirmed.
- Do not copy a target repository's instructions. Record only the per-project differences.

## Permissions and tools

- GitHub is the single source of truth for issues, priority, approval, pull requests, and final state.
- The orchestrator owns execution batches, work item tasks, dispatches, and the agent lifecycle.
- The skill baselines own the design, planning, implementation, review, and verification procedures.
- When `orchestrator.name` is `none`, the agent manages the execution lifecycle directly, as defined in [no-orchestrator mode](docs/integrations/ORCHESTRATOR.md#no-orchestrator-mode).
- Apply the target repository's instructions to actual code changes.
- Perform worker creation and dispatch through the orchestrator even when a skill baseline procedure calls for agent delegation.

## Execution gate

- Do not dispatch before a valid approval. The approval methods and the verification criteria follow [execution plans and approval](docs/integrations/TRACKER-GITHUB.md#execution-plans-and-approval).
- The user may instruct the agent with exactly `approve execution plan #<number>` to request the approval comment record and the dispatch.
- When the scope changes after approval, void the existing approval, record the change in GitHub, and get approval again.
- Record a decision that changes scope, priority, or completion criteria in GitHub as well, not in the orchestrator alone.
- Do not move a GitHub work item to `Done` on a completion report alone.

## Document change principles

- Choose the smallest document change that satisfies the request.
- Define a shared rule in one document only, and link to it from the others.
- Do not repeat a shared rule in a project document. Write only the exceptions and the additional constraints.
- Leave no broken relative link, no unfinished decision, and no conflicting rules.
- Run the smallest relevant verification after a change, and report any verification that could not run.
