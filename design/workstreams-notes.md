# Notes: directing parallel workstreams (ledger 326)

The one-page state of the topic, for the next session to read first (N34). Updated 2026-11-24, at the end of the requirements stretch.

## Read first

1. `design/requirements-workstreams.md`: demand, use scenarios U1 to U8, sections 2.2 to 2.12, open questions. The how-to-read line at its top: nothing is binding; draft, challenge, try, adapt.
2. `design/walkthroughs/principal-day-managing-agents-could-be.md`: needs N1 to N46 in the principal's words. The requirements cite them by number.
3. Only where a need points there: the day as it is (`principal-day-managing-agents.md`), the LiberTEM walks (`libertem-maintainer-release.md`, `libertem-maintainer-maintenance.md`), `design/research/workstreams-mapping.md` (the record of 2026-11-16 in `uellue/modama`, branch `crawler`), `design/research/multi-agent-prior-art.md` (Quarterdeck, agy-hq, Anthropic's always-on primitives, agent view).

## State

- Requirements done and read back with the principal; acceptance examples E58, E60 to E66.
- Next step: a coarse architecture. Its first question is N29, where things live: not "a monstrous monorepo", but a structure that fosters development, testing, maintenance, collaboration and reuse. The "things" are the method (kaizen's skills), the tooling to be built (inbox and routing, the morning view, the authorised officer, the credential manager setup), each project's records, the principal's portfolio, and shared know-how such as the prior-art library for the reuse check (N37). The routing of 2026-11-16 (R1 to R16) is an input, reopened.
- Order of the architecture step, agreed 2026-11-24: first the needs against existing tools (`design/research/workstreams-tools.md`, done; its section "What is left over" holds what was agreed per gap, none needing code of our own yet; the Claude Code facts behind it in `design/research/claude-code-features.md`), then where things live (N29, next), then each gap challenged before code of our own. With it (principal): "keep a note to remain super flexible. Deferring or dropping requirements, or going for sth somewhat similar are also options at this stage."
- The principal's own account for N29 (2026-11-24): "managing multiple agents, token management for agents etc feels like it doesn't quite fit anywhere. It is not modama, not a skill, not a devvm"; and the authorised officer "sounds like it may need a separate repo to separate it from the rest." "Token management" there is to be "read as general credential management, authorization, authentication". Usage limits: "no second seat for now. We keep an eye on usage, see how it goes."
- The principle for the architecture (N47): existing tools wherever possible, linked together; "Not cook a whole lot of code." Every piece of code of our own names why no tool does it, and an adversarial fresh agent challenges that claim before the code is written.

## Open, with who answers

- Who does the security analysis, and when (A16, N20, N25, N41, N43): the principal; it gates anything that posts on GitHub or holds credentials.
- The next interview round: consequences of failure, network and accounts, lifetime, the end-of-day view (open questions in the requirements).
- The branch: this topic's commits sit on `kaizen-0.6.2`, the head of the working tracker's draft pull request #2 (enrollment); cleaned up as part of resolving N29 (principal, 2026-11-24).
- Deferred: what the ER-C Team plan allows (its admin, once the design names features); LiberTEM's co-maintainers, with concrete cases, once a trial is near; other people's projects (public tracker #18).

## How the principal likes to work on this

Short rounds, one step at a time, leans to accept or correct; their own account before an agent's draft; quotes over paraphrase; check other repositories' records before drafting (this topic's earlier draft in modama was found late, ledger skill friction 2026-11-24).
