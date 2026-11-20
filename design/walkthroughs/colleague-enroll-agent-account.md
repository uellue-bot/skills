# Walk-through: a modama colleague enrolls an agent account in a fresh devvm VM

Persona: a colleague enrolling an agent account in a VM (`design/stakeholders.md`): a modama developer who knows devvm, sets up a fresh VM for `modama-rdm/modama` so that an agent works there through a bot account; three variants of the account (principal, 2026-11-20): **A** an existing bot with forks of its own (as `uellue-bot`); **B** a bot account the colleague creates first, possibly working on the colleague's personal forks; **C** an existing account working on a repository of an organisation it does not own. Ends when the agent in the VM has pushed a branch and opened a draft pull request (principal, 2026-11-20). Confidence: simulated, walked on paper by the agent; nothing was run.
What exists: `design/requirements.md`, topic ledger 324 with "Enrollment" (round 3); `design/runbooks/agent-account.md` (run 0, done by hand); devvm at `2649ab5` (README, `design/spec.md`, `design/requirements.md`, `guest/setup-dev.sh`, `cmd_setup_dev`); `modama-rdm/modama` on GitHub (public, default branch `master`, Issues on; read 2026-11-20). `devvm setup-bot` does not exist; the walk imagines the colleague meeting it as the requirements describe it.

## Steps

1. **Which account.** Does: reads devvm's README for `setup-bot` and decides between A, B and C. Needs to know: what each variant asks of them before they start (an account, a fork, someone with the bot's password), and that in A the bot's owner, not the colleague, makes the token. Goes wrong: in A the colleague cannot do the browser steps at all; B with the bot working on the colleague's personal fork, and C with the bot as an outside collaborator, hit the fine-grained token's documented gap: no write "to repositories where the user is an outside or repository collaborator" (quoted in the requirements, 2026-11-20). Would ask: "Which of these is mine, and who has to be at the browser?"
2. **The bot account (B only).** Does: signs up a second GitHub account with an address of its own. Needs to know: whether GitHub's terms allow it (machine accounts; not checked), which email (the bot's own, forwarding, as decided for `uellue-bot`, or a noreply address), 2FA. Goes wrong: a second account in the same browser profile; the confirmation mail to an address nobody reads. Would ask: "Is a bot account allowed, and which address do I give it?"
3. **The fork and its settings (A, B).** Does: as the bot, forks `modama-rdm/modama`; turns on Issues; sets the interaction limit; syncs. Needs to know: that a fork starts with Issues off (run 0, step 0); the limit lapses after 6 months. Goes wrong: forked as themselves, by habit, since the browser is logged in to their own account; the limit not renewed. Whether the script can do this depends on the token's Administration permission, left off in run 0 ([unknown] in the requirements). In C there is no fork: the bot works on branches of the organisation's repository (assumed; the rest of the walk rests on it for C). Would ask: "Fork as me or as the bot? And why is there no Issues tab?"
4. **The token.** Does: as the bot, creates a fine-grained token, following the script's coaching. Needs to know: resource owner (the bot in A and B, the organisation in C), repository access, each permission and why (run 0's list), expiration. Goes wrong: twenty-odd permission rows with no way to paste a list (whether GitHub's form takes them from the URL is not known); C needs the organisation to allow fine-grained tokens and maybe to approve each (not checked); an expiration policy can forbid "none" (quoted in the requirements); the colleague copies the token into a chat or a file to carry it to the VM. Would ask: "Which boxes exactly, and how do I get it into the VM without writing it down?"
5. **Notifications.** Does: as the bot, turns email off; as themselves, watches the fork on "Participating and @mentions". Needs to know: why (the bot's mail would flood their inbox, the bot reaches them by mentioning them). Goes wrong: the step is skipped, since nothing breaks without it; noticed a week later in the inbox. Would ask: "Do I need this now?"
6. **The VM.** Does: in the host's modama checkout, `devvm create`. Needs to know: that a bot VM shares no folder with the host (run 0's decision: a shared folder lets the VM change hooks that run on the host), so `DEVVM_SHARE_WORKSPACE=0`. Goes wrong: the default shares the checkout; the colleague creates as always and learns at `setup-bot` that the VM is the wrong kind, after the image download and provisioning; the VM is named after the host's branch, though the VM will work on the fork's branches. Would ask: "Is this the same `devvm create` as always?"
7. **`devvm setup-dev`.** Does: as always. Needs to know: nothing new. Goes wrong: it copies the colleague's git name and email into the VM, which `setup-bot` has to replace; the Claude Code token is the colleague's own account (as decided for run 0, the usage budget is the colleague's). gh is not installed by it; Debian's gh 2.46 fails on `gh pr view` (run 0). Would ask: "Before or after `setup-bot`?"
8. **`devvm setup-bot`.** Does: answers the forge (GitHub), the bot's login, upstream `modama-rdm/modama`, the fork; pastes the token when asked. Needs to know: what the command will do before it does it; that the token goes over stdin into gh's `hosts.yml` in the VM, in plain text, and into every later snapshot (as the Claude token does, devvm README). Goes wrong: devvm's own rule is "the VM never holds upstream credentials" (devvm `design/spec.md`), which `setup-bot` breaks on purpose with the bot's; the token pasted where the terminal echoes it; for a second VM on another machine, a new token or the same one (the runbook says it lives only in the browser and the VM, so the same one cannot be carried). Would ask: "Is it OK that the token sits in the VM, and do I make a new one for my second VM?"
9. **The checks.** Does: watches the script check the login, the identity, the remotes, gh's default repository, and the blast radius. Needs to know: what "passed" means. Goes wrong: in C the push to upstream succeeds, since the bot has write there, so run 0's check (expect 403) fails for the right setup; in A and B the check proves the bot cannot touch upstream. Would ask: "It says the push to upstream worked: is that bad?"
10. **The round trip.** Does: the script pushes an empty commit on a branch and opens a draft pull request on the fork (in C: on the organisation's repository, visible to its watchers). Needs to know: who closes it, and that the branch is deleted afterwards. Goes wrong: in C the test pull request notifies the whole team. Would ask: "Can the test run without anyone else seeing it?"
11. **Done.** Does: `devvm snapshot bot-ready`, `devvm ssh`, `claude`. Needs to know: the next step is `/kaizen:update` in the clone; whom the agent takes instructions from (the maintainer list, open in the requirements). Would ask: "Who else can tell my agent what to do through the tracker?"

Where the others differ (paper only; no bot credential on the GitLab server):
- **GitLab fork**: `glab` instead of gh; personal access tokens have scopes (such as `api`, `write_repository`) rather than per-repository permissions, so a bot's token reaches everything the bot can; project and group access tokens come with a bot user of their own, which may replace step 2 (from memory, not checked). The instance (`gitlab.example`) is one more answer in step 8.
- **Plain git remote**: no tracker, no pull request; the credential is an SSH key with write on the remote (a deploy key or the bot user's key); step 10 is a push of a branch only, and the round trip ends there.

## Product hat

The colleague wants an agent that works on modama in a VM they can throw away, with their own credentials out of reach, in one sitting. "Done" for them is the agent's draft pull request on the fork and no surprise in their inbox. They would complain about: a browser round they did not know was coming (steps 3 and 5), a VM made the wrong kind (step 6), a check that fails for a correct setup (step 9, C), and having to come back to the principal for the token (A).

## Developer hat

- Secret handling: the token over stdin only, never as an argument or in the transcript; in plain text in the VM and its snapshots; revocation is the one remedy (run 0's records).
- Prompt injection: an agent that reads a public tracker reads text from anyone (run 0's risks); in C the bot's write reaches the organisation's repository, so the blast radius of a planted instruction is larger.
- Fragile order: `setup-dev` then `setup-bot`, or each must undo the other's git identity; the VM's kind fixed at `create`.
- Idempotence: `setup-bot` run twice (a typo in the fork's name) must not stack remotes or a second token.
- devvm's credential rule changes; its tests (`tests/unit`) need a case for a VM with a bot credential.

## Questions for the principal

- **Q1** (step 1) Which variant comes first, beside A on `uellue-bot/devvm`, and are B-personal-forks and C-as-outside-collaborator in scope although the fine-grained token cannot write there (a classic token could, with the reach the requirements describe)?
- **Q2** (steps 3, 4) Does the fork's setup stay a coached browser step, or does the token get Administration for the fork's settings?
- **Q3** (step 6) Does `setup-bot` refuse a VM that shares the checkout, or does a setting (`DEVVM_SHARE_WORKSPACE=0` in the project's `.devvm/config.env`, or a `create` option) make the bot VM from the start?
- **Q4** (step 8) One token per VM (made each time in the browser) or one per bot, kept on the host like the Claude token file, against the runbook's "only in the browser and the VM"?
- **Q5** (step 8) May devvm's rule "the VM never holds upstream credentials" become "never the user's; a bot's on purpose, with `setup-bot`"?
- **Q6** (step 9) In C, what does the blast-radius check expect?
- **Q7** (step 7) Does `setup-dev` install the current gh for everyone (the "devvm configuration with every dependency"), or only `setup-bot`?
- **Q8** (step 2) Is a bot account per colleague what GitHub's terms allow, and is it what ER-C wants?

## Answers from the principal

None yet.

## Gaps

- `devvm setup-bot` itself; its usage text and README section (steps 1, 8).
- A way to create the bot VM without a shared folder from the first command (step 6).
- gh from GitHub's repository in the VM (step 7).
- The coaching for the browser steps: the token's permissions as a list with reasons, the fork's settings, the notifications (steps 3 to 5).
- The maintainer list the agent takes instructions from (step 11; already open in the requirements).
- A check that differs per variant (step 9).

## Acceptance examples

E52 to E57 in `design/examples.md`.

## Questionnaire

From `references/requirements-questions.md`, not answered by the record for this process: who operates enrolled VMs after the first trial (1.4 users beyond the principal is still open); what happens when a token leaks and nobody notices (no monitoring named); how a colleague's bot is offboarded (token revoked, VM destroyed, fork kept or deleted).
