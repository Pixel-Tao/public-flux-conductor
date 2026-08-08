# Setup

This document defines the initial setup interview. Following it produces
`docs/env/ENVIRONMENT.md`, the one file that adapts every instruction document
to this environment.

## When to run

Run the interview when either condition holds.

- `docs/env/ENVIRONMENT.md` does not exist. Propose the interview before
  handling any other request.
- The user asks to run setup. Then run it regardless of whether the file
  exists and update the existing values.

The interview is read-only except for the files it writes. Do not install
anything, do not change permissions, and do not create a GitHub project unless
the user explicitly asks for it.

## Interview

Ask one question at a time. Wait for the answer before asking the next one.
When the user does not know a value, skip it and record it as unresolved.

1. **Language.** Which language should answers, issue bodies, and pull request
   descriptions use? Default is `en`.
2. **GitHub account.** Which account or organization owns the work? Which
   repository holds execution plan issues? Which repositories are in scope?
3. **Central project.** Does a central GitHub Project already exist? If yes,
   ask for its URL. If no, walk through
   [creating the central project](docs/integrations/TRACKER-GITHUB.md#creating-the-central-project)
   and then ask for the URL.
4. **Approval phrase.** Which exact comment text records approval? It must be
   one fixed phrase.
5. **Agent platforms.** Which agent platforms are in use? For each, which model
   and effort serve as the default tier, the lightweight read-only tier, and
   the escalation order?
6. **Skill baselines.** Which shared skill baselines are installed and active
   in the home the workers actually use? Check read-only. Record what could not
   be verified.
7. **Orchestrator.** Is an orchestrator in use? If yes, ask for its name, where
   its current command guide lives, and the term mapping described in
   [role vocabulary](docs/integrations/ORCHESTRATOR.md#role-vocabulary). If no,
   record `none`.
8. **Scheduled wake.** Has the user approved scheduled wake? If yes, ask for
   the mechanism and the interval. If no, record `automation.approved` as `no`.

## Output

1. Copy `docs/env/ENVIRONMENT.example.md` to `docs/env/ENVIRONMENT.md` and fill
   in the answers. Record the date each value was verified.
2. When `language` is not `en`, perform [switching language](#switching-language).
3. Report the resulting configuration, the unresolved items, and what the user
   needs to decide next.

Do not start implementation, write an execution plan, or dispatch as part of
setup.

## Switching language

Run these steps only when `language` is not `en`.

1. Move `AGENTS.md`, `SETUP.md`, `README.md`, and `docs/**` into `archive/en/`,
   preserving the same relative paths.
2. Write translations of those documents back at their original paths.
3. Fix every relative link path and anchor so it matches the translated
   headings.
4. Leave `docs/env/ENVIRONMENT.md` at its original path. It holds values, not
   prose, so it is not translated and not archived.
5. Do not modify anything under `archive/en/`. It is the reference copy.
6. Report any document that could not be translated and why.

When `language` is `ko`, handle the READMEs differently. Move `README.md` to
`archive/en/README.md`, then move `README.ko.md` to `README.md`. Do not
translate it, because a Korean README already exists. For any other language,
translate `README.md` and leave `README.ko.md` where it is.

## Reconfiguring

Running setup again updates `docs/env/ENVIRONMENT.md` in place.

Changing `language` after a previous switch requires restoring the English
originals from `archive/en/` first, then running
[switching language](#switching-language) again for the new language. Do not
translate an existing translation.
