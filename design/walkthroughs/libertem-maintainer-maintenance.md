# Walk-through: the LiberTEM maintainer keeps the project healthy between releases

Persona: the LiberTEM maintainer (`design/stakeholders.md`), here the principal. Confidence: interviewed (the principal answers in the chat, 2026-11-24); co-maintainers, users and conda-forge's bots simulated. Part of `design/requirements-workstreams.md` (ledger 326); follows `design/walkthroughs/libertem-maintainer-release.md`.
What exists: as in the release walk; on the feedstock `conda-forge/libertem-feedstock`, pull requests by `regro-cf-autotick-bot` (version updates, rebuilds for new Python versions; "Rebuild for python 3.15" open on 2026-11-24) and `conda-forge-admin` (rerenders); dependabot on the principal's repositories.

## How it is now (principal, 2026-11-24)

- **Stale issues.** "We do have things going stale: A new issue that we didn't follow up with ends up on the bottom of the pile while we work on sth else, like here right now."
- **Breakage noticed by accident.** "Usually we notice when we open a PR and it doesn't work, or we install LiberTEM somewhere and it fails."
- **Dependabot across all repositories.** "The dependabot mails and similar come across all our repos and I don't find the time to press 'merge, merge, merge' -- it is a lot more tedious than necessary, also because I didn't warm up with GitHub's inbox features."
- **The work itself is routine.** "Looking after these is I think pretty braindead (Change is small and plausible, CI passes resp. ran into a new upstream issue [vomit], fix said issue [deprecation, changed behavior, new bug, dependency resolution clash e.g. the amazing numba pins where we explore the full syntax of Python requirements specifications -- what fun ...] one way or another, press merge)".

## Steps

1. **Dependency and feedstock pull requests.** Could be: agents go through them across all repositories, fix what fails, and offer the green ones as one batch; later merged under a standing approval (N7, N8, N42).
2. **New issues.** Could be: read within a day or two; a safety check before touching anything from the report (N44); a labelled bot reply where it adds value, such as "reproduced".
3. **Breakage.** Could be: a scheduled CI run against the newest dependencies, an agent trying the fix (N45).
4. **Heavy runs.** Today: a dedicated CI machine; someone's agents starting heavy runs out of nowhere once led the principal to deactivate an SSH key until they could reach the person (N46).

Needs N44 to N46 are recorded in `design/walkthroughs/principal-day-managing-agents-could-be.md`.

## Answers from the principal (2026-11-24)

- **Dependency pull requests across all repositories:** yes to the two stages. Now: an agent goes through them, fixes what fails in a small pull request of its own, and offers the green ones that only touch requirements as one batch to approve. Later: a standing approval per repository for that kind of change, merged by the agent and shown in the morning recap.
- **conda-forge's bots:** yes, our agent treats their pull requests like a colleague's. "Most helpful would be resolving issues with new Python version, changed requirements etc. Usually not difficult, just tedious to go through the long requirements lists of complex projects like LiberTEM. For simple projects like sparseconverter we have activated automerge on conda-forge."
- **New issues:** labelled bot replies "are fine if they add value, e.g. notifying that an issue was reproduced. However, these are a bit 'hot' -- trick the agent into downloading a file and then interacting with it, running code, stuff like that. I think the first step here, and that's also reusable, is a robust check if that makes sense, any possible implications. If running a few harmless looking commands or opening a file can compromise a system through LiberTEM, that is also an issue -- just not what was reported necessarily." Example: hyperspy/rosettasciio#457 (loading PRZ files unpickled their metadata, so opening a data file could run arbitrary code).
