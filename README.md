# Flux Conductor

Read this in [한국어](README.ko.md).

A set of instruction documents for planning and coordinating AI agent work
across several GitHub repositories. GitHub issues and a central GitHub Project
are the permanent source of truth for work state. Everything else adapts to
your environment through one file.

## What this is not

This repository holds documents only. It contains no product code, no bots, no
schedulers, and no GitHub Actions. Nothing here runs on its own.

## Dependencies

Install these before use. The templates assume they are present.

**Required**

| Dependency | Why |
|---|---|
| [git](https://git-scm.com/) | Version control for this repository and your target repositories |
| A GitHub account | Issues and the central project live there |
| [GitHub CLI](https://cli.github.com/) | Reading and updating issues, projects, and pull requests |
| One agent CLI | The agent that reads these instructions and does the work |

The workflows define a minimum
[development work contract](docs/core/OPERATING-MODEL.md#development-work-contract)
for understanding, changing, reviewing, and verifying a target repository.
Each agent can use its own procedure to satisfy that contract, so no particular
skill set is required. The documents require no particular model; worker
selection follows
[worker capability routing](docs/integrations/ORCHESTRATOR.md#worker-capability-routing).

**Optional**

An orchestrator that manages runs, tasks, dispatches, and worker lifecycles.
Without one, a single agent runs everything in dependency order. Set
`orchestrator.name` to `none` and the documents adapt.

Additional skills or skill sets on your agent platform. These documents require
none, and no gate depends on one being installed. Add them when you want them,
and record them in `skills.optional`.

Check each upstream project's current instructions for installation commands.
This README does not repeat them, because they change.

## Getting started

1. Fork this repository.
2. Clone your fork.
3. Install the dependencies above.
4. Start your agent in the clone.
5. Tell it `setup`.

When your agent CLI does not read `AGENTS.md` automatically, point it at that
file or symlink your CLI's own instruction filename to it.

The agent runs the interview in [SETUP.md](SETUP.md) and writes
`docs/env/ENVIRONMENT.md`. Choosing a language other than English also performs
the switch described in the Language section below.

`docs/env/ENVIRONMENT.md` is untracked by default, because committing it in a
public fork would publish your project URL and your repository scope. Setting up
a second machine means running `setup` again there rather than pulling the file.

## Language

The instruction documents are written in English. The setup interview asks
which language you want. Choosing another language copies the English originals
to `archive/en/` and writes translations at the original paths. The English
originals stay as the reference copy.

## Documents

| Document | Role |
|---|---|
| [AGENTS.md](AGENTS.md) | Entry point. Setup check and document routing |
| [SETUP.md](SETUP.md) | Initial setup interview and language switching |
| [docs/core/INVARIANTS.md](docs/core/INVARIANTS.md) | The rules that hold in every mode, on one page |
| [docs/core/OPERATING-MODEL.md](docs/core/OPERATING-MODEL.md) | Operating principles that apply to every environment |
| [docs/integrations/TRACKER-GITHUB.md](docs/integrations/TRACKER-GITHUB.md) | GitHub issue and project rules |
| [docs/integrations/ORCHESTRATOR.md](docs/integrations/ORCHESTRATOR.md) | Execution lifecycle responsibilities and no-orchestrator mode |
| [docs/workflows/PLAN-AND-DISPATCH.md](docs/workflows/PLAN-AND-DISPATCH.md) | From execution plan to dispatch |
| [docs/workflows/REVIEW-AND-CLOSE.md](docs/workflows/REVIEW-AND-CLOSE.md) | From review to completion |
| [docs/workflows/LOOP-ENGINEERING.md](docs/workflows/LOOP-ENGINEERING.md) | Roadmap cycles and the continuous execution trigger contract |
| [docs/env/ENVIRONMENT.example.md](docs/env/ENVIRONMENT.example.md) | The environment variable contract |

`docs/projects/<owner>--<repo>.md` records what differs for one onboarded
repository. Create it when you onboard, not before.

## License

MIT. See [LICENSE](LICENSE).
