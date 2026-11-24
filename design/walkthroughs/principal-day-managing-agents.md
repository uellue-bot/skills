# Walk-through: a day of the principal managing agents, as it is now

Persona: the manager (`design/stakeholders.md`), here the principal, directing coding agents on several projects. Told by the principal in the chat, 2026-11-24, as "a day in my life managing agents"; their words are quoted, the rest is the agent's summary of what they said. Confidence: interviewed. Part of `design/requirements-workstreams.md` (ledger 326); the walk of how it could be follows once this one is complete.

Numbering: **A** an annoyance, **W** what works well and should be kept.

## Steps

1. **Before leaving work the day before.** Does: "tried to kick off some work somewhere to run independently during my off time."
2. **Morning check-in from home.** Does: checks "from home before going to work if there's something to unblock"; without appointments, works "for a bit like this". From the home laptop, not the work machine; attaches to running sessions with `screen -x` ("`screen -x` is my friend!").
   - **A1** Copy and paste: "Copy & paste from terminals with Claude and screen is a hot mess, esp across OS and terminals."
   - **A2** Credentials: "Managing credentials and SSH keys across the machines is annoying as well."
   - **A3** Overview: "I often have several Claude sessions in parallel, and I need to see where they are at, what the session is even about."
   - **A4** Check-ins grow long: "I'd like to be able to stop and let agents churn on their own after a quick check-in. Instead, I often start a long question and answer game."
3. **Ideas arrive.** Does: "In the morning I often have fresh ideas that I'd like to quickly drop off somewhere, to be routed where they fit."
   - **A5** No drop-off place: "Instead, I have to decide which of the sessions and which of the projects. Or I don't even have a proper place yet." Ideas also "come in at random points for random workstreams. To free me up, I have to drop them off and move on."
4. **Working through the day.** Does: feeds sessions alongside other work; "The workstreams I can manage also include e.g. replying to e-mails, meetings, etc, so that competes with Claude sessions."
   - **A6** Attention as the limit: "I can feed two sessions simultaneously, usually. More than that with active engagement requires 'task switching'. So I'm a bit like a single core CPU with 2x hyperthreading." "Task switching is hard and costly for me."

5. **Where the sessions run.** Does: sessions "currently run on two machines. Each one with several devcontainers and VMs." Runs them in `screen` "to make sure they don't die".
   - **A7** Machines and keys: "the sessions now live on different machines. That means I have to do SSH juggling to access them -- again, where is which key that I need to push to what repo, etc."
   - **A8** One view, too many terminals: "A `screen` for all the sessions on different machines would be great, but again that's a lot of terminals."
   - **A9** Nameless windows: "Right now I see 'bash 1, bash 2, bash3, ...' -- heck if I know which is which!"

6. **Input from others.** Does: routes input from colleagues by hand. "a colleague dumped a whole stream of feedback into a group chat, and I'd have to copy and paste this to somewhere where it makes sense. In that case it could have been issues or issue comments."
   - **A10** Routing by hand: wanted instead, "to just unload that somewhere, with a confirmation 'I guess that's for the crawler workstream, right?', 'yes', and then fed into the right direction in an appropriate format." (The same need as A5, for input from others.)
7. **Trying out what is built.** Does: "I like to try out prototypes. Also, chatting about requirements, users and their needs, and fit with the real world is very nice!" (W1, works well.) "You looking at photos of the board and of the heat pump UI worked phantastic! I'd like to do that whenever I try sth -- e.g. look at the crawler web GUI, talk about queries and results, etc." (W2, works well.) `record.sh` "helped a lot" (W3).
   - **A11** Telling the agent what happens: "It is also annoying to talk about what is happening when I try a prototype. … Still I was sometimes just copying stuff back and forth. Made worse by the copy&paste issues I described before. E.g. if a line breaks I have to reassemble it in an editor I use as a scratchpad."
   - **A12** Commenting in context: wanted, "to essentially try out natively what is being built, hit 'pause' and then scribble on the screen and add comments. But while preserving privacy, I can't send my desktop screenshots unfiltered to Anthropic!"
8. **Reviewing changes.** Does: reviews on GitHub and GitLab, "good alternatives to review changes and have nice tools to review changes. So that is my current preference." (W4, works well.)
   - **A13** Files across the sprawl: "I need VS code sessions, they need to go to the right machine and see the right folder, I can't easily see all the things in a native folder."
   - **A14** Review beyond code and Markdown: "I can't easily look at things and at the same time comment sensitive to a specific context other than code and MD -- HTML, graphics, jupyter notebooks, ..."
9. **What the agent may see.** "Maintaining a good separation between what you can see and what you can't was a major issue with crawler development." In the home automation project the agent runs production with an SSH key, "which was a blessing" (W5, works well there).
   - **A15** Private data: "That wouldn't work with the crawler prototype and Modama -- I can't let you see private user data, so there has to be a filter / anonymization stage, or somehow an issue / scenario is recreated in a test environment so that you can look at it."

10. **Approving.** Does: "with agents I approve in the chat mostly." With colleagues: "I mostly authorize per e-mail or chat -- e.g. Telegram 'yes let's deploy now' sth like this. For important things we have digital workflows at our institute."
    - **A16** Approving on the tracker: "It feels a bit weird to approve in GitHub / GitLab, but I could get used to it once authentication, roles and barriers are bulletproof -- at least for minor things like 'run the benchmark with these settings' and not sth critical that could compromise an account or machine or cause damage. If we have a proper security analysis I would probably feel better."
11. **End of the day.** Does: "I like to send agents on a bit of a mission towards the end of the day so I can pick up results in the morning." "I do like that agents can run in the night" (W6, works well).
    - **A17** Unused tokens: running at night "also introduces a form of FOMO when tokens are not used."

## Not told yet

- How the principal knows at the end of the day what is still open (step 11).
