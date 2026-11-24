# Coarse architecture: directing parallel workstreams (ledger 326)

Author: the agent (Claude Fable 5.2). Decides: the principal. Date: 2026-11-24. Built on `design/requirements-workstreams.md` (needs N1 to N47 in `design/walkthroughs/principal-day-managing-agents-could-be.md`) and on `design/research/workstreams-tools.md`, which sets each need against existing tools.
Markers: [agreed] said or accepted by the principal on the date given; [lean] the agent's proposal, not yet read back.

How to read it: nothing here is binding; it is tried, and changed by what the trial shows. "Deferring or dropping requirements, or going for sth somewhat similar are also options at this stage" (principal, 2026-11-24).

## 1. The principle

- [agreed] Existing tools, linked; no code of our own without a reason that a fresh, adversarial agent could not knock down (N47).
- [agreed] The tools table found no gap that needs code of our own yet: what the needs add to GitHub, Claude Code and devvm is set-up that spans projects and machines (label names, timers, hooks, settings text, briefs). So the architecture is mostly an answer to where that set-up and its records live (N29).

## 2. Where things live

[agreed] The map as a whole (principal, 2026-11-24: "that makes a lot of sense, matches with what I had in mind roughly"); it keeps the routing of 2026-11-16 (R1 to R16, `design/research/workstreams-mapping.md`) except where the last column says otherwise.

| Home | Holds | Readers and writers | Against 2026-11-16 |
|---|---|---|---|
| `uellue/skills`, the kaizen plugin (public, exists) | The method: skills, standing instructions, research. The reuse check and the first entries of the prior-art library (N37), until the library has a life of its own | anyone who installs it; changed through the working tracker | same (R2) |
| Werkstatt, the repository for directing agents (public, new) | What had no home: label names, timers, hooks, settings templates, the briefs for triage, missions and housekeeping; how to set it up. This topic's records move there (requirements, walks, research, this file) | managers and their agents; others take from it what fits their work style (N14) | same (R3), now with content |
| The principal's portfolio (private, new, on GitHub) | The portfolio's inbox (ideas, exceptions, what has no workstream yet), the one-page overview of what exists (N38), priorities, the principal's settings for every agent (N28) | the principal and the agents working for them | same (R8); on GitHub, not the institute's GitLab |
| The authorised officer (public, later) | Workflows and the policy for approvals, in one repository; created when the security analysis names its content | the principal writes; agents read | one repository, not two (R5, R15) |
| devvm (exists) | VMs, the enrolment of the bot account, putting the homes above into each VM | as today | same |
| Each project (exists) | Its records, its workstreams' issues in its working tracker, the rules that guard its own merges and releases | as today | same |

- [agreed] Why a repository of its own for directing agents, and not a second plugin in `uellue/skills`: it is "not modama, not a skill, not a devvm" (principal, 2026-11-24); it leans on features still in preview and will change faster than the method.
- [agreed] Its name, and the word for the whole organisation one manager directs: Werkstatt ("Ok, werkstatt it is", principal, 2026-11-24). Wanted was a term for the whole, "like swarm, but organized and coordinated". The picture is the master's workshop: several pieces on the benches at once, each at its own pace; the master walks through, unlocks and leaves; nothing leaves without the master's sign-off; each master runs their own, in their own style. Set aside: orchestra (the most crowded word in the field; one score, and a conductor present for every bar), the English "workshop" (reads as a training event), brigade and ensemble (taken, by a GitHub search the same day). Its weak side: it does not say "many at once".
- [agreed] The portfolio on GitHub (principal, 2026-11-24): "Let's use GitHub and be a bit conscious what we put there. Private, but no highly sensitive / damaging / 'hot' info. We don't want to send that to Anthropic anyway, so that's fine. GitHub and Anthropic roughly in the same trust level." So one rule covers both: what must not go to Anthropic does not go into the portfolio either. The bot account on the institute's GitLab did not get going so far, for want of details, not for a technical reason.
- [lean] The portfolio repository is owned by the bot account, with the principal as collaborator, like the working trackers: the bot's token reaches only what the bot owns (working tracker #3, point 7).
- [agreed] The officer as one repository, policy included ("sounds good!", principal, 2026-11-24): in the fork model every change passes the principal's merge. Separate from the rest, as the principal asked the same day.
- [agreed] Credentials live in no repository. "Token management" is read as "general credential management, authorization, authentication" (principal, 2026-11-24). The inventory is the password manager and GitHub's list of tokens (N20); which tools, and what the officer holds, wait for the security analysis.

## 3. How the homes are linked

The few conventions that make the tools one system; each is a row of the tools table, accepted there as a lean ("rest ok!", principal, 2026-11-24), and none is tried yet.

1. [lean] **A workstream is an issue.** One issue in its project's working tracker, and at most one session that writes, named after the issue (N2, N19, N33). When the session winds down, its state goes into a comment and its notes file (N34).
2. [lean] **Labels are the shared vocabulary.** A handful of label names, defined once in the repository for directing agents and created in every tracker: waits for the principal, mission candidate, blocked, exception. Their names are open; they go through `GLOSSARY.md`.
3. [lean] **Two lists make the morning view.** What waits: one search across owners by label, as a command and as a link that also works on the phone (N1, N2). Who is live: Remote Control's list of sessions (N4, N19). They are joined by eye, through the session's name.
4. [lean] **Ideas go to the portfolio's inbox.** One issue each, written from the phone or said to a session; a triage session proposes where it belongs, and the principal's comment confirms it (N10 to N12, N16). Only the principal's text counts as a confirmation (working tracker #3, point 8).
5. [lean] **Work in the background starts from a timer.** A cheap check outside the model looks for something new and only then starts a run, with a dollar ceiling per run; results go to the working tracker (N7, N15).
6. [lean] **Settings travel by file.** The principal's settings live in the portfolio, devvm puts them into each VM, and a hook adds them again after a context summary (N28, N31).
7. [agreed] **Approvals stay in the chat** until the security analysis is done (N43); merges go through pull request review as today.
8. [lean] **The network learns through the same lists.** An exception is a labelled issue in the portfolio's inbox; each project keeps its debrief and ledger; the measure to start with is how many issues asked for the principal per day (N32).

## 4. What waits, and for whom

- The security analysis (requirements 2.11): the principal says who does it and when. It gates the officer, anything that posts outside the working trackers, and new credentials. New for it: Remote Control lets whoever holds the claude.ai login answer sessions inside the VMs.
- The Team plan's Owner: the switch for Remote Control, and unattended runs on a timer under the Team login (`workstreams-tools.md`, "For the Team plan's Owner").
- To try before relying on it: the list in `workstreams-tools.md`, and its rows marked **r**.
- `GLOSSARY.md` has "inbox" as the ledger's notes section; the portfolio's inbox is the same idea one level up. [lean] Widen the entry instead of adding a second word.
- Where the guard goes that lets only the principal's text on GitHub count (working tracker #4): kaizen, the repository for directing agents, or the officer's.
- The next interview round (requirements, open questions): consequences of failure, network and accounts, lifetime, the end-of-day view.
- Usage: "no second seat for now. We keep an eye on usage, see how it goes" (principal, 2026-11-24).

## 5. A first trial

[lean] The smallest slice that shows whether the lists work, with nothing that posts in a new place or holds a new credential:

1. Ask the plan's Owner for Remote Control; try it from inside one devvm VM.
2. Create the two repositories and move this topic's records; that also takes this topic's commits off the kaizen release branch.
3. In two projects where the bot account already works (skills, energymanager): the labels, the search link, sessions named after their issues, the settings file with its hook.
4. After a week, a debrief: did the morning check-in get shorter, and how many issues asked for the principal per day.
5. Then the timer for events (convention 5), and the next need by what the debrief shows.
