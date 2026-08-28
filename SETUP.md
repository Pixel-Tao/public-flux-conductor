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

The interview changes nothing outside the files this document names. Do not
install anything, do not change permissions, and do not create a GitHub project
unless the user explicitly asks for it.

## Interview

Ask one question at a time. Wait for the answer before asking the next one.
When the user does not know a value, skip it and record it as unresolved.

1. **Language.** Which language should answers, issue bodies, and pull request
   descriptions use? Default is `en`.
2. **GitHub account.** Which account or organization owns the work? Which
   repository holds execution plan issues? Which repositories are in scope?
3. **Central project.** Does a central GitHub Project already exist? If yes,
   ask for its URL. If no, guide the user through
   [creating the central project](docs/integrations/TRACKER-GITHUB.md#creating-the-central-project)
   and let the user perform the creation, then ask for the URL.
4. **Approval phrase and approvers.** Which exact comment text records approval?
   It must be one fixed phrase. Which GitHub logins may leave it? Only a comment
   by one of those logins is a valid approval record.
5. **Agent platforms.** Which agent platforms are in use? For each, which model
   and effort serve as the default tier, the lightweight read-only tier, and
   the escalation order?
6. **Optional skills.** Are any additional skills installed on the platforms in
   use? Check read-only. An empty answer is valid and is the default. Do not
   install anything during setup.
7. **Orchestrator.** Is an orchestrator in use? If yes, ask for its name, where
   its current command guide lives, and the term mapping described in
   [role vocabulary](docs/integrations/ORCHESTRATOR.md#role-vocabulary). If no,
   record `none`.
8. **Scheduled wake.** Has the user approved scheduled wake? If yes, ask for
   the mechanism, the interval, and how many coordinators may run at once. If no,
   record `automation.approved` as `no`.

## Output

1. Copy `docs/env/ENVIRONMENT.example.md` to `docs/env/ENVIRONMENT.md` and fill
   in the answers. Record the date each value was verified. The file is
   untracked by default, so it stays on this host.
2. When `language` is not `en`, perform [switching language](#switching-language).
   When `language` is `en` and `archive/en/` exists, restore the English
   documents as [reconfiguring](#reconfiguring) describes.
3. Report the resulting configuration, the unresolved items, and what the user
   needs to decide next.

Do not start implementation, write an execution plan, or dispatch as part of
setup.

## Switching language

The English documents are the reference copy. Every translation is produced
from them, never from another translation.

`docs/env/` is never translated. It holds variable names and recorded values,
and a translated key breaks setup.

Run these steps when `language` is not `en`.

1. When `archive/en/` does not exist, copy `AGENTS.md`, `SETUP.md`, `README.md`,
   `README.ko.md`, `LICENSE`, and everything under `docs/` except
   `docs/env/ENVIRONMENT.md` into it, preserving relative paths. The copy
   includes `docs/env/ENVIRONMENT.example.md`, `README.ko.md`, and `LICENSE`, so
   every link inside `archive/en/` resolves inside `archive/en/`.
   `docs/env/ENVIRONMENT.md` records one environment's values and does not
   belong in a reference copy. When `archive/en/` already exists, it is already
   the reference copy. Leave it untouched.
2. Translate each document in `archive/en/` into the language set by `language`
   and write the result at its original path. Three files are never translated.
   `docs/env/ENVIRONMENT.example.md` is a variable contract, and a translated
   key breaks setup. `README.ko.md` already ships in Korean. `LICENSE` is a
   legal text whose wording is fixed. For `README.md`, when `language` is `ko`,
   copy `README.ko.md` over `README.md` instead of translating. Leave
   `README.ko.md` in place either way.

   Separately from that file list, inside a document that is translated, never
   translate a literal command token, a variable key, or a state value. The
   tokens `fast path:`, `standard workflow:`, and
   `approve execution plan #<number>`, and the `Status` and `Priority` field
   names and their values, stay in English.
3. Fix every relative link path and anchor in the translated documents so each
   one matches the translated headings.
4. Fix the relative links and anchors in `docs/env/ENVIRONMENT.example.md`,
   `docs/env/ENVIRONMENT.md`, and `README.ko.md` so each one matches the
   headings step 3 just wrote. When `language` is `ko`, fix `README.md` the
   same way, because step 2 produced it by copying rather than translating.
   Each of these files keeps its own language. Only their link targets change.
   Do not touch the copies under `archive/en/`.
5. Verify the result before reporting. Confirm that every relative link in every
   changed file resolves to a file that exists, and that every anchor matches a
   heading actually present in the target file. Confirm that `fast path:`,
   `standard workflow:`, `approve execution plan #<number>`, and the `Status` and
   `Priority` field names and values are still in English. List every file that
   failed a check. Do not report the switch as complete while a check fails.
6. Do not modify anything under `archive/en/` after step 1. It is the reference
   copy, and it wins when a translation and its original conflict.
7. Report any document that could not be translated and why.

When `language` is `en`, do not create `archive/en/`.

## Reconfiguring

Running setup again updates `docs/env/ENVIRONMENT.md` in place.

Changing `language` after a previous switch runs
[switching language](#switching-language) again for the new language. Step 1
finds `archive/en/` already present and leaves it alone, so step 2 translates
the English reference copy rather than an existing translation.

Changing `language` back to `en` is different. Copy every file from
`archive/en/` back to its original path, delete `archive/en/`, then fix the
links in `docs/env/ENVIRONMENT.md` to match the restored English headings. The
restore already returns `docs/env/ENVIRONMENT.example.md` to its English form,
so it needs no further repair.
