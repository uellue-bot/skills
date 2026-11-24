# Demand and requirements: directing parallel workstreams (ledger 326)

Author: the agent (Claude Opus 5.5). Principal: the principal of `design/requirements.md`. Date: 2026-11-24. Interviewed: the principal in the chat, starting from the record (uellue-bot/skills#3 points 13 to 20, #6 option 3; ledger 308 and topic ledger 324 in `design/requirements.md`; the prior-art survey of 2026-11-24, `design/research/multi-agent-prior-art.md`); sections 2.1 to 2.12 from the walk-throughs of 2026-11-24 in `design/walkthroughs/` (`principal-day-managing-agents.md`, A1 to A17 and W1 to W6; `principal-day-managing-agents-could-be.md`, needs N1 to N46; `libertem-maintainer-release.md`; `libertem-maintainer-maintenance.md`) and the comparison `design/research/workstreams-mapping.md`.
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
- [record] A second persona of the principal's: the LiberTEM maintainer (`design/stakeholders.md`), working with co-maintainers, outside contributors and conda-forge's bots, who see what the agents post (`design/walkthroughs/libertem-maintainer-release.md`, `libertem-maintainer-maintenance.md`).
- [answered] Affected, not users: LiberTEM's co-maintainers and contributors, who see what the agents post, and anyone whose machine or CI an agent's heavy runs share (N46) (principal, 2026-11-24).

## 2. Requirements

Needs are cited by number: N1 to N46 in `design/walkthroughs/principal-day-managing-agents-could-be.md`, A and W in `principal-day-managing-agents.md`; the walks keep the full wording. They are needs, not a design: how each is met is decided with the coarse architecture.

### 2.1 Use scenarios

Main, daily, the principal as manager:

**U1 Evening missions and the morning check-in** (could-be walk, step 1; as-is steps 1, 2, 11)
1. [answered] Before leaving: which projects have candidates for missions, proposals to adjust or approve, what blocks which workstream (N1).
2. [answered] Spare capacity goes to work without big consequences and without approval: research, tests, options, preparing a pending release (N7; where results go: 2.7, N7 amended; limits: 2.11, N8).
3. [answered] Results come in baby steps: "I wouldn't like to be greeted with a 100 files, 10k lines PR from a background job." (N9)
4. [answered] In the morning: one list of all workstreams across machines and VMs, each named by what it is about, with its state, a recap of the night and what is wanted from the principal (N2), reached without "SSH and screen juggling" (N4); "quick and easy is the requirement", the place is a detail (N6).
5. [answered] The check-in: "check in, understand the state, unlock things for maybe 30 min, dump ideas, and then leave things running" (N5); a notification only for a small unlock, "not if it turns into 1 h Q&A" (N3).
6. [answered] Open work, worktrees and changes bubble up, also as candidates to drop (N39; 2.10). [unknown] How the principal knows at the end of the day what is still open (as-is walk, "Not told yet"); N1 and N39 answer part.

**U2 Dropping an idea or someone's input** (step 2; A5, A10), any time
1. [answered] Dropped in seconds, from wherever, without choosing a project or session first (N10).
2. [answered] The agent proposes where it belongs, the principal confirms or corrects in a word, it is filed in a fitting form (N11); an idea with no fitting workstream still gets a place (N12).
3. [answered] No immediate polling: it moves down a pipeline "for as long as it can within the rules", prepared with research and questions, and "pops up" in the right workstream (N15); a short routing confirmation, whose form is only an example (N16).
4. [answered] Colleagues are not senders into the principal's pipeline: they keep their own, and it reaches the principal as an issue they are CC'd in (N13).

**U3 Working through the day across workstreams** (step 3; A1, A2, A6, A7, A13)
1. [answered] At most two workstreams talk to the principal at a time; the others wait in the queue of N3, "only the order is fine" (N17; the tension with a limit on what is produced: 2.2, N28).
2. [answered] Switching into a workstream shows a short recap: what it is, where it stands, what is asked (N18).
3. [answered] A workstream is reached without knowing its machine, VM or container (N19); its files open in a native tool the same way (N22); text to take elsewhere copies cleanly (N21).

**U4 Trying out and reviewing** (step 4; A11, A12, A14, W1 to W4)
1. [assumed] While the principal tries a prototype, what they see and do reaches the agent without copy and paste, shared by their choice per moment (N23; proposed, not contradicted).
2. [assumed] Pause, mark a spot, comment; the comment reaches the agent tied to the spot (N24; proposed, not contradicted).
3. [answered] Any artifact (HTML, graphics, notebooks) reviewed with comments anchored in place, "inspiration are inline comments in code review" (N26); code stays on GitHub and GitLab (W4).
4. [answered] Every result handed to the principal had an independent review first, specs too (N30; 2.7).

**U5 Approving** (step 5; A16)
1. [answered] One prepared decision, answered in a word, bound to exactly that act (N40); logged, standing approvals revocable (N42); where it happens matters less than proof it was the principal (N41); beyond minor acts in the chat until the security analysis (N43). Details: 2.11.

Periodic, the principal as the LiberTEM maintainer:

**U6 A LiberTEM release** (`libertem-maintainer-release.md`; the checklist in `docs/source/releasing.rst`)
1. [answered] Started by "someone", often offline; an agent may propose it as part of maintenance.
2. [answered] Step 0: the agent does small pull requests for review (deprecations, dependencies, link check, change log draft), runs and reports the full CI, and prepares what the maintainers decide, with open issues and pull requests sorted into proposed resolutions and "for review" (E60).
3. [assumed] Step 1: the maintainer tags and pushes; an agent checks what it reaches without new credentials.
4. [answered] Step 2: every box, every release, by agents ("literally 'tick all the boxes'"): the full suite on the VM, CUDA in a devcontainer, Windows arranged, the GUI QA as a hallway test, the five related packages as a deterministic CI job the agents build and maintain; the maintainer spot-checks what is flagged (E58).
5. [assumed] Steps 3 and 4: the agent drafts the release notes, the announcement and the post-release pull request; the maintainer pushes the tag, publishes, sends the mail, enters the publication database.
6. [assumed] E58 and E60 in `design/examples.md` (priorities the agent's lean).

**U7 LiberTEM between releases** (`libertem-maintainer-maintenance.md`)
1. [answered] Dependency and feedstock pull requests across all repositories: now an agent fixes what fails in a small pull request of its own and offers the green, requirements-only ones as one batch; later a standing approval per repository, merged by the agent, shown in the morning recap. conda-forge's bots are treated like a colleague.
2. [answered] New issues: a safety check before touching anything from a report (N44); a labelled bot reply where it adds value, such as "reproduced". [assumed] Read within a day or two.
3. [answered] Breakage found before someone trips over it (N45); heavy runs only where expected (N46).

Periodic, the whole network:

**U8 The network learns** (N32, N36)
1. [answered] An agent raises an "exception" to a collector, which groups it into the debrief and interrupts the principal only for what blocks a workstream now; sub-agents may push back against the agent that controls them (N32).
2. [answered] Lessons become checks, rules or skills that load by themselves (N36).

Optional, not walked: [answered] a modama colleague who knows devvm works the same way (1.4, N14); [deferred] other people's projects (public tracker #18).

Acceptance examples: E58, E60 to E66 in `design/examples.md`.

### 2.2 Demand uncertainties
- [unknown] Whether ordering who talks (N17) is enough, or a limit on what is produced, sized to review capacity, is needed too (`workstreams-mapping.md`, tension at N17; O6 of 2026-11-16); N28 makes it a setting per manager. Resolves: trying it, measured as in N32.
- [unknown] Baselines for the principal's minutes per merged change and decisions asked per day (N32): none measured. Resolves: a first measurement before the first change.
- [unknown] How the principal knows at the end of the day what is still open (U1.6). Resolves: the next round with the principal.
- [unknown] Whether LiberTEM's repositories count as "spaces that we control" (N7 amended), where bot replies (U7.2) may be posted directly. Resolves: the principal with the co-maintainers.
- [unknown] Whether a "clean" environment, such as a GUI in a remote desktop session, can replace reviewing what leaves the screen (N25). Resolves: the security analysis.
- [unknown] Who else adopts the tooling, and with which work styles (N14). Resolves: the modama colleague's first try.
- [record] The Claude Code features the prior art points to are moving: agent view "is in research preview", agent teams are experimental (`multi-agent-prior-art.md`). Resolves: a check with `claude-code-guide` at the architecture step.
- [deferred] What the Team plan allows (2.9); the security analysis (2.11); where things live (N29, 2.8).

### 2.3 Interfaces with existing systems
- [answered] Existing tools wherever possible, linked together "in a smart way. Not cook a whole lot of code." (N47; principal, 2026-11-24). It applies to every section: the architecture starts from what exists (Claude Code's own features, GitHub, gh, devvm, Quarterdeck-like plugins) and names each piece of code of our own with the reason no tool does it, and that claim is challenged by an adversarial fresh agent before the code is written (principal, 2026-11-24).
- [record] GitHub, outside our control: the working tracker and the public tracker, the principal's repositories, organisations' repositories (`LiberTEM/LiberTEM`, `conda-forge/libertem-feedstock`, upstream projects such as `hyperspy/rosettasciio`); issues, pull requests, labels, Actions, dependabot; the bot account as identity, with only the principal's text counting (1.2). GitLab too for review [answered] (W4).
- [record] CI beyond GitHub: Azure DevOps for the full pipeline, a dedicated CI machine for heavy runs, LiberTEM's VM, a devcontainer for CUDA, Windows on request (release walk; N46).
- [record] Release targets, outside our control: PyPI, ghcr.io, Zenodo, conda-forge and its bots, the institute's publication database, the announcement mail; failures seen were "upstream services misbehaving or not being available" and errors swallowed (release walk, principal).
- [answered] The ER-C data management system holds LiberTEM's test files (release walk).
- [answered] Credentials through existing tools and infrastructure, "e.g. gh handling the GitHub token, not Claude"; the model that works is the Firefox password manager (N20; 2.11).
- [record] Claude Code and Anthropic, outside our control: sessions and background features, the 5-hour and weekly usage limits (N8), the Team plan (2.9).
- [answered] Input arrives by e-mail, group chats and meetings (A6, A10); colleagues' own pipelines reach the principal as issues (N13). [record] Important approvals with colleagues go through the institute's digital workflows (as-is step 10).
- [answered] Private deployments (modama, the crawler): one idea is an agent inside, working with models approved for private data such as blablador, reporting sanitised results out (N27); related: MA2, O28, O29 in `uellue/modama`, branch `crawler`.
- [answered] An overview of what exists, possibly generated from devcontainer and devvm files, `requirements.txt` and the like (N38; 2.7).
- [answered] Stand-ins: a dedicated space where background results are tried before they are posted (N7 amended); a filtered copy or a recreated scenario for private data (N27).

### 2.4 Operating conditions
- [answered] Two machines, each with several devcontainers and VMs; sessions in `screen`; reached from the home laptop and the work machine (as-is steps 2 and 5).
- [answered] One writer per area, many readers and reviewers, with workspaces added as needed so that parallel work does not interfere (N33; 1.1, contention).
- [answered] A fresh session per area or task, its state in a notes file (N34).
- [answered] Heavy runs only where they are expected, such as the dedicated CI machine; failed runs not retried in a loop (N46; 2.11); no "excessive resource use" that interferes with normal work (N8).
- [unknown] Network, mounts, permissions and the account each session runs as; whether the machines reach each other. Resolves: the architecture step, from devvm's record.

### 2.5 Availability and reliability
- [answered] New comments and events are picked up reliably (1.1, the watcher).
- [answered] The principal's limits and settings hold for every agent at every point of a session, also after a context summary; a check enforces them where it can (N31; 2.11).
- [answered] How a wrong result is noticed: the independent review (N30), scheduled runs against the newest dependencies (N45), exceptions raised to a collector (N32).
- [deferred] A check that every release asset and step exists, catching what the scripts swallowed: "nice, but less important for now" (release walk, options for later).
- [answered] Nothing mission-critical depends on it: when it is down, work falls back to today's chats. "yes for now -- we test iteratively and see how it goes" (principal, 2026-11-24).

### 2.6 Performance
- [answered] 15 to 20 workstreams across about 10 projects; the aim is fewer switches, not more workstreams (1.3).
- [answered] Two workstreams in active talk at a time (A6, N17); a check-in of about 30 minutes (N5); an idea dropped in seconds (N10).
- [answered] Results small and reviewable, not "100 files, 10k lines" (N9).
- [answered] Usage limits (5-hour, weekly) are not burnt on background work (N8); tokens "were rarely the limit in practice, except when cooking skills" (N30), yet unused tokens at night bring "a form of FOMO" (A17). [record] Default cost ceiling $20 at list price per stretch (standing instructions); passed once before it was reported (1.1).
- [answered] How much goes to the manager, and at what size, is a setting per manager (N28; 2.7).
- [unknown] Figures for N32's measures; how fast a notification must arrive (N3). Resolves: the first measurement (2.2).

### 2.7 Testing, installation and service
- [answered] An independent review before the principal's look, specs included, with a different model where it saves the principal's attention (N30); a reuse check is part of it, "a project in itself" (N37; where it lives: 2.8).
- [answered] Settings per manager (how much detail, what they review) reach every agent working for that manager: "Some *.md file in today's world probably?" (N28).
- [answered] Agents and people know what exists across projects, without duplicating the requirements files (N38).
- [answered] Background results are tried first in a dedicated space, then forwarded by the principal, and only later posted directly in spaces we control (N7 amended).
- [answered] GUIs are tried by hallway test, as a colleague's across several VMs for the modama ingest client, which "works extremely well" (release walk).
- [answered] Diagnostics end in a debrief (N32); lessons become checks (N36); housekeeping (N39, 2.10).
- [answered] Operated by the principal, with Claude, "yes for now. Other colleagues will likely run their own instances, 'command chains'" (principal, 2026-11-24; N14).

### 2.8 Portability and distribution
- [answered] Reusable tooling, so that others work in a compatible way, allowing for other work styles (N14).
- [answered] Runs wherever the workstreams run (2.4); where the principal checks in is a detail (N6).
- [deferred] Where things live: not "a monstrous monorepo", but "a meaningful structure that fosters development, testing, maintenance, collaboration and reuse"; the routing of 2026-11-16 (R1 to R16) is reopened; the reuse check and its prior-art library go with it (N29, N37).

### 2.9 Jurisdictions
- [record] Terms that govern unattended runs (read 2026-11-24 through WebFetch, which renders pages with a small model; not compared character by character):
  - Claude Code docs, "Legal and compliance" (code.claude.com/docs/en/legal-and-compliance): the Consumer Terms apply "for Free, Pro, Max, and Ultra users", the Commercial Terms "for Team, Enterprise, and Claude API users"; "Advertised usage limits for Pro, Max, and Ultra plans assume ordinary, individual usage of Claude Code and the Agent SDK"; OAuth sign-in "is designed to support ordinary use of Claude Code and other native Anthropic applications"; developers building products, including with the Agent SDK, "should use API key authentication"; nothing prevents "an end user from signing in to the unmodified Claude Code binary with their own Claude subscription".
  - Consumer Terms (anthropic.com/legal/consumer-terms, effective 2025-10-08), section 3, a prohibited use: "Except when you are accessing our Services via an Anthropic API Key or where we otherwise explicitly permit it, to access the Services through automated or non-human means, whether through a bot, script, or otherwise"; section 2: "You may not share your Account login information … or make your Account available to anyone else."
  - [assumed] Reading, for a consumer plan: Claude Code's own background features (background sessions, `/loop`, scheduled tasks, Remote Control) are Anthropic's product and count as permitted; a script outside Claude Code that drives it on a subscription login is the grey zone; sharing one login between people, or running other people's work on it (public tracker #18), is excluded. Not legal advice; the Usage Policy itself was not read.
- [answered] The VMs' Claude login is on an ER-C Team plan (principal, 2026-11-24), so the Commercial Terms apply, not the Consumer Terms above.
- [record] Commercial Terms (anthropic.com/legal/commercial-terms, effective 2025-06-17, read the same way): no restriction on automated or non-human access found; D.5: "Customer is responsible for all activity under its account"; D.3: "It is Customer's responsibility to evaluate whether Outputs are appropriate for Customer's use case." The docs' sentence on "ordinary, individual usage" names Pro, Max and Ultra, not Team.
- [deferred] What the ER-C Team plan's admins allow or limit (seat usage limits, extra usage, features such as Remote Control or cloud sessions turned off): asked of the plan's admin once the design names the features it needs (principal, 2026-11-24).
- [answered] Private user data of modama and the crawler is not shown to the agent: a filtering or anonymisation stage, or the scenario recreated in a test environment (A15, N27); the principal decides as "sort of the steward … on behalf of the users and the institute", conferring with leadership and colleagues (N27).
- [answered] Screen content is not sent "unfiltered to Anthropic" (A12, N25).
- [answered] LiberTEM's test files are not public "because of sometimes muddy licensing, but uncritical to send to Anthropic servers" (release walk).

### 2.10 Lifetime and decommissioning
- [answered] Dropping work is a valid choice: open work, worktrees, ledgers and issues bubble up as candidates, to "avoid endless sprawl", while some may "lie around for a bit until the right moment" (N39).
- [answered] Sessions are wound down cleanly and restarted, so nothing gets lost (N34).
- [answered] Standing approvals stay until revoked and are reviewed during housekeeping (N42).
- [unknown] How long the tooling must live, and what happens to its records when it is replaced: not asked. Resolves: the principal, after the coarse architecture.

### 2.11 Safety and risks
- [answered] Never without the principal (N8): burning tokens needed elsewhere (weekly or 5-hour limits); bothering third parties or noise ("don't spam my inbox, please!"); excessive resource use; acts with real-world impact that is hard to undo (deploy, release, merge other than mechanical updates, and those only "tested well and hardened, and specifically 'perma-approved' for a repo").
- [answered] These limits are enforced outside the agent where a check can do it (N31).
- [answered] Approvals bound to the exact act and asked again when it changes (N40); proof that it was the principal, GitHub for minor acts once a security analysis holds, for major ones later (N41); a log and revocable standing approvals (N42); the chat until then (N43).
- [answered] Credentials: an inventory and fewer of them, handled by existing tools, not our code: "we better get that one right!" (N20); a credential manager for what only the principal controls kept apart from an "authorized officer" for four-eye access among agents, both hardened (N20 refined).
- [answered] One accountable person per agent; bots never speak as a person (N35).
- [answered] Before running commands, downloading or opening a file from someone's report, a check of what could happen; a way to compromise a system through the project is a finding of its own, reported to the principal first (N44; hyperspy/rosettasciio#457).
- [answered] Someone affected can stop another person's agent without revoking its access (N46).
- [answered] What the agent may see is set per project (N27, 2.9); a sanitised report from inside a deployment gets a check of its own (N27 amended); nothing from the screen leaves unseen (N25).
- [record] Slips seen so far: an approval for text never shown, a deploy before its review, the cost ceiling passed (1.1). Issue text as a route for prompt injection (survey, Codex cloud); Quarterdeck's trust by `author_association` would count the bot's own text on its fork (survey).
- [deferred] The security analysis that A16, N20, N41 and N43 wait for.
- Who may authorise acts on the systems: see Release; no lean given.

### 2.12 Applicable standards
- [answered] Terms: `GLOSSARY.md`.
- [record] The LiberTEM release follows its checklist (`docs/source/releasing.rst`, an issue template).
- [record] Vocabulary from other fields in the records: work-in-progress limit, andon, SBAR handovers (record of 2026-11-16, O2, O6); Crew Resource Management and the four-eye principle (the principal, N32, N20). [unknown] Which the project adopts. Resolves: the glossary, as terms come up.
- [record] Anthropic's terms (2.9); GitHub's terms on machine accounts (`design/requirements.md`, topic ledger 324). No formal standard named; not asked further, since none applies so far.

## Release
Decides: the principal. Builds: the principal with Claude. Pays: ER-C (tokens). Signs off: the principal. Owns the shared systems it acts on: [answered] the principal as steward for modama and the crawler (N27); [record] `LiberTEM/LiberTEM` the principal with co-maintainers (`design/stakeholders.md`), the feedstock conda-forge; [unknown] the rest (other organisations' repositories; the VM hosts).

## Open questions
- What the ER-C Team plan allows (2.9), for the plan's admin, deferred until the design names the features it needs.
- Other people's projects (public tracker #18), for the principal, resolves 1.1, deferred until our own projects run this way. Prior art for it: Overflow and the r/ClaudeAI thread in `design/research/multi-agent-prior-art.md`.
- Where things live (N29, with N37 and the routing R1 to R16 of 2026-11-16), for the principal, resolves 2.8 and 2.10, deferred to the coarse architecture.
- The security analysis (A16, N20, N25, N41, N43): who does it and with which tools, for the principal, resolves 2.11 and 2.3, deferred until the coarse architecture names what must be protected.
- The default for N28's setting and whether a limit on what is produced is needed besides N17, for the principal, resolves 2.2 and 2.6, deferred to the first trial.
- Whether LiberTEM's repositories are "spaces we control" for direct posting, and whether standing approvals for dependency merges need the co-maintainers' agreement, for the principal and the LiberTEM co-maintainers, resolves 2.2, 2.11 and Release, deferred to before agents post or merge there. Asked "later, with very specific examples: 'on this issue, the bot would have done this and that there, posted that comment, ...'" (principal, 2026-11-24).
- Consequences of failure (2.5), network and accounts (2.4), lifetime (2.10), the end-of-day view (U1.6): for the principal, deferred to the next interview round.- Credentials for LiberTEM's "Thorough" workflow and Azure pipeline (release walk, "a sort of TODO/option for later"), for the LiberTEM maintainers, resolves 2.11, deferred until the authorised officer exists.
- Models approved for private data for an inside agent (N27), for the principal with leadership, resolves 2.9, deferred to the modama and crawler work.
