# Decisions

Every structural choice in Palo is recorded as an architecture decision record in the private
repository, with its context, the decision, why, and what it costs. These are the 58 so far, in
order, one paragraph each. When a later decision changes an earlier one, it says so, and addenda that
change an older record are noted under it.

**0001 · Telegram is the only interface (14 Oct 2026).** iMessage was ruled out: it has no bot API
and would need a Mac awake and signed in. A Telegram bot read by long polling needs no open port and
obeys an allowlist of user ids.

**0002 · Models through their subscription CLIs, never a paid API (14 Oct 2026).** The owner already
pays for Claude and Google AI subscriptions, which include the official CLIs but not API access.
Claude Code handles projects, Gemini everything general, and a local model routes. Claude's quota
is the scarce resource, so nothing trivial ever reaches it.

**0003 · WSL2 on the home PC, with no open ports (14 Oct 2026).** The desktop is always on and has a
GPU; WSL2 gives Linux tooling with Windows reachable through interop. Maintenance goes over
Tailscale.

*Addendum (22 Nov 2026):* the PC now runs native Ubuntu 26.04 with GNOME on Wayland. WSL was the
biggest consumer of RAM, Windows closed it when it thought nobody was using it, and keeping it
alive needed a helper process, a Windows-side guardian and an emergency SSH into Windows. Native
Linux gives Palo the whole machine, a complete journal, per-process GPU memory from the real
driver, and the desktop through D-Bus and the official Wayland portals instead of PowerShell.

**0004 · Local voice within the GPU's budget (14 Oct 2026).** Voice notes are transcribed with
faster-whisper and the router is a 4B model, sized so both fit next to each other. Audio answers only
to audio, never with code or long lists.

**0005 · Every capability is an MCP tool (14 Oct 2026).** One server, `palo-mcp`, registered in both
CLIs, so a capability is written once and every engine can use it. Integrations stay simple: app
passwords, `gh`, no OAuth apps. An addendum gives the models full control of the PC, fully logged.

**0006 · Palo's memory is the owner's working context, shared with the Mac (14 Oct 2026).** The
agent instructions, memories, skills and per-project files live in a private repository synced by
git on both machines, so the assistant never has to be taught twice.

**0007 · The memory is an Obsidian vault (21 Oct 2026).** That same repository, opened in Obsidian:
memories, a note per project with its progress prompt, daily notes and a Dataview board. Palo writes
plain Markdown following the vault's templates.

**0008 · Real-time voice with OpenAI's Realtime API (26 Oct 2026).** Turn-taking and barge-in at
phone-call latency were out of reach locally, so the owner chose to pay for this one feature, behind
an opt-in key, from a web page of Palo's own.

**0009 · Local semantic memory and graph, as an index of the vault (26 Oct 2026).** Hybrid search
(FTS5 and `sqlite-vec`, fused by reciprocal rank) plus a deterministic graph, in one rebuildable
SQLite file. No vector database service to run.

**0010 · palo-node, a daemon on the Mac (26 Oct 2026).** A Go LaunchAgent that holds the session's
privacy permissions, listens only on Tailscale and checks a token. SSH from the PC would not be
allowed to take screenshots or drive apps.

**0011 · A web panel for projects, and Claude asking questions (28 Oct 2026).** Cards assembled from
the existing sources of truth, actions through the same methods as Telegram, and an `ask_juan` tool
that ends Claude's turn and resumes the session with the answer.

**0012 · Access security (29 Oct 2026).** Alerts for unexpected access, a weekly review of what the
PC exposes, and a web key that is rotated without ever passing through a model.

**0013 · Closed-loop self-verification (30 Oct 2026).** After screenshots with error messages and
wrong sizes reached the owner, every image is measured and shown to the local vision model before it
is sent, and every README is checked before a push.

**0014 · Cross-contextual proactivity (30 Oct 2026).** Calendar, pending coursework and red CI are
crossed every 30 minutes. Rules handle facts; Gemini judges the rest and must cite facts from two
sources. The local model is deliberately not a fallback: a bad alert costs more than a missed one.

**0015 · An elastic GPU (30 Oct 2026).** Palo steps aside when a game or a render wants the GPU and
carries on from the CPU. Unloading after every call was measured and rejected: the router runs on
every message and is twenty times slower cold.

**0016 · The living graph (30 Oct 2026).** Open ideas and notes lined up against existing code, with
Gemini proposing pairs. Embeddings were measured first and gave noise.

**0017 · The PC monitor (30 Oct 2026).** A desktop window showing quota, where each workload runs and
memory by owner. It measures only while someone is looking, and draws at 15 frames per second so it
never takes GPU from a game.

**0018 · A free call (1 Nov 2026).** Whisper, one warm Gemini process for the whole call and neural
voices, sentence by sentence. About five seconds to the first word instead of instant, at no cost.

**0019 · Third-party MCP servers (1 Nov 2026).** A headless browser with an in-memory profile and
up-to-date library documentation, at pinned versions.

**0020 · Photos, kept promises and a safety net (1 Nov 2026).** Photos reach the engines by path,
promises in Palo's own answers become scheduled tasks, an encrypted daily backup, and a heartbeat
watchdog on a free Cloudflare Worker.

**0021 · The night watch (2 Nov 2026).** Every repository checked without a model between 02:00 and
06:00; Claude is woken only for what is red, under a stricter quota gate, and a report goes out in
the morning only when there is something to tell.

**0022 · University practices in the vault (2 Nov 2026).** Lab practices, and only practices, copied
with their files. Rules decide what a practice is, not a model, because the exclusions must hold
every time; requirements are listed only if a sentence of the statement asks for them.

**0023 · Tasks in throwaway worktrees that reach `main` only when green (2 Nov 2026).** Palo, not
Claude, decides whether the tests passed, and never touches unfinished work in the owner's checkout.

**0024 · Passkeys for the web (3 Nov 2026).** Face ID and Touch ID through WebAuthn, with the passkey
in the iCloud Keychain so one registration covers phone and laptop, and the access key kept as the
way back in.

**0025 · A screen on the desk (8 Nov 2026).** An old Android phone in kiosk mode shows `/dock`, a
page made to be read at a glance: the time, what Palo is doing, what the PC is spending, the quota
and the last lines of the console, all without scrolling. It receives the monitor's samples but never
asks for Claude's quota, so a screen that is on all night does not open quota windows at 3 a.m.

**0026 · One session per device (8 Nov 2026).** Each browser or app that signs in gets its own random
token, stored only as a hash with a name, how it signed in and when it was last used. The access key
only opens the door to a new device: changing it no longer signs anyone out or deletes passkeys. A
lost device loses its access alone, from the panel's device list or by asking Palo.

**0027 · "Hey Palo" on the Mac and the PC (8 Nov 2026).** After an afternoon fighting the phone's
microphone and autoplay rules, the desk screen became look-only and the wake word moved to a small
program of its own, `palo-ear`, that stays open on both computers. On the laptop listening can be
turned off, or limited to when it is charging, because at university it runs on battery.

**0028 · "Hey Palo" with a face on the Mac (8 Nov 2026).** A Swift menu-bar app launches `palo-ear`
and talks to it over pipes, one JSON line per message. While a conversation is on, a borderless
bubble under the menu bar shows what was said and Palo's answer, without taking focus or clicks.

**0029 · Faster answers on Telegram (9 Nov 2026).** What Gemini is writing is shown as a live draft
with Telegram's `sendMessageDraft`, at most once a second and without formatting; the final answer is
then sent as a normal message, so the phone's notification shows the answer and not "Thinking…".
General questions go to a Gemini process kept warm, which saves the three to five seconds the CLI
takes to start.

**0030 · CI that fixes itself as soon as it turns red (10 Nov 2026).** The CI watch already looked at
every repository every ten minutes. Now a red run in a project under watch hands Claude one narrow
fix in a throwaway worktree, without waiting for the night. One attempt per red streak: what one
attempt did not fix needs the owner, not the same attempt again.

**0031 · Ideas researched overnight (10 Nov 2026).** An idea written down with an `#investigar` tag
becomes a technical dossier in the vault by morning: Gemini searches and reads the web with the
subscription, in a home directory that holds only a browser. At most two a night, and the queue is
the vault itself, so removing the tag removes the idea from the queue.

**0032 · New lab practices ready to start (10 Nov 2026).** Deadlines from the Moodle and those stated
in the practice's text go to the calendar. A date taken from the text needs a sentence that really
appears in it and names that day and month. Events that already exist that day are not duplicated.

**0033 · A 3D globe of live public data (12 Nov 2026).** An open-source CesiumJS globe with aircraft,
ships, satellites, earthquakes and fires, installed apart and pinned to a commit, with no keys. It
starts on first use as a separate memory-capped service, is reachable only from the tailnet, stops
itself when idle, and Palo drives it by voice through its own MCP tools.

**0034 · "Look at what I'm getting" (12 Nov 2026).** `look_at_screen` captures the screen of the
computer the owner is talking from and hands the image to the engine, not to the owner. The answer is
what reaches the phone. Only on request, never continuous capture, and what is on screen is data.

**0035 · Talking to Palo while it works (12 Nov 2026).** A general turn that has not answered after
30 seconds moves to the background with a one-line notice, and the chat is free again. Up to three run
at once. "Stop" cuts the latest of everything running, "how's it going?" answers at once without a
model, and background work that a restart interrupted is resumed once.

**0036 · OSINT tools (13 Nov 2026).** A separate MCP server with two passive tools: Maigret for a
username over the top 100 sites without recursion, and a local-only email lookup. Each runs in its
own environment, in its own process group, with a timeout, and the output is filtered so it does not
flood the model's context.

**0037 · Restarting itself with new code (14 Nov 2026).** Every two minutes Palo fetches `main` and
fast-forwards only a clean checkout that is behind. It waits until nothing is running or queued and
the owner has been quiet for five minutes, checks that the new code imports in a separate process,
re-registers its MCP server, and exits cleanly for systemd to start it again.

**0038 · A copilot on the Mac and continuity between computers (15 Nov 2026).** One standard-library
probe, run on both machines, reads what the tools leave behind (pytest's last-failed cache, Xcode's
build logs). When the same tool fails twice in a row on the same repository, Gemini has a diagnosis
ready before anyone asks. When the owner switches from one computer to the other, a summary of what was
left open there arrives, assembled from git and running work, without a model.

**0039 · Watching production (15 Nov 2026).** The deployed Workers, a D1 database and public sites are
checked from outside over HTTP, as a user would, with no new Cloudflare keys. A failure is confirmed
before anyone is told: two rounds in a row, three if it is only slow. The alert carries DNS,
each redirect with its timing and what the error page says. There is one alert when it goes down and
one when it comes back. (This later grew into InfraPing, a public project of its own.)

**0040 · A Palo that gets itself back up (16 Nov 2026).** Before every start, a standard-library
rescuer reads how the previous run ended from the journal and names the likely cause with rules (out of
memory, full disk, bad settings, a syntax error). After three crashes in a row on a commit that never
reached stable, it rolls back to the last one that did, if the checkout is clean. Each background loop
has its own supervisor with backoff, so one failing watch no longer dies silently.

**0041 · A measurable Palo (16 Nov 2026).** One trace per turn, without its text: where it came in,
what it was, who answered, the time of each stage and of the first chunk, the engines that failed
first and the quota it spent. Tool calls are measured in the MCP server, which every call goes
through. A `/metrics` page, a weekly summary in the daily note and CSV export all come from the same
pure functions, so the figures never disagree.

**0042 · Palo learns from its mistakes (16 Nov 2026).** Corrections, repetitions and misrouted
messages are recognised with narrow rules, not a model: a missed one costs nothing, an invented one
would end in a fix for something that was not broken. A weekly review groups them by cause. Code
fixes go to Claude in a worktree and need the owner's button to reach `main`; changes to the
persona are proposed, never applied.

**0043 · Palo on the iPhone (16 Nov 2026).** A small API for iOS Shortcuts behind a device token that
is only valid there, and a native SwiftUI app for the owner only, outside the App Store. It has a Live
Activity and Dynamic Island for the Claude task in progress, quota widgets, and a home screen. Palo
sends the pushes itself with a minimal APNs client over HTTP/2.

**0044 · Mail sorted before it is opened (17 Nov 2026).** Unread mail across every account is sorted
into reply, read, archive or junk without moving anything. Replies come as a draft card (*Send /
Edit / Ignore*). An unsubscribe is offered only after three unwanted mails from the same list, and
receipts become expenses only if the amount is written in the mail. Off by default.

**0045 · A Telegram Mini App (17 Nov 2026).** The panel, the monitor and the approvals open inside
the chat, signed in by Telegram's own HMAC signature, checked in constant time against the allowlist.
No third-party script is loaded. An approval can be seen whole: a mail draft editable before sending,
Claude's full diff before it is pushed.

**0046 · Capabilities as plugins (17 Nov 2026).** A small contract (a manifest, background watches
and CLI commands) and a toolbox lent by the core, so adding or removing a capability does not touch
the core. Two capabilities were migrated as a proof. The rest of the migration was parked on 23 Nov.

**0047 · AI Core inside Palo, behind settings (17 Nov 2026).** The owner's own AI infrastructure, a
set of small from-scratch components published as an installable SDK, gets its first production
client. It is an optional dependency pinned to a release, and every piece is behind a setting that
is off by default. The rule was that nothing that already works may get worse.

**0048 · An alternative long-term memory, in shadow (17 Nov 2026).** AI Core's memory layer, which
scores similarity × importance × decay, answers next to the hybrid index over the same chunks and the
same embeddings, so the two can be compared on real messages. The measurements took importance by
type and forgetting out of the defaults; the hybrid index still decides.

**0049 · A learned router, in shadow (17 Nov 2026).** A logistic regression over n-grams that answers
in a fraction of a millisecond on the CPU, served from a champion/challenger registry. In shadow mode
it only records agreement with the local model. When switched on it decides only when it is at least
90 % sure, and otherwise the local model decides.

**0050 · An input guard (17 Nov 2026).** A prompt-injection detector in front of every message, in
observe or block mode, rules-only by default. On public data it had perfect precision and no false
positives on 1,500 real questions. Only injection counts: the owner's own data and swear words are
not attacks.

**0051 · Every turn as an OpenTelemetry trace (17 Nov 2026).** Nothing new is measured; what is
already measured is exported with the GenAI semantic conventions, a root span per turn and one per
stage and model call, to any OTLP endpoint such as Jaeger or Grafana Tempo.

**0052 · Messages for Palo stay with Palo (20 Nov 2026).** "Push it to main" sent while a project was
active ended up in a Claude session locked in its worktree, which answered that it could not. Now
messages that address Palo by name, and orders to push, deploy or restart, are recognised by code
before the router and stay with the general assistant.

**0053 · Messages in a row arrive as one (20 Nov 2026).** The owner writes in bursts. Telegram text
now waits 2.5 seconds after the last message, and at most 10 from the first, and the burst reaches
the core as a single message with a single answer.

**0054 · Research that knows the project (20 Nov 2026).** Two overnight dossiers proposed building what
already existed. Now Palo itself, with code, puts the project's decision records, module tree and
roadmap into the brief, marked as data, while the researcher's home directory still holds nothing
private.

**0055 · Reported failures go to Claude (20 Nov 2026).** "It still doesn't work" was being debugged by
the general model, by hand, six times in an hour. Phrases that report a failure are now recognised by
code and come with a note telling the general assistant to hand project bugs to Claude rather than
investigate them itself.

**0056 · Tasks through fleet (21 Nov 2026).** The worktree lifecycle of 0023 (branch from a fresh
`origin`, the repository's checks, overlap warnings, rebase and push without force) moves to
[fleet](https://github.com/juanmmm21/fleet), a separate public orchestrator for coding agents. Palo
keeps what only it has: launching Claude, the quota, questions to the owner and the notices. If fleet
does not answer, Palo does it the old way.

**0057 · Using programs that have no command line (20 Nov 2026).** A local loop of screenshot →
model → one atomic action → screenshot, with the same 4B model that already routes, picked by a bench
of 74 real controls on the owner's screens (76 % on the first try with a second, closer look). It
stops at a step or time cap, when the screen stops changing, or as soon as the owner touches the
mouse or keyboard. No confirmation button, by the owner's choice. Every action is logged.

**0058 · Memory of the whole conversation (23 Nov 2026).** Every exchange (a message and the answers
that follow it) is indexed with its date in the same hybrid index, as it happens. Each message gets
up to three relevant past exchanges, and `recall_conversation` answers "what did I tell you about the
logo last week?". Each day is also summarised and indexed. The budget was the memory stage's p95 of
91 ms plus 50 ms.
