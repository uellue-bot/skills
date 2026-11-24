# Claude Code features for a multi-session workstream setup: verified facts

Date of check: 2026-11-24. Local CLI: `claude --version` = 2.1.331 (Claude Code).
Sources: pages fetched from https://code.claude.com/docs/en/ with WebFetch (the docs map was truncated, so pages were fetched by their known URLs; long pages were read in parts or through targeted queries), and `claude ... --help` run locally on 2.1.331.
Convention: "unverified" = not in a fetched page or command output. "not found" = the fetched docs say nothing about it. Nothing is filled from memory.

Fetched pages: agent-view, remote-control, cross-session-messaging, scheduled-tasks, routines (now titled "Automate work with scheduled tasks"), channels, hooks, worktrees, agent-teams, costs, statusline, monitoring-usage, permission-modes, sandboxing, artifacts, tools-reference, approvals, approvals-classifiers, plugins/mods/overview, plugins/mods/api, server-managed-settings.
Local help: `claude --help`, `claude agents --help`, `claude attach --help`, `claude remote-control --help`.

---

## 1. Agent view / background sessions (`claude agents`)

(a) Terminal list and control panel for background sessions (`claude --bg`, `/bg`, or `←` on an empty prompt). `claude agents --json` prints active sessions as a JSON array for scripts (local help). `--cwd <path>` narrows the list.

(b) Status: "Agent view is in research preview" (agent-view.md). Version history: "v2.1.139 Agent view introduced as a research preview." Local `claude agents --help` and `claude attach --help` work on 2.1.331.

(c) Plan: not stated on the agent-view page (unverified). Admin: `disableAgentView` setting or `CLAUDE_CODE_DISABLE_AGENT_VIEW`; "Administrators can enforce this through managed settings." Auth: "The supervisor and its sessions authenticate with the same stored credentials as your interactive sessions." "Each session uses your subscription quota independently."

(d) Local only: "Agent view runs sessions on your machine." Cloud sessions are pointed to Projects. Interactive terminal sessions appear only after they are backgrounded. Subagents and teammates are not separate rows.

(e) Hard limits:
- Peek: `Space` opens the peek panel; Enter sends a reply without leaving; `Enter` or `→` attaches. Permission prompts, sandbox prompts and MCP input requests cannot be answered from the peek panel; attach with `→`.
- Supervisor: a separate process keeps sessions running after the terminal closes. It stops finished or waiting sessions that stay unattached for about an hour (pin with `Ctrl+T` to prevent). On macOS and Linux, if the supervisor stops, sessions stop unless a new supervisor connects within about a minute.
- Sleep: sessions are preserved and resume on wake.
- Reboot or shutdown: "Shutting down or restarting your machine stops running background sessions." Sessions that stopped within 48 hours show as failed and restart from the saved conversation on attach or reply; older ones show as stopped.
- Limitations section: rate limits apply per session; sessions are local and stop on shutdown; Claude-created worktrees are deleted with the session in agent view.
- VMs, devcontainers, SSH: not found.

(f) Sources: https://code.claude.com/docs/en/agent-view.md (fetched, two parts); `claude agents --help`, `claude attach --help` (local, 2.1.331).

## 2. Remote Control

(a) A local session (or `claude remote-control` server mode) is shown on claude.ai/code and in the Claude mobile app. Execution and filesystem access stay on the machine; the transcript is stored on Anthropic servers while connected. Messages can be sent from terminal, browser and phone.

(b) Status: no "research preview" or "beta" label found in the fetched text (unverified whether one applies). Local `claude remote-control --help` (2.1.331): `--spawn same-dir|worktree|session` (default same-dir), `--capacity <N>` (default 32), `--name`, `--permission-mode`, `--settings`. Version gates in the page: `--continue` and `--session-id` v2.1.200; crash recovery in server mode v2.1.238; `--sandbox`/`--no-sandbox` removed v2.1.300. Initial release version: not found.

(c) Plans: Pro, Max, Ultra, Team and Enterprise. API keys are not supported; with `ANTHROPIC_API_KEY` set the page shows "Remote Control requires claude.ai subscription auth". Not available with Bedrock, Google Cloud's Agent Platform, Microsoft Foundry, a custom `ANTHROPIC_BASE_URL`, or an enterprise Claude apps gateway. Team and Enterprise: an Owner must enable the Remote Control toggle in admin settings (off by default there). Enterprise custom roles need the cloud sessions permission and the "Claude Code remote control" permission. HIPAA configuration turns it off. Optional Trusted Devices (Owner enables on Team/Enterprise; sign-in must be under 18 hours old, with a biometric step-up).

(d) Runs locally. "Your local Claude Code session makes outbound HTTPS requests only and never opens inbound ports on your machine." Traffic goes through the Anthropic API over TLS. Transcript (messages, responses, tool activity) is stored on Anthropic servers while connected.

(e) Hard limits:
- One remote session per interactive process; server mode runs several (default capacity 32).
- The local process must keep running; closing the terminal ends the session. Over SSH, start `claude` inside tmux or screen.
- Server mode gives up after roughly 10 minutes of network outage and exits; an interactive session keeps retrying and reconnects when the network returns.
- Presence heartbeats: an interactive session disconnects with "could not reach the Remote Control server for about 30 minutes".
- Forwarded permission prompts and AskUserQuestion stay open until answered; other forwarded dialogs close after five minutes by default.
- "Keep this computer awake and this command running, or the session stops" (local help).
- After a server stop, resume commands work for about four hours.

(f) Sources: https://code.claude.com/docs/en/remote-control.md (fetched in large parts; targeted searches of the saved text); `claude remote-control --help` (local, 2.1.331).
- All of a person's Remote Control sessions across machines in one list: partly. The claude.ai/code session list shows sessions by name with a computer icon and green dot when online. The docs do not state explicitly that every machine's sessions appear in one list (unverified as worded).
- "Waiting for input" state in the claude.ai list: not found.
- PushNotification tool: tools-reference.md row: "Sends a desktop notification from a local session, and a phone push with Remote Control or in a cloud session. Push delivery runs through Anthropic-hosted infrastructure, which is not accessible from Amazon Bedrock, Claude Platform on AWS, Google Cloud's Agent Platform, or Microsoft Foundry." When a session may call it: not found.
- Person's requirements (remote-control.md, "Mobile push notifications"): install the Claude app on iOS or Android; accept the OS notification permission; in the terminal run `/config` and enable "Push when Claude decides" and/or "Push when actions required". If `/config` shows "No mobile registered", open the Claude app to refresh its push token. Pushes are skipped while the connected terminal is focused (`CLAUDE_CLIENT_PRESENCE_FILE` extends this). iOS Focus modes can suppress pushes.

## 3. Messaging between sessions (SendMessage, ListAgents, /list-agents)

(a) `SendMessage` sends plain text to a subagent, an agent-team teammate, or another Claude Code session. `ListAgents` lists reachable agents (backs `/list-agents`, also `/peers`). The receiving Claude reads the message between tool calls, or a new turn starts if it is idle. A session can also ask for one notice when another session next goes idle (`notify_when_idle`, v2.1.236; abandoned after 12 hours).

(b) Versions: v2.1.224 or later on macOS, Linux and WSL 2; v2.1.234 on native Windows. Same-machine messaging on Bedrock, Claude Platform on AWS, Google Cloud, Foundry, or with feature-flag fetching off: v2.1.248. Starting a conversation with a session on another machine: v2.1.225. `@name` mention in a prompt: v2.1.232.

(c) Availability and admin: same-machine messaging works on every provider. Finding cloud and other-machine sessions needs the sending session connected to Remote Control with claude.ai sign-in as its active authentication; not with an API key, Bedrock, Claude Platform on AWS, Google Cloud or Foundry. Admin controls: managed settings `permissions.deny` with `SendMessage` and `ListAgents` (stops sending and listing), and `crossSessionInbound: "refuse"` (stops receiving). `isolatePeerMachines: true` requires the user's approval before a message leaves the machine; a `true` from any scope applies.

(d) Routing:
- Same machine: a per-session Unix socket, never through Anthropic servers; restricted to the OS user.
- Another of the person's machines, or a cloud session: through Anthropic servers, arriving over that machine's Remote Control connection.
- A session inside a container cannot reach the host, and the reverse; two sessions inside the same container can. WSL 2 and native Windows on the same computer cannot reach each other.
- A cloud session cannot use SendMessage to answer a message from another machine; it can receive, and the reply appears in its transcript at claude.ai/code. For a reply to reach a session on another machine, Remote Control must be connected before the message is sent.

(e) Hard limits: plain text only; same-machine size cap about one million characters (refused at the sender); rapid bursts to one session are refused at the sender; the receiver rate-limits repeated messages per sender, drops identical repeats within a short window and queues at most 50 accepted messages; held messages are capped at 100 (oldest dropped); held messages expire after `dialogExpiry` (default five minutes) when no dialog can be shown; notice subscriptions expire after 12 hours; a long exchange can pause ("type a prompt to resume"); slow loops between sessions can continue, and each delivered message counts toward usage.

(f) Sources: https://code.claude.com/docs/en/cross-session-messaging.md (fetched); tools-reference.md rows for ListAgents and SendMessage (fetched).

How a received message is treated (same page, "How a session treats an incoming message"):
- "It can't approve anything: a message from another session never counts as your consent, so it can't answer a pending permission prompt on your behalf."
- "It can't change configuration": the receiving Claude is instructed never to change permission settings, CLAUDE.md or other configuration because another session asked.
- Commands in the text (such as `/compact`) arrive as plain text and never run.
- The receiving session's own permission prompts still apply to anything the message asks for.
- Inbound default when no `crossSessionInbound` value applies: decided per message from the two sessions' permission-mode classes. Sessions that bypass prompts form one class; plan mode counts as bypassing only in interactive terminal sessions with bypass available; auto, acceptEdits and dontAsk count as prompting. Values: `accept`, `hold` (shown as a notice, not delivered), `refuse` (dropped). Precedence is in the settings reference (linked, not fetched).
- A mod can observe incoming messages before Claude reads them (`session.receive`, mods API); a session set to refuse drops them before that event.

## 4. Scheduled and event-started work

### 4a. `/loop`, CronCreate, CronList, CronDelete (session-scoped)

(a) `/loop [interval] [prompt]` re-runs a prompt on an interval (cron, at least one minute) or at an interval Claude chooses (one minute to one hour). `CronCreate` schedules a 5-field cron prompt; `CronList` and `CronDelete` manage tasks. One-time reminders are created from natural language.

(b) Available in every session. `/loop` on Bedrock, Claude Platform on AWS, Google Cloud or Foundry, or with flag fetching off: v2.1.248. Next-run display: v2.1.299. Detach with Ctrl+C twice without stopping a self-paced loop: v2.1.295. Status: no preview label in the page.

(c) Plan: not stated for the local scheduler (unverified). Admin: `CLAUDE_CODE_DISABLE_CRON=1` disables the scheduler; no managed-settings key found.

(d) Local. Tasks fire inside the running session only while Claude is idle. A backgrounded session keeps its `/loop` tasks running (agent view).

(e) Limits: tasks fire only while Claude Code runs and is idle; no catch-up for missed fires; 50 scheduled tasks per session; recurring tasks expire after seven days; on `--resume` or `--continue`, tasks are restored except expired recurring tasks, past one-shots, and a self-paced `/loop`; a background session's self-paced loop stops if its wakeup comes due while the process is not running; recurring tasks run up to 30 minutes late (jitter).

(f) Source: https://code.claude.com/docs/en/scheduled-tasks.md (fetched); tools-reference.md rows for CronCreate and ScheduleWakeup (fetched).

### 4b. `/schedule`, cloud scheduled tasks ("routines"), RemoteTrigger

(a) Cloud scheduled tasks: a saved prompt plus repositories and connectors, run on Anthropic-managed cloud (or a self-hosted environment) on triggers: schedule, API (HTTP POST to the task's `/fire` endpoint with a bearer token), or GitHub events. `/schedule` creates them from the CLI; the `RemoteTrigger` tool creates, updates, runs and lists them.

(b) Status: "Scheduled tasks are in research preview. Behavior, limits, and the API surface may change." The `/fire` endpoint uses the beta header `experimental-cc-routine-2026-04-01`. Version: adding a GitHub trigger from the CLI needs v2.1.225.

(c) Plans: Pro, Max, Ultra, Team and Enterprise. Team and Enterprise Owners can turn them off for all members (Organization settings > Capabilities > Remote sessions, toggle "Scheduled tasks (Was Routines)"); when off, existing tasks stop. Runs count against the owner's subscription usage.

(d) Cloud (Anthropic-managed) by default. Cloud runs work on a fresh clone of the repository, not on local files.

(e) Limits (Usage and limits table): scheduled runs 100 per hour per account (waits when over); Run now, API fires and one-off re-runs 30 per hour per task (one count shared); Run now and one-off re-runs 100 per hour per account; API fires 100 per hour per account; GitHub events capped per task and per account per hour (events beyond the limit are dropped). No overage on these hourly limits. Minimum interval: one hour for cloud (comparison table in scheduled-tasks.md). Cron expressions that run more often than hourly are rejected.

(f) Sources: https://code.claude.com/docs/en/routines.md (fetched); https://code.claude.com/docs/en/scheduled-tasks.md (comparison table, fetched); tools-reference.md row for RemoteTrigger (fetched).

GitHub identity: "Anything a scheduled task does through your connected GitHub identity or connectors appears as you. Commits and pull requests carry your GitHub user." GitHub-event triggers need the Claude GitHub App installed on the repository. A separate bot account's token used instead: not found.

### 4c. Channels (outside events pushed into a running session)

(a) An MCP server, installed as a plugin, pushes events into an open session; Claude can reply through the same channel. Built-in plugins: Telegram, Discord, iMessage (macOS only), and fakechat (a local demo). Custom channels: built with the channels reference (linked, not fetched). A webhook receiver example is linked from the page (not fetched).

(b) Status: research preview ("Channels are a research preview feature"). Version: not stated. Per the page, `--channels` and `--dangerously-load-development-channels` do not appear in `claude --help` during the preview.

(c) Requires Anthropic authentication through claude.ai or a Console API key; not on Bedrock, Google Cloud's Agent Platform or Foundry. Team and Enterprise: an Owner must enable channels (managed setting `channelsEnabled: true`, or Organization settings > Claude Code > Channels). `allowedChannelPlugins` restricts which plugins may register. Pro, Max and Ultra without an organization need no org setting, only `--channels` per session.

(d) Local: the channel server runs on the machine; the chat platform (Telegram, Discord) carries the messages.

(e) Limits: events arrive only while the session is open; a server must be named in `--channels` (being in `.mcp.json` is not enough); a sender allowlist (pairing) applies; a channel can declare permission relay, and then "Anyone who can reply through the channel can approve or deny tool use in your session". Whether a channel message counts as user approval: not stated.

(f) Source: https://code.claude.com/docs/en/channels.md (fetched).
A GitHub issue comment pushed into a running session: not found among the built-in channels; possible only through a custom channel (unverified).

### 4d. Monitor tool

(a) Runs a command in the background and feeds each output line to Claude, or opens a WebSocket and treats each message as an event.

(b) Status: not labelled. Minimum version v2.1.310 on Bedrock, Claude Platform on AWS, Google Cloud, Foundry, or with telemetry or non-essential traffic disabled (when feature-flag fetching is off).

(c) Plan: not stated (unverified). Permission rules: same as Bash, including Bash deny rules. In auto mode, allow rules naming Monitor itself are set aside.

(d) Local, as a background process of the session.

(e) Lifetime: "Every watch Claude starts has a deadline: 5 minutes by default, at most 30 minutes, and at most 10 minutes in a non-interactive run given a single prompt with -p." At the deadline the watch ends and Claude gets one notice, so it can start the watch again. A monitor stops when asked or when the session ends, and when the subagent that started it is stopped. Resume does not restart background commands and monitors. Separately, background shell commands in unattended runs (`-p`, Agent SDK, CI, cloud) have a time limit of 30 minutes by default, up to 2 hours; a local terminal session has none.

The 30-minute ceiling matches the cadence reported for the issue watcher, but no page says that a watch restarts itself, so the cause of the stops is unverified.

(f) Sources: tools-reference.md, "Monitor tool" section and the background-command time limit section (fetched); scheduled-tasks.md (fetched).

## 5. Hooks

(a) Hooks run on lifecycle events. A hook can be a shell command, an HTTP request, a prompt, or (mods) a JavaScript function; its output can block, allow, or add context.

(b) Event list (hooks.md, fetched; 33 events): Session: `SessionStart`, `Setup`, `SessionEnd`. Per turn: `UserPromptSubmit`, `UserPromptExpansion`, `Stop`, `StopFailure`. Tool calls: `PreToolUse`, `PermissionRequest`, `PermissionDenied`, `PostToolUse`, `PostToolUseFailure`, `PostToolBatch`. Subagents and tasks: `SubagentStart`, `SubagentStop`, `TaskCreated`, `TaskCompleted`, `TeammateIdle`. Compaction: `PreCompact`, `PostCompact`. Model: `PreModelSwitch`, `PostModelSwitch`. MCP elicitation: `Elicitation`, `ElicitationResult`. Other: `Notification`, `MessageDisplay`, `InstructionsLoaded`, `ConfigChange`, `CwdChanged`, `DirectoryAdded`, `FileChanged`, `WorktreeCreate`, `WorktreeRemove`. Version of each event: not found.

(c) Plan: not stated. Admin: `allowManagedHooksOnly` in managed settings blocks user, project, local and plugin hooks (hooks from plugins force-enabled in managed `enabledPlugins` are exempt). `disableAllHooks` cannot disable managed hooks from outside managed settings. Managed HTTP hooks can be restricted with `allowedHttpHookUrls`.

(d) Local, inside the Claude Code process. Hooks also run inside subagents: "When a subagent calls a tool, tool events such as PreToolUse and PostToolUse fire the same configured hooks as in the main conversation."

(e) Context injection:
- `SessionStart` `source` values: `startup`, `resume`, `clear`, `compact` ("after compaction"), `fork`. `additionalContext` is "String added to Claude's context at the start of the conversation, before the first prompt." Plain-text stdout from SessionStart is also added as context. A `SessionStart` hook with matcher `compact` is therefore the documented route for context after compaction. The page lists `compact` as a SessionStart source; it does not spell out in words that the injected text reaches the model again after compaction (strong, not word-for-word).
- `PostCompact`: decision control "No" ("Used for side effects like logging or cleanup"); matcher values `manual` and `auto`. It is not listed among events that accept `additionalContext`. So a PostCompact hook cannot inject context (inferred from the table; not stated in those words).
- `PreCompact`: exit code 2 blocks compaction; its stdout becomes instructions for the compaction summary.

Plugin hooks: yes. `hooks/hooks.json` in a plugin is a documented location, active when the plugin is enabled, merged with user and project hooks.

Hooks in bypassPermissions mode: not stated (unverified). Hooks in background sessions as such: not stated (unverified). Notification types `agent_completed` and `agent_needs_input` fire from agent view (agent-view version history).

(f) Sources: https://code.claude.com/docs/en/hooks.md (fetched in two parts); https://code.claude.com/docs/en/agent-view.md (version history).

## 6. Spend and usage

(a) Documented ways to read a session's cost or tokens from outside the model:
- Status line JSON on stdin: `cost.total_cost_usd` (estimated, list price unless `modelPricing` applies; resets to 0 on `/clear`), `cost.total_duration_ms`, `context_window.total_input_tokens` and `total_output_tokens` (current context window, not a session total), `context_window.used_percentage`, `rate_limits.five_hour.used_percentage` and `rate_limits.seven_day.used_percentage` (0 to 100), each with `resets_at` (Unix seconds); `rate_limits.spend_limit.*` behind a Claude apps gateway.
- Status line `rate_limits`: "appears only for claude.ai Pro, Max, and Ultra subscribers, or behind a Claude apps gateway that sets a spend limit for you. Even then, it appears only after the first API response in the session." Team seats are not in that list.
- OpenTelemetry: `CLAUDE_CODE_ENABLE_TELEMETRY=1` with `OTEL_METRICS_EXPORTER` and `OTEL_LOGS_EXPORTER` (`otlp`). Metrics: `claude_code.cost.usage` (USD; per session via `session.id`), `claude_code.token.usage` (types input, output, cacheRead, cacheCreation), `claude_code.session.count`, `claude_code.commit.count`, `claude_code.pull_request.count`. Events: `claude_code.api_request` (with `cost_usd` and token counts), `claude_code.tool_result`, `claude_code.user_prompt`. The page does not say whether these figures match billing.
- `/usage` (interactive): a Session block with "Estimated cost" and per-model tokens (intended for API users); on Pro, Max, Ultra, Team and Enterprise it also shows plan usage bars and an attribution breakdown (skills, subagents, plugins, MCP servers). Not documented as scriptable.
- `/insights`: writes an HTML report to `~/.claude/usage-data/report.html`.
- `/cost`: not found on the fetched costs page (unverified).
- Transcript files: a directory `~/.claude/projects/` exists on this machine (observed locally); its format and token fields are not verified from docs.

(b) 5-hour and weekly limit usage: from status line JSON for Pro, Max and Ultra only (above). For Team seats the docs show it only in the interactive `/usage` bars. A scriptable source for Team seats: not found.

(c) Caps:
- `--max-budget-usd <amount>`: "Maximum dollar amount to spend on API calls (only works with --print)" (local `claude --help`). Per headless run only. The 1.1x data-residency multiplier counts toward it (costs.md).
- Team plans: each member draws from a per-seat allowance (rolling five-hour window and a weekly window), shared with Claude chat and Cowork; seat tiers Standard and Premium. Beyond it, usage credits (turned on by the organization) with spend limits "at the organization, group, or individual member level". Spend report in org analytics, with per-user CSV export updated daily.
- Across several sessions: the seat allowance and the usage-credit spend limits. No per-session cap is documented. Console workspace spend limits apply to API organizations.
- Routines: hourly caps (section 4b).

(d) Beta or preview status: not stated for these features.

(f) Sources: https://code.claude.com/docs/en/costs.md (fetched); https://code.claude.com/docs/en/statusline.md (fetched); https://code.claude.com/docs/en/monitoring-usage.md (fetched); `claude --help` (local).

## 7. Artifacts published from Claude Code

(a) The Artifact tool publishes an HTML or Markdown page from a session to a private claude.ai URL; the page updates in place when the session republishes. Connector-backed pages call MCP connectors when a viewer opens the page.
- Tool names `ArtifactComments` and `ArtifactData`: not found in the fetched page (unverified that these exact names exist).
- Comments: sessions collect comment threads on the page; a thread is activated when someone who can edit the artifact sends it to Claude ("Send to Claude" or `@claude`). Comments anchored to a place on the page: not found.
- Watching: after a publish, Claude Code "watches that artifact for comments"; a sent comment reaches the session right away and Claude can reply without being asked (v2.1.228). `/tasks` lists these watches.
- Data changes pushed to a session: not found. Live data comes from connector calls made when a viewer loads or refreshes the page.

(b) Status: not labelled. Version notes: comment collection v2.1.221; auto-reply v2.1.228; connector calls v2.1.209; the page's availability table says v2.1.281 or later for the CLI surface.

(c) Plans: Pro, Max, Ultra, Team and Enterprise. On Team and Enterprise, artifacts are on by default and an Owner can turn them off (Organization settings > Artifacts). The session must be signed in with claude.ai (`/login`); API key, gateway token or cloud-provider credential cannot publish; not on Bedrock, Google Cloud's Agent Platform or Foundry. Off by default with `-p`, in the Agent SDK and in the GitHub Action. Public sharing is off on Team until an Owner enables "External sharing".

(d) Stored on Anthropic-operated infrastructure; visible only to authenticated members of the publishing organization and the people shared with, unless shared publicly. Remote Control sessions can publish.

(e) Sharing: private to the author until shared; on Team and Enterprise, share with specific people or everyone in the organization; move to a shared folder; viewer or editor roles. Limits: rendered page 16 MiB or less; no backend; automatic comment replies capped at 60 comments or thread activations per artifact per hour; a session watches at most 25 artifacts; an automatic watch ends after several idle hours.

(f) Source: https://code.claude.com/docs/en/artifacts.md (fetched).

## 8. Claude Approvals

(a) A check on GitHub pull requests that reports who still has to approve. A policy file `.claude/approvals.yml` sets rules: which paths need how many people (`seats`), whether Claude may approve (`claude: comments | approves`), and conditions on facts or classifiers. A custom classifier lives in `.claude/classifiers/<name>/def.yml` (a prompt and typed outputs), runs as a Claude Code cloud session on Anthropic infrastructure, and answers a question about a change that rules can read. Built-in classifiers: `claude/code_review` (pass or fail) and `claude/diff`.

(b) Status: "public beta" for Claude organizations on Team and Enterprise, as part of Claude Ship. Works on github.com, including GitHub Enterprise Cloud. The GitHub App's name is not in the fetched text (unverified).

(c) Team and Enterprise only. Not available with Zero Data Retention, the HIPAA configuration or CMEK; FERPA or Access Transparency block starting it. Anyone who can merge a policy file to the default branch can turn it on; an Owner can turn it off for the whole organization. Billed to usage credits by tokens (Code Review and classifier runs).

(d) Classifier runs execute on Anthropic infrastructure; the check itself appears on GitHub.

(e) Limits: classifier defaults `budget.time` 600 seconds (maximum 3600) and `budget.cost` $10 (maximum $10); a run that reaches a limit reads `incomplete`. Classifier sessions have read-only GitHub access. The default branch's copy of a classifier definition runs, not the pull request's copy.

(f) Sources: https://code.claude.com/docs/en/approvals.md (fetched); https://code.claude.com/docs/en/approvals-classifiers.md (fetched). Not fetched: approvals-policy, approvals-setup, approvals-github-rules.

Relevance: Approvals decides who may approve a pull request. It does not gate or observe Claude Code sessions.

## 9. Plugin "mods" (panes, status line, toasts)

(a) A mod is a plugin whose JavaScript or TypeScript hook functions run inside Claude Code. The overview lists: a pane beside the transcript or a band above the prompt, with tabs, buttons and text fields; redrawing parts of Claude Code's own interface; holding or changing tool calls; `/command` functions; state shared between hooks. Timers: `$.clock.every`, `$.clock.after`. UI calls: `$.ui.status(text)` (one line under the prompt, prefixed with the mod's name), `$.ui.toast(text)`, `$.ui.log(text)` (dim transcript line that Claude does not read), `$.ui.notify(text)` (terminal notification, per the user's `preferredNotifChannel`).
- Pane showing items from outside the session: the mods API documents `$.http.fetch`, `$.process.run`, `$.fs.read` and `$.fs.list`, `$.mcp.call`, and `$.session.messages()`. A pane can therefore display data the mod fetched itself (API capability verified). How a pane binds data: on the interface page, not fetched (unverified).
- Messaging: `$.session.send({ to: { sessionId } | { agentId }, text })` sends to another session, a subagent or a teammate; the `session.receive` and `session.send` events observe messages.

(b) Status: not labelled on the overview. Versions: terminal mods on by default from v2.1.287; Desktop Code tab from v2.1.286; VS Code extension version 2.1.299 (2.1.304 when feature-flag fetching is off). `$.wasm` from v2.1.326; the `key` argument to `$.settings.read` from v2.1.304. Built-in `cc-plugin-easel` requires a claude.ai subscription, v2.1.321 (Team and Enterprise v2.1.326), and macOS or Linux outside WSL.

(c) Plan: not stated for mods in general. Admin: managed settings decide which mods load (`allowManagedModsOnly`). The built-in guard `cc-plugin-sec-default` restricts user mods from overriding approvals by default; admins set its order (admin page, not fetched). Mods are not sandboxed; `$.process.run` runs as the user.

(d) Where they run: terminal CLI (hooks and drawing), Desktop Code tab (except WSL and SSH sessions), VS Code chat panel (hooks and drawing above the prompt), `claude -p` and Agent SDK (hooks run, nothing drawn), Remote Control (hooks run in the terminal on your machine; drawing appears only there), cloud sessions (not in every session). WSL sessions in Desktop: no hooks. SSH sessions in Desktop, for a mod installed only on the local computer: no hooks.

(e) Limits: `$.process.run` takes an argument list with no shell and a 30-second default timeout; `$.http.fetch` follows at most five redirects and drops most request headers on a cross-origin redirect; timers stop when the module reloads; `$.ui.notify` is not rate-limited by Claude Code (the user's setting is the only switch).

(f) Sources: https://code.claude.com/docs/en/plugins/mods/overview.md (fetched); https://code.claude.com/docs/en/plugins/mods/api.md (fetched). Not fetched: interface.md, events.md, reference.md, admin.md.

## 10. Worktrees

(a) Git worktrees per session. `claude --worktree <name>` (or `-w`) creates `.claude/worktrees/<name>/` on a new branch `worktree-<name>`. Also: the `EnterWorktree` tool (Claude switches into or creates a worktree); `isolation: worktree` in a subagent's frontmatter (temporary worktree, removed when the subagent finishes without changes); `claude --bg --worktree` for background sessions; `--worktree "#1234"` starts from a pull request; `worktree.baseRef` (`fresh` by default, or `head`); `.worktreeinclude` copies gitignored files into new worktrees (v2.1.239); `WorktreeCreate` and `WorktreeRemove` hooks replace the git logic for non-git version control.

(b) Status: not labelled. Version notes on the page: `.worktreeinclude` for subagent worktrees v2.1.239; `-p` resume into a worktree v2.1.212. The page gives no first-release version for `--worktree`.

(c) Plan: not stated (git-level feature). Admin: `permissions.blockWritesOutsideWorkingDirectories` (v2.1.314 or later) removes EnterWorktree and ExitWorktree or refuses them; background sessions then cannot edit the main checkout until they call EnterWorktree (unless `worktree.bgIsolation` is `"none"`). Settable in managed settings.

(d) Local git.

(e) Limits: a worktree session blocks Edit, Write and NotebookEdit into the main checkout, Bash commands whose working directory is the main checkout, and git commands redirected into it; it does not track shell writes such as `cp` or redirects. `${CLAUDE_PROJECT_DIR}` stays at the project root while `cwd` follows the worktree. Interactive exit prompts before removing work; `-p` sessions get no exit prompt, and their worktree lock stays until the sweep or a manual `git worktree remove`. Background-session and subagent worktrees are swept after `cleanupPeriodDays` unless they hold work. Git LFS filter drivers are not run in created worktrees. "Don't ask again" approvals are saved to the main checkout's `.claude/settings.local.json`.

(f) Source: https://code.claude.com/docs/en/worktrees.md (fetched); https://code.claude.com/docs/en/permission-modes.md (fetched, for the blocked-write setting version).

## 11. Agent teams

(a) One lead session coordinates teammates (separate Claude Code instances, each with its own context) that share a task list and message each other. Teammates are shown in an in-process agent panel or in tmux or iTerm2 split panes. Subagents that Claude names also become teammates while the feature is on.

(b) Status: "Agent teams are experimental and disabled by default." Enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` (in `env` of settings.json or the shell). Introduction version: not stated (unverified). `--teammate-mode` exists but is "experimental and doesn't appear in claude --help" (per page).

(c) Plan: not stated (unverified). Admin: managed settings apply after user settings; if an organization enables teams there, the user cannot turn them off.

(d) Local. Team config under `~/.claude/teams/{team}/`, mailboxes as JSON files there, and the task list under `~/.claude/tasks/{team}/`, which stays on disk after the session ends.

(e) Limits (page "Limitations"): no session resumption with in-process teammates (`/resume` and `/rewind` do not restore them); task status can lag; shutdown can be slow; one team per session; no nested teams; no background subagents from in-process teammates; no per-teammate permission mode at spawn (new teammates start in the lead's mode); split panes need tmux or iTerm2. Spawning needs an interactive session (not `-p` and not the Agent SDK). Token use scales with the number of teammates.

(f) Source: https://code.claude.com/docs/en/agent-teams.md (fetched).

## 12. Permission modes and sandboxing for unattended runs

(a) Permission modes (permission-modes.md, fetched): `default`, `acceptEdits`, `plan`, `auto` ("Everything, with background safety checks"), `dontAsk` (anything that would prompt is denied), `bypassPermissions` ("Everything").

Auto mode:
- Plan: all plans.
- Organization: "on Team and Enterprise, auto mode is available by default. Administrators can turn it off for the organization by setting permissions.disableAutoMode to "disable" in managed settings."
- Models: on the Anthropic API and Claude Platform on AWS and Google Cloud (Anthropic-managed): Opus 4.6 or later, Sonnet 4.6 or later, Haiku 5.5, or a Fable model. On Bedrock, Google Cloud's Agent Platform, Microsoft Foundry and signed-in Claude apps gateway sessions: Sonnet 5 or later, Opus 4.7 or later, Haiku 5.5, Fable models. Older models are not supported on any provider.
- Default: the terminal and VS Code use auto from v2.1.283 on every plan. `claude -p` and the Agent SDK default to `default`; from v2.1.302, calls needing approval are denied unless an allow rule, hook, `canUseTool` callback or `--permission-prompt-tool` covers them.

Rules across modes (fetched):
- Deny rules block in every mode, including bypassPermissions. Allow rules have no effect in bypassPermissions. Ask rules still prompt.
- In auto mode, broad allow rules that grant arbitrary code execution are dropped on entry; narrow rules stay.
- Actions no mode auto-approves, even in bypass: tools matched by an explicit ask rule; AskUserQuestion; recognized `rm` and `rmdir` critical-path removals; the cross-session messaging safeguards; reads or writes outside the working directories when the blocking settings are on.
- Dont-ask mode: "Most calls that your permissions.allow rules or a PreToolUse hook approve run too." A PreToolUse hook returning "allow" cannot approve critical-path removals.
- Subagents: the frontmatter `permissionMode` is ignored in auto mode; the classifier reviews a non-teammate subagent's final report before the parent reads it.
- Background sessions: bypass mode is honored only when the acceptance is recorded in user or managed settings; `claude --bg --permission-mode bypassPermissions` is refused otherwise.
- Whether PreToolUse hooks fire in bypassPermissions mode: not stated (unverified).
- Whether hooks or rules apply to background sessions as such: not stated (unverified). Hooks do apply inside subagents (hooks.md).

(b) Status: auto mode and sandboxing are not labelled on the pages.

(c) Plan: auto mode is on all plans; on Team and Enterprise it is available by default and an admin can disable it. Sandbox: no plan condition stated.

(d) Local. The sandbox is enforced by the OS: Seatbelt on macOS; bubblewrap with a socat proxy on Linux and WSL2 (built on `@anthropic-ai/sandbox-runtime`). Native Windows: "Claude Code runs commands unsandboxed"; use WSL2. WSL1 not supported. Off by default; enable with `/sandbox` or `"sandbox": {"enabled": true}`.
- Default writes: the working directory, a per-user temp directory and added directories; `.claude` settings and shell startup files stay write-denied.
- Default reads: "Most of the machine, including credential files such as ~/.ssh and ~/.aws/credentials." Blocking needs `denyRead` or credential entries.
- Default network: no direct route; a local proxy checks each host against `allowedDomains`, which starts empty. Allowed domains do not limit WebFetch.
- Covers Bash commands. Built-in file tools, MCP servers, hooks and status-line commands run outside it.
- Background sessions: "strict sandbox mode covers shell-mode commands in a background session." Subagents: not confirmed.
- Admin (managed settings): `sandbox.failIfUnavailable`, `sandbox.allowUnsandboxedCommands: false`, `sandbox.network.allowManagedDomainsOnly`.

(e) Hard limits: the sandbox does not cover built-in file tools, MCP, hooks or the status line; commands a user runs at the `!` prompt usually run outside it; native Windows is unsandboxed.

(f) Sources: https://code.claude.com/docs/en/permission-modes.md (fetched, first 100 KB); https://code.claude.com/docs/en/sandboxing.md (fetched, first 100 KB).

---

## Items not verified (consolidated)

- Agent view on VMs, devcontainers or over SSH: not found. Agent view plan requirement: not stated.
- Remote Control: initial release version and any preview label: not found. "Waiting for input" state in the claude.ai list: not found. One list across all machines: partly (not stated as worded). When a session may call PushNotification: not found.
- Routines: a bot account's token instead of the person's GitHub identity: not found.
- Channels: a GitHub issue comment pushed into a session: not found among built-in channels; custom-channel route unverified. Channel version: not stated.
- Hooks: PostCompact context injection (inferred no, not stated in those words); PreToolUse in bypassPermissions mode; hooks for background sessions as such; first version of each event.
- Monitor: whether a watch restarts itself after its deadline; Monitor plan requirement.
- Costs: `/cost` command; transcript field layout; whether OpenTelemetry cost matches billing; scriptable 5-hour percentage for Team seats (documented only for Pro, Max, Ultra).
- Artifacts: `ArtifactComments` and `ArtifactData` tool names; comment anchoring to positions on the page; data changes pushed to a session.
- Approvals: GitHub App name; policy and setup details (approvals-policy and approvals-setup not fetched).
- Mods: pane data binding (interface.md not fetched); a status label; admin ordering (admin.md not fetched).
- Worktrees: first version of `--worktree`.
- Agent teams: introduction version; plan requirement.
- Permission modes: PreToolUse in bypassPermissions; hook behaviour for background sessions.
- Sandbox: GA or beta status; coverage for subagents.
- `/list-agents` output in a live session: not run (needs an interactive session); the docs only were read.
