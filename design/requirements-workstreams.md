# Demand and requirements: directing parallel workstreams (ledger 326)

Author: the agent (Claude Opus 5.5). Principal: the principal of `design/requirements.md`. Date: 2026-11-24. Interviewed: the principal in the chat, starting from the record (uellue-bot/skills#3 points 13 to 20, #6 option 3; ledger 308 and topic ledger 324 in `design/requirements.md`; the prior-art survey of 2026-11-24, `design/research/multi-agent-prior-art.md`).
Markers per item: [answered] said or confirmed by the principal; [record] taken from the source named, not yet confirmed; [assumed] proposed by the author; [unknown] listed in 2.2 with what would resolve it; [deferred] listed under open questions with stage and owner.

Where it lives: a file of its own in this repository while the requirements and a coarse architecture are worked out; moved as a whole to where the result belongs (a tool of its own, devvm, the plugin) once that is known (principal, 2026-11-24: "we can also transfer to the right place once we know requirements and coarse architecture").

How to read this and the related records (principal, 2026-11-24): "nothing here nor there is 'the law'. we are drafting things and challenging and changing our minds until it 'feel right'. Then we try sth, learn and adapt." Earlier decisions (in `uellue/modama`, branch `crawler`, `python/crawler/design/working-with-agents.md` and `working-with-agents-routing.md`, 2026-11-16) are inputs, not constraints; where today's answers differ, they are compared, not overruled.

Terms: `GLOSSARY.md`; "workstream" chosen 2026-11-24 over "thread", which the record below still quotes.

## 1. Demand

### 1.1 Problem and context
- [answered] This is the swarm that topic ledger 324 deferred ("the principal will direct a swarm of agents later"); agents working on someone else's project (public tracker #18) come once our own projects are under control (principal, 2026-11-24).
- [record] One chat holds several ideas at once; in energymanager six workstreams ran in one sitting of about 2.5 hours (HTTPS, alerts, a collector, issues, a hook, a browser install), and in one day a watcher, four research agents, two manual readings, seven photo readers, a build with review, a deploy and a walk-through ran side by side (uellue-bot/skills#3, points 13 and 17).
- [record] What slipped between workstreams: an approval given for text never shown (13); code deployed before its review (14); the $20 cost ceiling passed before it was reported (15); decisions spread over queued chat messages, issue comments and the debrief, with no list of what is open where (16).
- [record] Every finished agent's report lands in the main context, and the questions of different workstreams share one numbering (17); separate sessions on one checkout get in each other's way (18); an issue watcher stopped every 30 minutes and was restarted seven times, each a full-context turn (20).
- [record] On a day with many parallel agents in another project, open items scrolled out of sight ("What else is open? It just scrolls out of sight so quickly here"); answers on an open-items page were missed, sub-agents' numbers collided, done work showed as running (ledger 308).
- [record] The agent lost the larger plan while working on the current step: a walk-through started in the wrong project phase (uellue-bot/skills#6).
- [answered] All of the above belong to the problem (principal, 2026-11-24). The watcher restarts were "the least of the problems. But still good to have sth that picks up new comments etc reliably."
- [answered] Not yet covered, and possibly not the whole problem either (principal, 2026-11-24):
  - Collaborating with other people, and with agents that are not ours.
  - Telling what is important for the principal to review and what is not: in earlier workstreams, trying a quick prototype worked better than reviewing a spec, and a hallway test brought up much that a review of the spec would not have.
  - Enrolling everything on a host and in a VM or devcontainer: forks, clones, keys, logins, shared folders, dependencies.
  - Enrolling a larger existing project such as `LiberTEM/LiberTEM`, and taking over routine maintenance: fixing CI, resolving issues from upstream changes, the conda-forge work after a release, the release itself (LiberTEM's checklist, `docs/source/releasing.rst`: "I think a lot of that can be automated these days").
  - Contention and interference between agents and workstreams, for example one running a benchmark while another runs a test suite. [record] Seen so far: sessions sharing a checkout (uellue-bot/skills#3 point 18), shared state on a deployment host (same point); worktrees do not separate ports or databases (survey).

### 1.2 Current solutions
- [record] Today: one chat per project with sub-agents and Monitor watchers; the bot's fork as working tracker, one issue per topic; an author-labelling wrapper and guard hook so that only the principal's text on GitHub counts (energymanager; uellue-bot/skills#4); devvm VMs for isolation; the kaizen ledger and numbered items in the chat.
- [record] Closest prior art to the whole setup: Quarterdeck, a Claude Code plugin (background "goals" started by a schedule or a cheap watch, actions off, propose or auto, a "Needs you" pane and phone pushes, issue text read as untrusted); agy-hq (a local event queue with leases, hash-bound approvals). Both from the Reddit post on the always-on guide, pasted by the principal 2026-11-24 (`design/research/multi-agent-prior-art.md`).
- [answered] Quarterdeck is relevant prior art and a source of new questions, not a reference design, for now: first refine our own workflows and requirements (principal, 2026-11-24). Its fit against this setup is in the survey's last section.
- [record] Prior art (survey 2026-11-24, `design/research/multi-agent-prior-art.md`, key quotes spot-checked): orchestrators that give each agent a worktree or container (Gas Town with Beads, Claude Squad, Conductor, Sculptor, container-use, Sandcastle); trackers as work queues with the human as owner and the agent as delegate (GitHub Copilot coding agent, Agent HQ, Linear agents, OpenHands); Claude Code's agent view (a "needs input" queue across background sessions), agent teams (experimental), cross-session messaging, worktrees, scheduled tasks; Anthropic's always-on agent primitives (event inbox, timers, memory, permissions, outbox; an approval ends a run and comes back as an event). Not found: a ceiling on spend across sessions; a bot account as identity for Claude Code's cloud sessions; messaging between VMs without Remote Control.

### 1.3 Benefits
- [answered] The limit today is the principal's attention: two sessions fed at once, more needs task switching, which is "hard and costly"; e-mail and meetings compete for the same attention (`design/walkthroughs/principal-day-managing-agents.md`, A6). So the benefit sought is more work done per unit of the principal's attention, not more agents.
- [answered] Figures: "I have too many. I think the total may be around 15-20 across maybe 10 projects or so -- I need to reduce, delegate, streamline, batch-process, automate to reduce task switching" (principal, 2026-11-24). So the aim is fewer switches for the same 15 to 20 workstreams, not more workstreams.

### 1.4 Users
- [record] The manager (`design/stakeholders.md`), here the principal, directing several projects (skills, energymanager, devvm, modama) through the bot account; the coding agents and sub-agents doing the work.
- [answered] Users: the principal first, then a modama colleague who knows devvm (as in topic ledger 324's walk-through); nobody beyond that for now (principal, 2026-11-24).
- [answered] The principal's day as it is now, with what annoys them (A1 to A17) and what works well (W1 to W6): `design/walkthroughs/principal-day-managing-agents.md`.

## 2. Requirements

Not asked yet, except where noted; to follow the demand.

### 2.9 Jurisdictions
- [record] Terms that govern unattended runs (read 2026-11-24 through WebFetch, which renders pages with a small model; not compared character by character):
  - Claude Code docs, "Legal and compliance" (code.claude.com/docs/en/legal-and-compliance): the Consumer Terms apply "for Free, Pro, Max, and Ultra users", the Commercial Terms "for Team, Enterprise, and Claude API users"; "Advertised usage limits for Pro, Max, and Ultra plans assume ordinary, individual usage of Claude Code and the Agent SDK"; OAuth sign-in "is designed to support ordinary use of Claude Code and other native Anthropic applications"; developers building products, including with the Agent SDK, "should use API key authentication"; nothing prevents "an end user from signing in to the unmodified Claude Code binary with their own Claude subscription".
  - Consumer Terms (anthropic.com/legal/consumer-terms, effective 2025-10-08), section 3, a prohibited use: "Except when you are accessing our Services via an Anthropic API Key or where we otherwise explicitly permit it, to access the Services through automated or non-human means, whether through a bot, script, or otherwise"; section 2: "You may not share your Account login information … or make your Account available to anyone else."
  - [assumed] Reading, for a consumer plan: Claude Code's own background features (background sessions, `/loop`, scheduled tasks, Remote Control) are Anthropic's product and count as permitted; a script outside Claude Code that drives it on a subscription login is the grey zone; sharing one login between people, or running other people's work on it (public tracker #18), is excluded. Not legal advice; the Usage Policy itself was not read.
- [answered] The VMs' Claude login is on an ER-C Team plan (principal, 2026-11-24), so the Commercial Terms apply, not the Consumer Terms above.
- [record] Commercial Terms (anthropic.com/legal/commercial-terms, effective 2025-06-17, read the same way): no restriction on automated or non-human access found; D.5: "Customer is responsible for all activity under its account"; D.3: "It is Customer's responsibility to evaluate whether Outputs are appropriate for Customer's use case." The docs' sentence on "ordinary, individual usage" names Pro, Max and Ultra, not Team.
- [deferred] What the ER-C Team plan's admins allow or limit (seat usage limits, extra usage, features such as Remote Control or cloud sessions turned off): asked of the plan's admin once the design names the features it needs (principal, 2026-11-24).

## Release
Decides: the principal. Builds: the principal with Claude. Pays: ER-C (tokens). Signs off: the principal. Owns the shared systems it acts on: [unknown] (GitHub repositories of the principal and of organisations; the VM hosts).

## Open questions
- What the ER-C Team plan allows (2.9), for the plan's admin, deferred until the design names the features it needs.
- Other people's projects (public tracker #18), for the principal, resolves 1.1, deferred until our own projects run this way. Prior art for it: Overflow and the r/ClaudeAI thread in `design/research/multi-agent-prior-art.md`.
