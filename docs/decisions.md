# Decisions

Every structural choice in Palo is recorded as an architecture decision record in the private
repository, with its context, the decision, why, and what it costs. These are the 24 so far, in
order, one paragraph each.

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
