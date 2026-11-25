# Security

Palo has a lot of power: it can run any command on two computers, read every mailbox and act on
the calendar. The design does not pretend otherwise. It keeps the surface small, makes every action
visible, and keeps the few actions that reach other people out of a model's hands.

## A small surface

- **Nothing listens on the internet.** Telegram is read by long polling, and only an allowlist of
  user ids is obeyed; anyone else is ignored without a reply.
- **Machines talk over Tailscale.** Maintenance is Tailscale SSH; the Mac's daemon listens only on
  its Tailscale address and checks a token on every request.
- **The web** is published through a tunnel with its own access layer, on top of Palo's own access
  key, a session per device, passkeys and a lock-out after repeated wrong keys. The Telegram Mini
  App has an address of its own without the access layer, so there the key and passkey logins are
  closed and the only way in is Telegram's signature. Cookies are `HttpOnly` and `SameSite=Strict`
  and every page runs under a strict Content Security Policy.
- **No OAuth apps.** Mail and calendar use app passwords over IMAP, SMTP and CalDAV; GitHub goes
  through the already-authenticated `gh`. The university's Moodle token is kept with mode 600 and
  never shown to a model.

## Full control, fully logged

The models may run any PowerShell, bash or zsh command without asking. That is the owner's explicit
choice, made knowing the prompt-injection risk. In exchange:

- **Every command is logged**, with the machine it ran on, whether or not the model mentions it.
- **What a web page or a photo says is data, never orders**, and the headless browser never holds
  the owner's sessions or receives passwords.
- **Engines are told never to write a key, password or token** in the chat.
- **What is on screen is data too**, when Palo looks at a screen or operates one. The screenshots
  of an operation are kept only for the last task, never in the vault or the backups, and the
  operation stops the moment the owner touches the mouse or keyboard.
- **An input guard** can sit in front of every message, observing or blocking prompt injection in
  what the owner forwards or pastes. It is rules-only by default, because for the owner's own
  messages a false block costs more than a missed warning.
- **OSINT is passive only**: no recursion into the accounts it finds, a cap on the sites it asks,
  and each lookup in its own process group with a timeout.

## What a model never does

Sending an email, inviting guests to an event and deleting an event are denied to every model. The
request comes back as a button (on Telegram) or a card (on a call); if approved, Palo itself runs
exactly the call that was shown. The same holds in project sessions for anything outside the
allowlist, such as a force push or deleting files outside the repository.

Work done by Claude in a project reaches `main` only when the repository's own checks pass, and
never with a commit that has another author or a co-author trailer.

## Hearing about the unexpected

Palo does not block access; it makes sure its owner hears about any access nobody expected, and
can cut it.

- **Alerts**, checked every 30 seconds: a device joining the tailnet, an SSH session into the PC,
  and wrong keys on the web with where they came from. Each part notes what
  it sees the first time without alerting.
- **A weekly review**, or one on demand: ports listening outside loopback, the tailnet, what Tailscale and the tunnel publish, and the permissions of the
  files holding secrets, with what changed since the last one.
- **Rotating the web key never shows it to a model.** The new key is written to the environment file
  and delivered in a Telegram message that deletes itself after ten minutes; the old one is scrubbed
  from the history. Sessions already open stay open; each device is removed on its own.

## When things go wrong

- **A heartbeat** goes to a free Cloudflare Worker every five minutes. After 15 minutes without one
  the Worker messages the owner directly through Telegram's API, once, and again when Palo is back.
  A heartbeat catches a hung process too, which polling a URL behind the access layer would not.
- **A black box before every start.** A rescuer that uses only the standard library, run from a copy
  outside the repository so it works exactly when Palo's code does not, reads how the previous run
  ended from the journal and names the likely cause with rules. After three crashes on a commit that
  never ran stably, it rolls back to the last one that did, if the checkout is clean.
- **Supervised loops.** Every background watch restarts on its own with backoff, and after five
  failures in a row it is switched off until the next start and the owner is told.
- **When the watchdog speaks**, it also takes over the bot: it keeps what the owner writes and
  hands it back to Palo with the first heartbeat, so nothing said during an outage is lost.
- **A daily backup** of the state (databases through SQLite's backup API, settings and history),
  encrypted with AES-256 and kept as a release of the private repository, seven deep.
