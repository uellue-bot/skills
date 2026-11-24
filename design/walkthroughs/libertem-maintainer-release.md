# Walk-through: the LiberTEM maintainer releases a version

Persona: the LiberTEM maintainer (`design/stakeholders.md`), here the principal. Confidence: interviewed (the principal answers in the chat, 2026-11-24); co-maintainers and conda-forge's bots appear as others the persona works with, simulated. Part of `design/requirements-workstreams.md` (ledger 326), the first of two walks of a large existing project (the second: routine maintenance between releases).
What exists (read 2026-11-24 through the GitHub API): `docs/source/releasing.rst` (tagging with `scripts/release bump`, the release checklist as an issue template); `scripts/` (`release`, `releasetests.ps1`, `zenodo_upload`, `deploy_docs`, `codecov.sh`, `creatorlist.ipynb`); GitHub Actions workflows `ci.yml`, `cb.yml`, `thorough.yml`, and Azure DevOps for the full pipeline with slow tests; releases v0.15.0 to v0.16.0 (2026-05-19) and a rolling `continuous` pre-release; the release issue for v0.16 (#1823) and its post-release pull request (#1831); the feedstock `conda-forge/libertem-feedstock`, whose pull requests come from `regro-cf-autotick-bot` and `conda-forge-admin`.

## The release as it was: v0.16 (#1823, opened 2026-05-18, closed 2026-06-01)

From the issue's ticked boxes; the reasons are not recorded there.
- Step 0 (before the release candidate): all twelve items ticked.
- Step 1 (release candidate): all three ticked.
- Step 2 (using the release candidate): ticked: install in a clean environment, the Docker image, version info and its links, the missing-files tests by script. Not ticked: tests of the five related packages (LiberTEM-live, -holo, -blobfinder, ptychography40, -iCoM); the complete suite with slow, sample-file and CUDA tests; most of the GUI QA (no internet, every file type, every analysis, recent files, wrong parameters, keyboard and mouse, shutdown while running, Windows against a remote Dask cluster, responsiveness); the bad-parameter tests by script.
- Steps 3 and 4 (final version, after release): all ticked, including the post-release pull request #1831 (next `.dev0`, Zenodo badge).

## Steps

Where the time goes today (principal, 2026-11-24): step 2's QA, the documentation review with the change log, and sorting open issues and pull requests are "time intensive"; testing the related packages "can be automated with deterministic scripts, here it becomes 'agent helping to develop / maintain our CI infrastructure'".

1. **Step 0, before the release candidate.** Does today: twelve items by hand. Could be:
   - an agent does as small pull requests for review: deprecations due, GUI dependencies, dependabot, the link check, version in the change notes, the change log merged as a draft, `packaging/` JSON, flaky tests listed;
   - an agent runs and reports: the full CI with slow tests and the `dist` tests; starting "Thorough" and Azure needs a credential, through the authorised officer or a prompt to a maintainer;
   - an agent prepares, maintainers decide: which issues and pull requests go in (obvious ones proposed for resolution, the rest marked for review), the licence review, the documentation review.
   - Who starts a release: "someone" says it, often offline; an agent may propose it as part of maintenance.
2. **Step 1, the release candidate.** Does today: `scripts/release bump … --tag`, push, confirm assets and the Zenodo draft. Could be: the maintainer tags and pushes; an agent checks what it can reach without new credentials (easy wins first).
3. **Step 2, using the release candidate.** Does today: spot checks, since doing it all "became too tedious". Could be: every box, every release, by agents: the full suite on the VM, CUDA in a devcontainer, Windows arranged; test files from the data management system (may go to Anthropic); the GUI QA as a hallway test by eye; the five related packages as a deterministic CI job that agents build and maintain. The maintainer spot-checks what the agents flag.
4. **Steps 3 and 4, release and after.** Does today: final tag, release notes, the confirm boxes, Zenodo, the badge, the feedstock's pull requests, the mail, the next `.dev0`, the publication database. Could be: an agent checks the confirm boxes it can reach and drafts the release notes, the announcement and the post-release pull request; the maintainer pushes the tag, publishes on GitHub and Zenodo, sends the mail, enters the publication database. What goes wrong in practice: "the release scripts have failed many times, but never mixed up assets. Real was just crashing, upstream services misbehaving or not being available, or perhaps missing an asset / step because it 'failed successfully' so to speak -- error swallowed. Pretty reliable in recent times, fortunately." A check that every expected asset and step is there would catch what the scripts swallow; kept as an option for later (below).

## Acceptance examples

E58 and E60 in `design/examples.md`.

## Options for later (principal, 2026-11-24: not acceptance examples for now)

- A check after the release pipeline that every expected asset and step exists (wheel, tar.gz, AppImage, ghcr tags, PyPI, the Zenodo draft), catching what the scripts swallowed: "nice, but less important for now. That is usually a quick check before pressing the button on e.g. GitHub or Zenodo."
- Starting "Thorough" and the Azure pipeline without an agent holding the credential (through the authorised officer or a prompt to a maintainer): "Let's leave this part out for now, keep it as a sort of TODO/option for later."

## Questions for the principal

Asked and answered in the chat; see below.

## Answers from the principal (2026-11-24)

- Step 0, the split (agent does it as small pull requests; agent runs and reports; agent prepares, the maintainer decides): fits. Issues and pull requests: "the agent can propose resolutions for the obvious ones (addressed in this or a previous release; clearly not addressed and recent/relevant), and the ones for review (open for a long time, unclear, detailed, not looked at recently)."
- Starting the "Thorough" workflow and the Azure pipeline: "I'm not sure if agents will get credentials to run the 'thorough' workflow just like that. That may be a good fit for the 'authorized officer' to approve and hand out keys, or to prompt for a maintainer to do it." And: "pressing the pipeline button is the least of problems when cutting releases. Let's keep that on the list, but focus on what saves most time and effort for maintainers."
- Scope and timing: "usually we say somewhere offline 'time for a release', someone opens the issue, and we discuss there and coordinate with chat. Also OK for an agent to say 'we may want to cut a release to get the finished features out, should I start prepping' as part of maintenance."

- Step 2 in v0.16: "a mixture, but mostly we elected to skip thorough checks and rather spot check. It became too tedious to do everything by hand. That's where agents can help us to literally 'tick all the boxes'!"
- Where step 2 runs: "We are running right now on a VM on a machine where we can run these by hand. Devcontainer works well for CUDA tests. Windows we can also arrange."
- Test files: "we have them on pretty much all machines at the ER-C, one way or the other: They are in the data management system. Not public because of sometimes muddy licensing, but uncritical to send to Anthropic servers -- no sensitive data whatsoever."
- GUI QA by hallway test: "yes, hallway tests will do phenomenal there." A colleague's GUI hallway test across several VMs for the modama ingest client "works extremely well, and very helpful."
