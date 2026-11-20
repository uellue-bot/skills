# Runbook: an agent with its own GitHub account, in a VM

A working document: how this repository was set up so that an agent works through its own GitHub account (the bot) on a fork, in a VM without the principal's GitHub credentials. Kept to evolve the setup and to repeat it in other repositories. Requirements and decisions: `design/requirements.md`, topic "an agent account working on a fork" (ledger 324). Terms: `GLOSSARY.md` (public tracker, working tracker).

Status: first run in progress, 2026-11-20. Each step is read together before it runs; what changed on the way is noted under the step.

## The setup in one paragraph

The principal's repository (`uellue/skills`) has the public tracker. The bot's fork (`uellue-bot/skills`) has the working tracker (issues from collaborators only) and is where pull requests are reviewed, with inline comments. The agent runs in a VM (`skills-dev`, made with devvm) with no folder shared with the host. It pushes to the fork with a fine-grained token of the bot. The principal opens the pull request on `uellue/skills` from the fork's branch. Instructions count only from maintainers of `uellue/skills`. Code from anyone else runs only after a maintainer approves. The bot never merges on the fork; the fork's `main` changes only by syncing with `uellue/skills` (a stray merge only makes the fork's `main` drift, which is low damage and fixable). Discussions stay off: issues only, with the label `idea` for notes not yet actionable.

## Variables

On the host, only `VM` is needed:

```bash
VM=skills-dev                      # the devvm VM
```

In the VM, these are for the setup only: set them in the shell before steps 2 to 7 (and again after a new login or `exec bash`).

```bash
BOT=uellue-bot                     # the bot's GitHub login
FORK=uellue-bot/skills             # the bot's fork, with the working tracker
UPSTREAM=uellue/skills             # the principal's repository, with the public tracker
BOT_EMAIL=d.weber.bot@fz-juelich.de   # the bot account's own email: public on purpose, so people can reach the person behind it (2026-11-20); a GitHub noreply address (<id>+<login>@users.noreply.github.com) is the alternative
CLONE="$HOME/work/skills"          # the fork's clone inside the VM
```

Every command below writes them as `${FORK:?}`: if one is unset, the command stops with an error instead of running with an empty value. After the setup, nothing depends on them: the identity is in `~/.gitconfig`, the remotes (`origin` the fork, `upstream` the principal's repository) and gh's default repository are in the clone. They are not kept in a file every shell reads: the bot works on several forks, and one global `FORK` would be wrong in all clones but one, which `:?` does not catch (run 0, item 45). When the setup is automated (ledger 325), they become the script's parameters.

## Step 0: in the browser, logged in as the bot

- Fork → Settings → General → Features: turn on Issues.
- Fork → Settings → Moderation options → Interaction limits: "Limit to repository collaborators", 6 months. It lapses after that; renew it.
- Fork → Sync fork, so that its `main` matches `uellue/skills`.
- Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token:
  - resource owner: the bot; expiration: none;
  - repository access: All repositories ("This applies to all current and future repositories you own. Also includes public repositories (read-only).");
  - repository permissions, as chosen in run 0 (2026-11-20): read and write for Actions, Contents (code), Code quality, Commit statuses, Deployments, Issues, Pull requests, Security events, Workflows; read for Dependabot alerts, Metadata, Repository advisories, Repository license policy, Secret scanning alerts. Administration, Secrets, Variables, Environments, Webhooks, Pages and Discussions stay off, so the bot cannot change its own guardrails or send data out through them;
  - account permissions: none.
  - Run 0: Security events was first given write and then reduced to read: write would let the bot dismiss code-scanning alerts. A token's permissions can be edited later without changing the token (confirmed by the principal, 2026-11-20).
- Notifications (run 0, item 46): as the bot, at Settings → Notifications, email off for Watching, Participating and Actions, and "Automatically watch repositories" off (the bot's address forwards to the principal). As the principal, the fork's Watch button on "Participating and @mentions": the bot reaches the principal by mentioning them.
- Keep the token in the browser tab for step 4; it goes into the VM only, never into a chat or a file in a repository.

## Step 1: on the host, start the VM and keep a way back

```bash
devvm -n "$VM" start
devvm -n "$VM" snapshot pre-bot      # the state before this runbook; `devvm reset pre-bot` returns to it
devvm -n "$VM" ssh                   # the following steps run inside the VM
```

## Step 2: in the VM, tools up to date

```bash
claude update
# gh from GitHub's own apt repository (docs/install_linux.md of cli/cli, "Debian", read 2026-11-20), not Debian's package:
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) && sudo mkdir -p -m 755 /etc/apt/keyrings && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg && cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg >/dev/null && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg && sudo mkdir -p -m 755 /etc/apt/sources.list.d && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list >/dev/null && sudo apt update && sudo apt install gh -y
gh --version
```

Run 0: Debian 13's `gh` 2.46.0 (2025-01) failed on `gh pr view` with "Projects (classic) is being deprecated … (repository.pullRequest.projectCards)", so `gh` comes from GitHub's repository (2.105.0 current on 2026-11-20); `sudo apt update && sudo apt install gh` upgrades it.

Run 0 (2026-11-20): Claude Code 2.1.319 → 2.1.329; `gh` was missing and came from Debian 13's package, 2.46.0 (2025-01-13). The agent could not log in to the VM (see Open), so the principal ran the VM steps in a recorded terminal (`~/bot-setup/run-0/`, private, on the host) and the agent read along.

## Step 3: in the VM, the bot's identity

```bash
git config --global user.name "${BOT:?}"
git config --global user.email "${BOT_EMAIL:?}"
git config --global --list | grep '^user\.'
```

The VM's `dev` snapshot carries the principal's name and email from `devvm setup-dev`; this replaces them.

## Step 4: in the VM, the token (the principal types it)

```bash
gh auth login --hostname github.com --git-protocol https   # choose "Paste an authentication token"
gh auth setup-git                                          # git uses gh's token for https pushes
gh api user --jq .login                                    # prints the bot's login
```

Run 0: answer "No" to "Authenticate Git with your GitHub credentials?"; `gh auth setup-git` does it afterwards. gh warns "Authentication credentials saved in plain text" (`~/.config/gh/hosts.yml` in the VM, which has no keyring); that file is the token's only copy outside the browser. `gh auth status` shows the token's identifier part and masks the secret part with asterisks, so the recording holds no usable token (checked).

## Step 5: in the VM, the fork's clone and the plugin from it

```bash
gh repo sync "${FORK:?}" --source "${UPSTREAM:?}"   # the fork's main follows upstream; the only way it changes
mkdir -p "$(dirname "${CLONE:?}")"
gh repo clone "${FORK:?}" "${CLONE:?}"  # adds upstream as the remote "upstream"
cd "${CLONE:?}"
git remote -v
gh repo set-default "${FORK:?}"         # run 0: `gh repo clone` made upstream gh's default, so issues and pull requests without --repo would aim at the public tracker
git config core.hooksPath .githooks     # the repository's commit hooks
claude plugin marketplace list          # if "uellue" points to GitHub, remove it first:
# claude plugin marketplace remove uellue
claude plugin marketplace add "${CLONE:?}"
claude plugin install kaizen@uellue
```

Companions, as on the principal's host (run 0):

```bash
claude plugin marketplace add mattpocock/skills && claude plugin install mattpocock-skills@mattpocock
claude plugin uninstall mattpocock-skills@claude-plugins-official   # run 0: the `dev` snapshot had a second copy from the official directory
npm install -g --prefix ~/.local @fission-ai/openspec@latest && openspec config set telemetry.enabled false
```

OpenSpec follows its latest release on purpose, not a pinned one: "we'd like to know first about any change or issues in a plugin we recommend" (principal, 2026-11-20). It needs Node.js 22.20 or newer (the VM has v26.13.0); `openspec init` is not run in the repository, which does not use OpenSpec yet.

The standing instructions for the agent (`~/.claude/CLAUDE.md`) come from the plugin: `/kaizen:update` in the first session in the VM (step 8), the path a user takes too. Run 0: the VM's copy was from 2026-11-06, without the numbering rule.

## Step 6: in the VM, check the blast radius

```bash
cd "${CLONE:?}"
git push --dry-run https://github.com/"${UPSTREAM:?}".git HEAD:refs/heads/blast-radius-check   # expected: refused (403)
gh api repos/"${UPSTREAM:?}" --jq .permissions                                                     # expected: push false, admin false
gh api repos/"${FORK:?}" --jq .permissions                                                         # expected: push true, admin true (owner; the token has no Administration)
```

Run 0: as expected ("Permission to uellue/skills.git denied to uellue-bot", 403).

## Step 7: in the VM, a test round trip

```bash
cd "${CLONE:?}"
git switch -c bot-setup-check
git commit --allow-empty -m "Check of the bot's setup: push and draft pull request on the fork"
git push -u origin bot-setup-check
gh pr create --repo "${FORK:?}" --base main --head bot-setup-check --draft \
  --title "Check of the bot's setup" --body "Test of the runbook design/runbooks/agent-account.md; to be closed."
```

The principal looks at the draft pull request on the fork, comments inline once, and closes it; the branch is deleted on the fork.

## Step 8: on the host, keep the result

```bash
devvm -n "$VM" snapshot bot-ready
```

From here, a session starts with `devvm -n "$VM" ssh`, then `cd ~/work/skills && claude`.

## Open

- Memory files of the agent are per checkout path (`~/.claude/projects/<slug>/memory/`); the VM's clone starts without them.
- How the principal's local checkout and the fork stay in step (the principal pulls the fork's branches for review, or reviews on GitHub only).
- What of this becomes a tool: enrolling a bot account in a VM should become automated, so that a VM to work on any of `uellue-bot`'s forks comes up fast (principal, 2026-11-20; ledger 325). This manual run and its recording are the record to automate from.
- A ruleset on the fork's `main` that blocks merges: try whether it blocks them, whether "Sync fork" and `gh repo sync` still work, and whether draft pull requests can still be opened.
- Discussions: revisit if the `idea` issues pile up; on `uellue/skills`, once outside users ask questions.
- The agent cannot use the principal's SSH key for the VM (it has a passphrase, and handing the agent a loaded key would reach whatever else the key opens), so the principal runs the VM steps in a recorded terminal (`plugins/kaizen/scripts/record.sh`) and the agent follows it live. A key of its own for the VM would let the agent run them.
