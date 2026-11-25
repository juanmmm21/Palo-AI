# Two computers

Palo lives on one machine and works on two. The owner mostly codes on a MacBook; the assistant
runs on the desktop at home, where it can stay on around the clock. Since 22 November 2026 that
desktop runs native Ubuntu; for its first five weeks Palo lived inside WSL2 on Windows.

```mermaid
flowchart LR
    subgraph PC[Home desktop · Ubuntu]
        PALO[palo run<br/>chat · loops · web]
        MCP[palo-mcp]
        CC[Claude Code]
        PALO --- MCP
        CC --- MCP
    end
    subgraph MAC[MacBook]
        NODE[palo-node<br/>LaunchAgent]
        AGENT[launchd sync agent]
        CCM[Claude Code<br/>owner's sessions]
    end
    MCP -->|JSON over Tailscale<br/>bearer token| NODE
    PALO <-->|git, every 15 min| CTX[(Private context repo<br/>= Obsidian vault)]
    AGENT <-->|git| CTX
    CCM -.reads.- CTX
```

## The PC

Palo runs as a systemd user service in the owner's own desktop session, which restarts on any exit
and survives reboots. Being inside that session is what lets it reach the desktop the official way,
since Wayland allows nothing else:

- **Screens** through the desktop portal's screenshot interface over D-Bus, in pure Python. Palo
  crops the monitor it wants using Mutter's display configuration. It grants itself the portal's
  permission the first time, as part of the full control its owner chose, and respects it if the
  owner takes it away.
- **Mouse and keyboard** through Mutter's remote-desktop API, without root or `uinput`.
- **The session**: `loginctl` to lock and to know whether it is locked, `wpctl` for the volume,
  MPRIS for the music, and Mutter's idle monitor for how long nobody has touched it.
- **Apps and games** from the GNOME launchers (with their Spanish names, and the English ones as
  aliases) and Steam's manifests, started with `gio launch`.
- **The machine** from `/proc` and the native `nvidia-smi`, which lists every process with its video
  memory, something the paravirtualised GPU of WSL2 never gave.

Maintenance happens over Tailscale SSH from the laptop.

Moving off WSL removed a whole layer of machinery: a hidden process that only kept WSL open, a
Windows scheduled task that guarded it, an emergency SSH into Windows, and an empty journal caused by
the virtual machine's clock. It also cost something. Wayland has no "active window", a capture
takes half a second, and nothing can be clicked while the session is locked.

### The GPU

The GPU belongs to the owner first. With everything loaded Palo holds about two thirds of it, and
most of that lets go by itself: Ollama drops models after five idle minutes and Whisper loads on
demand. Every 20 seconds Palo looks at the whole card; if it stays busy or nearly full for a minute,
something else wants it, whether a game or a render. Palo unloads all its models and carries on from
the CPU with four threads, then goes back after three calm minutes. A marker file tells the separate
MCP process to follow along.

## palo-node

The laptop is reached through palo-node, a small daemon written in Go for this purpose:

- **It runs in the owner's session.** A LaunchAgent, not an SSH login, so it holds the macOS
  privacy permissions granted once (screen recording, accessibility). A command sent over SSH would
  not be allowed to take a screenshot or drive an app.
- **It listens only on the Mac's Tailscale address** and checks a bearer token on every request.
  Nothing is exposed on the local network or the internet.
- **JSON over HTTP.** Two programs written by the same person, a dozen calls and one client: no
  code generation needed, and everything can be tried with `curl`. Long commands stream their output
  back as NDJSON while they run.
- **Few capabilities, done well.** Status, free shell commands, screenshots, opening apps and links,
  notifications, the clipboard, files in both directions and the iOS Simulator. Clicking and typing
  on the Mac needed no new endpoint: JXA run through the shell already holds the accessibility
  permission and can post `CGEvent`s. They come from the
  Mac's own commands (`screencapture`, `osascript`, `open`, `pbcopy`, `xcrun simctl`), which is why
  Go was enough: it cross-compiles from the PC and is tested on Linux, so Palo can maintain its own
  daemon without Xcode.
- **A laptop sleeps.** If the Mac does not answer within five seconds, the tool says so in one
  sentence. Nothing is retried or queued behind the owner's back.

Every command lands in the same log as the PC's, tagged with the machine it ran on.

With two screens on the Mac, Palo looks at and operates the screen of the frontmost visible window,
not of the frontmost app: a system dialog belongs to another process and often sits on the other
screen.

## Following the owner between computers

- **Which computer is in use** comes from each one's idle time. When the other one has just been
  touched and the previous one has been still for five minutes, the owner has switched. Actions
  then run on the machine the owner is talking from, unless another is named.
- **Where things were left** arrives on the switch: a summary of the uncommitted files, unpushed
  commits and running tasks of the other machine, assembled from git by a small standard-library
  probe that runs unchanged on both. No model is involved.
- **A copilot for repeated failures.** The same probe reads what the tools already leave behind,
  pytest's last-failed cache and Xcode's build logs, without running anything. When the same tool
  fails twice in a row in the same repository, a diagnosis from Gemini is waiting before anyone
  asks.

## One shared context

Claude Code on the laptop has years of accumulated context: global instructions, memories,
skills, templates, and the agent files of every project, which are git-ignored on purpose so they
never reach public repositories. A clone on the PC would arrive without any of it.

So that context lives in its own private repository, with the same layout on both machines, and
`palo context sync` keeps it in step: pull with rebase, copy each file to its real place, collect
local changes, commit and push. It runs before and after every Claude session on the PC, every
15 minutes in `palo run`, and from a launchd agent on the Mac. The memory folder's name encodes an
absolute path, which differs between macOS and Linux; the sync translates it. A conflict is never
resolved silently: the sync stops and Palo says so.

That repository is also the Obsidian vault and Palo's long-term memory (see
[memory.md](memory.md)). There is one memory, not two that drift apart.
