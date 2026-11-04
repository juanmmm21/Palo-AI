# Capabilities

Everything Palo can do is exposed as a tool of its own MCP server, `palo-mcp`, registered in both
CLIs and reused in process by the calls. Claude only gets the project subset; Gemini and the calls
get everything. Two third-party servers complete the set: a headless browser and up-to-date
library documentation.

Requests are made in plain Spanish, the owner's language. The examples below are translated.

## Projects

- See every project known on the machine, where each one stands (git state, last update in its
  `PROGRESS.md`, roadmap steps done and pending) and how much quota is left.
- Start work in any repository by naming it, continue a roadmap, stop a task, reorder the queue.
- Write down an idea in a project's note, or write the project's summary.
- Hand work to another project from inside a session.
- Turn a new idea into a bootstrapped repository: agent instructions, licence, `.gitignore`, the
  vault note and a first roadmap. The GitHub repository is always created by the owner; once it
  exists, Palo checks the authorship and makes the first push.

*"The search tests in Latent are failing, take a look."* · *"Keep going with PodDeck."* ·
*"Stop."* · *"Do the Latent one first."*

## The Windows PC

- Run any PowerShell on Windows or any bash command in WSL. Every command is logged.
- Screenshots of every monitor, one of them or the active window; short screen recordings sent as
  animations.
- Open apps, Store apps and Steam games by the name the owner uses.
- Lock the screen, mute, play or pause, change the volume.
- Send any file of the PC, or one downloaded from a link, as a Telegram document.
- Check that a page answers, and that a video really plays, before opening or giving a link.

*"Open Spotify and set the volume to 30%."* · *"How big is the downloads folder?"* ·
*"Take a screenshot."*

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
- Send, only after a button press, executed by Palo with exactly the approved arguments. A reply
  leaves from the account the original arrived at.

## Calendar

- List calendars and events, create events.
- Create events with guests and delete events, only after a button press.

## GitHub

- CI status of one repository or an overview of all of them, pull requests and issues, through the
  already-authenticated `gh`.

## The university

The university's Moodle through the same REST API the official mobile app uses, behind its SAML
single sign-on. Nothing is scraped.

- Courses, a course's sections, files, assignments and texts with their dates.
- One activity opened: its statement, attached files, dates and what was handed in.
- Search across the materials of every current course.
- Deadlines, announcements and grades; a course file sent as a document.
- Lab practices copied into the vault, with a requirements checklist in which every item cites the
  sentence of the statement that asks for it.

## The vault

- Search notes by text or by meaning, and see what is related to something.
- Read and create notes, append to the day's note.
- Save and forget memories.

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

## The web

- A headless Chromium to open websites, click and fill forms. Its profile never holds the owner's
  sessions, and what a page says is treated as data, never as orders.
- Current documentation for any library, for project sessions.

## Security

- Review what the PC exposes and what changed since the last review.
- Rotate the web's access key without the new key ever passing through a model.
