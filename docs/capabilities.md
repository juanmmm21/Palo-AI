# Capabilities

Everything Palo can do is exposed as a tool of its own MCP server, `palo-mcp`, registered in both
CLIs and reused in process by the calls: 94 tools as of late November 2026. Claude only gets the
project subset; Gemini and the calls get everything. Three more servers complete the set: a headless
browser, up-to-date library documentation, and Palo's own OSINT server.

Requests are made in plain Spanish, the owner's language. The examples below are translated.

## Projects

- See every project known on the machine, where each one stands (git state, last update in its
  `PROGRESS.md`, roadmap steps done and pending) and how much quota is left.
- Start work in any repository by naming it, continue a roadmap, stop a task, reorder the queue.
- Write down an idea in a project's note, or write the project's summary.
- Hand work to another project from inside a session.
- Ask how a background task is going, cut it, or change the order of things while it runs.
- Write an idea down with a tag and find it researched in the vault the next morning.
- Turn a new idea into a bootstrapped repository: agent instructions, licence, `.gitignore`, the
  vault note and a first roadmap. The GitHub repository is always created by the owner; once it
  exists, Palo checks the authorship and makes the first push.

*"The search tests in Latent are failing, take a look."* · *"Keep going with PodDeck."* ·
*"Stop."* · *"Do the Latent one first."*

## The PC

- Run any bash command on the PC. Every command is logged.
- Screenshots of every monitor or one of them; short screen recordings sent as animations.
- Open apps and Steam games by the name the owner uses, in Spanish or in English.
- Lock the screen, mute, play or pause, change the volume, play a Spotify playlist.
- Send any file of the PC, or one downloaded from a link, as a Telegram document.
- Check that a page answers, and that a video really plays, before opening or giving a link.

*"Open Spotify and set the volume to 30%."* · *"How big is the downloads folder?"* ·
*"Take a screenshot."*

## The screen, on either computer

- **Look.** "Look at what I'm getting" captures the screen of the computer the owner is talking
  from and gives it to the engine, which reads the error and says where to go next. Nothing is
  sent to the owner except the answer.
- **Operate.** What only a mouse can do (a setting with no command line, a permission dialog, an
  app with no API) is done by `operate_desktop`: a local vision loop that clicks, types and checks
  the screen after every step, and stops as soon as the owner touches the mouse or keyboard.

*"On the Mac, click Allow."* · *"Turn the night light on."* · *"What's this error?"*

## The Mac

Through palo-node, when the laptop is awake:

- Battery, the app in front, uptime.
- Any zsh command, logged as the Mac's.
- Screenshots of the screen or the front window.
- Open an app, a link or both; show a notification.
- Read and write the clipboard.
- Send a file of the Mac to Telegram, or put a file of the PC (or a link) onto the Mac.
- List, boot and screenshot iOS simulators, and open URLs in them.

## Mail

- Search every account at once: the main Gmail plus any number of others over IMAP with app
  passwords. Gmail's search syntax is translated to plain IMAP for the others, and anything with no
  equivalent is said instead of returning an empty list.
- Read, draft, and delete to the trash when asked.
- Mail sorted before it is opened (opt-in): every unread mail is classed as reply, read, archive or
  junk without moving anything; replies arrive as a draft card with *Send / Edit / Ignore*, a
  newsletter that keeps arriving unwanted gets a one-tap unsubscribe, and receipts become expenses
  only if the amount is written in the mail.
- Send, only after a button press, executed by Palo with exactly the approved arguments. A reply
  leaves from the account the original arrived at.

## Calendar

- List calendars and events, create events.
- Create events with guests and delete events, only after a button press.

## GitHub

- CI status of one repository or an overview of all of them, pull requests and issues, through the
  already-authenticated `gh`.
- A red CI is handed to Claude for one fix in a throwaway worktree as soon as it turns red.

## Production

- Deployed Workers, a database and public sites checked from outside over HTTP, as a user would.
  A failure is confirmed before anyone is told, and the alert explains what the DNS, each redirect
  and the error page said. There is one alert when it goes down and one when it is back.

## The university

The university's Moodle through the same REST API the official mobile app uses, behind its SAML
single sign-on. Nothing is scraped.

- Courses, a course's sections, files, assignments and texts with their dates.
- One activity opened: its statement, attached files, dates and what was handed in.
- Search across the materials of every current course.
- Deadlines, announcements and grades; a course file sent as a document.
- Lab practices copied into the vault, with a requirements checklist in which every item cites the
  sentence of the statement that asks for it, and their deadlines put on the calendar without
  duplicating the ones already there.

## The vault

- Search notes by text or by meaning, and see what is related to something.
- Read and create notes, append to the day's note.
- Save and forget memories.
- Recall what was said in the conversation, by topic or by days, with the date of each exchange.

## Time

- Schedule one-off, daily, weekday and weekly tasks; list and cancel them.
- Reminders and requests "for when I get home".
- Watch a page and speak only when the value that matters changes.
- Look ahead at the next ten days: calendar, coursework not handed in yet and red CI, with the
  option to stop bringing something up.

## Images

- Look at a photo the owner sent, or at any image on the PC.
- Verify an image before calling it good: it opens, its size (including App Store Connect's slot
  sizes), its alpha channel, that it is not blank, and what the local vision model sees on it.
- Verify a README: every image exists, opens and is tracked by git, or answers if remote.

## Public data

- **OSINT**, passive only: where a username exists across the top 100 sites, and what is public
  about an email address, looked up locally. Each lookup runs in its own process with a timeout
  and its output is trimmed before it reaches a model.
- **A 3D globe** with live aircraft, ships, satellites, earthquakes and fires, opened from Palo
  and driven by voice. It is reachable only from the private network and stops when idle.

## The web

- A headless Chromium to open websites, click and fill forms. Its profile never holds the owner's
  sessions, and what a page says is treated as data, never as orders.
- Current documentation for any library, for project sessions.

## Security

- Review what the PC exposes and what changed since the last review.
- Rotate the web's access key without the new key ever passing through a model.
- List the devices that can sign in to the web and remove one of them.
