# The "could be" walk's needs against the working-with-agents record of 2026-11-16

Made 2026-11-24 for `design/requirements-workstreams.md` (ledger 326). Needs N1 to N27 from `design/walkthroughs/principal-day-managing-agents-could-be.md`; options O1 to O39 and routing decisions R1 to R16 from `uellue/modama`, branch `crawler` (commit `0c8c50d`), `python/crawler/design/working-with-agents.md` and `working-with-agents-routing.md`, mapped by the main session; research verdicts from the five research notes in `python/crawler/design/working-with-agents/`, mapped by a sub-agent (**S** supports, **Q** qualifies, **X** argues against, **–** nothing found). Neither record is binding: both are drafts to compare (principal, 2026-11-24).

| Need | In the 2026-11-16 record | Research | Note |
|---|---|---|---|
| N1 mission candidates, proposals, blockers | O10 one digest and one queue; O16 portfolio map; R14 portfolio repository as headquarters | S/Q: Hashimoto's evening agents write reports, not code | new: proposals for missions to adjust or approve |
| N2 morning recap | O10 digest; O13 a crawl's end starts the morning digest | S/Q: progress file and git log; agent teams' "Task status can lag" | |
| N3 notify only for small unlocks | O6 andon in two pulls, batched decisions; MA7 decisions prepared with options | S/Q: Hashimoto turns notifications off; Osmani batches reviews | |
| N4 no SSH or `screen` juggling | – | – | new |
| N5 the 30-minute check-in | O6 decisions batched at fixed times | S/Q: mission command; long unattended runs waste effort | |
| N6 place is a detail | – | – | |
| N7 spare capacity without approval | O13; W8 lean: scheduled agents yes for read-only work | S for research and reports; X for posting upstream unasked (LLVM, EOSC policies) | **conflict** with O30 and O23: posting outside the team reserved to people |
| N8 never without the principal | O30 reserved acts; O31 tiers; W6 | S, with a caveat: after compaction, constraints are lost unless kept outside the conversation (Wang et al., unchecked) | new in N8: usage limits (5 h, weekly), noise, resource contention, "perma-approved" mechanical merges |
| N9 baby steps | O8 one logical change per merge request | S strongly: EOSC 2,000 lines or 10 files, kernel, larger agent PRs merged less | |
| N10–N12 drop, route, orphan ideas | O29 routing the person's streams, nearly word for word | Q: lessons stores go unused unless routed to action (NASA) | |
| N11 agent proposes the place | O29 | Q: routing by surface words; agreement by sycophancy | *(judgement)* the proposal should say why |
| N13 colleagues' own pipelines | O20, O22, O25 | S: kernel maintainers, CODEOWNERS | |
| N14 reusable tooling, other styles | O24 kaizen for everyone; O26 layers; R12 a tool-neutral guide | S/Q: copied practices fail without the concept (Spotify, Toyota) | |
| N15 pipeline, pops up in place | O29; O13 | S/Q: the issue as the hand-off; more agents than review lengthens the queue | |
| N16 routing confirmation | O29 "says in one line where it went" | S (weak) | |
| N17 two at a time, order only | O6 work-in-progress limit; W3 lean three | S for the limit; Q/X on "order only" | **tension**: the record limits what is produced, sized to review capacity; ordering who talks does not cap the review pile |
| N18 recap on switching | O2 a notes file per area (SBAR) | S/Q: handovers omit detail; recap from files, not from a summary | |
| N19 reach without knowing the machine | – | – | new |
| N20 credentials, existing tools, inventory | O30 limited credentials; O31 gatekeeper holds credentials; R5 officer repository; R13 requirement "safe, predictable, understandable and convenient", Vaultwarden as an option | – | **tension**: an officer that holds credentials is our own code handling them; R13's vault is close to the Firefox password manager model |
| N21 clean copy | – (ledger 308: copy blocks) | – | new |
| N22 native file access | – | – | new |
| N23 what the principal sees reaches the agent | drop box and recorded terminal (section 1, what worked) | S (weak): go and see | |
| N24 pause, mark, comment | – | – | new |
| N25 nothing leaves unseen; a clean environment | O28 barriers crossed by an editing step | – | new: a clean environment instead of review |
| N26 anchored comments on any artifact | – | S (weak) | new |
| N27 per-project visibility; an inside agent | MA2 MCP server on staging; O28; O29 filters; R16 | S/Q: information crossing a boundary is biased and distorted | *(judgement)* the inside agent's sanitised report needs a check of its own |

## In the record or the research, not yet a need of the walk

From the 2026-11-16 record and the research notes; candidates, not decided:
- Independent review: the reviewer gets the diff and the requirement, not the maker's reasoning; a different model for risky code (O5, W4).
- A work-in-progress limit on what is produced, sized to review capacity (O6; see N17).
- Rules kept where summaries cannot drop them, and enforced by hooks where possible (O1).
- A fresh session per area or task, its state in a notes file (O2).
- One writer per area, many readers and reviewers (O3).
- One accountable person per agent, visible; bots never speak as a person (O20, O30).
- Agents push back when overloaded; signals of the organisation's load monitored (O39).
- Lessons turned into checks (section 7; the ledger).
- A reuse check before building (O32 to O37).
- A dependency record across projects, pins with checks (O16, O17).
- Measuring the person's minutes per merged change (O10, O14).
- Housekeeping of worktrees and open changes with an owner (O14).

## Where things go (routing decided 2026-11-16, `working-with-agents-routing.md`)

R2 research and comparison to the kaizen repository; R3 the embodiment of the agent organisation (roles, tree, events, housekeeping) to a repository of its own; R5 the authorised officer in its own repository, public under `uellue` once it has code; R8 a private portfolio repository of the principal's; R1 know-how across ER-C projects in the institute's wiki.

## Claims to check

The research notes flag their own weak points: Wang et al. (arXiv 2608.11242) and Salle et al. (2610.31778), central to N8, read mostly as abstracts; Osmani's "low single digit" (a blog post); vendor figures (Faros, Anthropic's 15×, Cognition); Kim et al.'s blog and abstract disagree; the human-organisations note was read through a summarising fetch tool, mostly from Wikipedia; Gas Town only second-hand. None was re-checked for this mapping.
