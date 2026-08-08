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

**Recommended**

| Dependency | Why |
|---|---|
| [Superpowers](https://github.com/obra/superpowers) | Design, planning, TDD, review, and verification workflows |
| [Ponytail](https://github.com/DietrichGebert/ponytail) | Choosing the smallest implementation that satisfies the requirement |
| [Karpathy Guidelines](https://github.com/multica-ai/andrej-karpathy-skills) | Checking assumptions, surgical changes, and verifiable goals |

The dispatch gate in the orchestrator guide checks that the recommended
baselines are installed and active. Installing them first is strongly
recommended. Without them, the baseline item of that gate does not apply and you
lose the review and verification discipline the workflows assume.

**Optional**

An orchestrator that manages runs, tasks, dispatches, and worker lifecycles.
Without one, a single agent runs everything in dependency order. Set
`orchestrator.name` to `none` and the documents adapt.

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

`docs/env/ENVIRONMENT.md` is a tracked file, so committing it in a public fork
publishes your project URL and your repository scope. Decide for yourself
whether to commit it.

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
