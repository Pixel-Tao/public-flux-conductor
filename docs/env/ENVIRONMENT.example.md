# Environment

This file records the values that adapt the instruction documents to one
environment. Copy it to `ENVIRONMENT.md` in the same directory and fill it in.
The setup interview in [SETUP.md](../../SETUP.md) creates that file for you.

This file records values only. It does not define policy. When a value here
conflicts with a rule in the instruction documents, the instruction documents
win.

An agent never guesses a required value. If a required value is missing, the
agent stops and returns to the matching interview step.

The instruction documents link to this page because it is the tracked contract
for these keys. The agent reads the actual values from `ENVIRONMENT.md`, and
every value shown on this page is an example rather than a real one.

## Language

| Key | Value | Notes |
|---|---|---|
| `language` | `en` | Language for agent answers, issue bodies, and pull request descriptions. Defaults to `en`. |

## GitHub

| Key | Value | Required |
|---|---|---|
| `github.owner` | `your-account` | Yes |
| `github.project_url` | `https://github.com/users/your-account/projects/1` | Yes. Without it, do not dispatch. |
| `github.coordination_repo` | `your-account/your-coordination-repo` | Yes. Execution plan issues live here. |
| `github.managed_scope` | Repositories owned by `your-account` | Yes |
| `github.approvers` | `your-account` | Yes. Only a comment by one of these logins is a valid approval. |

Default `github.coordination_repo` to the fork itself, which is
[the coordination repository](../../AGENTS.md#role). Record a different
repository only when the execution plan issues live outside the fork.

The [tracker guide](../integrations/TRACKER-GITHUB.md#execution-plans-and-approval)
defines an approval text for each execution plan. Do not add a local phrase
override.

An approval comment by a login outside `github.approvers` is not an approval
record, whatever its text. A public fork lets anyone comment on an execution
plan issue, so this list remains an approval boundary.

## Orchestrator

| Key | Value | Required |
|---|---|---|
| `orchestrator.name` | `none` | Yes. Use `none` when a single agent runs everything. |
| `orchestrator.guide_source` | Not applicable when `orchestrator.name` is `none` | Yes when an orchestrator is used |
| `orchestrator.terms` | See the mapping table below | Yes when an orchestrator is used |

When `orchestrator.name` is `none`, follow
[no-orchestrator mode](../integrations/ORCHESTRATOR.md#no-orchestrator-mode).

### Term mapping

Map the role vocabulary in
[the orchestrator guide](../integrations/ORCHESTRATOR.md#role-vocabulary) to the
names your tool actually uses. The example below shows one possible tool.

| Role term | Tool term |
|---|---|
| execution batch | Run |
| work item task | Task |
| dispatch | Dispatch |
| worker | worker agent |
| completion report | `worker_done` |
| workspace | folder context and terminal |

## Agent platforms

| Key | Value | Required |
|---|---|---|
| `agent.platforms` | `your-agent-platform` | Yes |

Record only the platforms available to the orchestrator. Do not record a model,
reasoning setting, tier, alias, or fallback order. Any model may be used when
the resulting worker satisfies
[worker capability routing](../integrations/ORCHESTRATOR.md#worker-capability-routing)
and the [worker capability contract](../integrations/ORCHESTRATOR.md#worker-capability-contract).

## Optional skills

| Key | Value | Required |
|---|---|---|
| `skills.optional` | A list of additional skills installed on the platforms in use, or empty | No. Empty is the default, and an entry never gates a dispatch. |

| Skill | Platform | Installed |
|---|---|---|
| | | |

This table is a convenience record only. A missing or unverified entry does not
block a dispatch and is not a failed gate. Do not add a rule that depends on an
entry being present.

## Scheduled wake

| Key | Value | Required |
|---|---|---|
| `automation.approved` | `no` | Yes |
| `automation.mechanism` | Not applicable while `automation.approved` is `no` | Yes when approved |
| `automation.interval` | `10 minutes` | No. Defaults to 10 minutes. |
| `automation.max_coordinators` | `1` | No. Defaults to 1. Keep it at 1 while the occupancy record is a GitHub comment. |

Scheduled wake only wakes a coordinator. It grants no execution authority. See
[continuous execution triggers](../workflows/LOOP-ENGINEERING.md#continuous-execution-triggers).

The automation definition lives on the executing host. Do not commit it to this
repository.

## Verified on

Record the date this file was last confirmed against the real environment.

| Field | Value |
|---|---|
| Last verified | not yet verified |
