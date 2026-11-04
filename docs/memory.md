# Memory

Palo's owner does not want to teach the same thing twice, and does not want to manage an assistant
with commands. Both shape how Palo remembers.

## One memory, in a vault

Long-term memory is an Obsidian vault, and that vault is the private git repository holding the
owner's coding-agent context (see [machines.md](machines.md)). It contains:

- **Memories**, one fact per file, in the same Markdown-with-frontmatter format Claude Code uses
  for its own, with an index loaded into every session. Claude Code on the laptop reads them without
  any extra code, and the owner can open, edit or delete them in Obsidian.
- **A note per project** with its state, stack, repositories, last session, steps done and pending,
  and a progress prompt that a fresh agent can pick up. A Dataview board lists them all.
- **Daily notes**, where Palo writes a nightly summary of the day's project work, a one-line summary
  of every call and anything worth keeping from the conversation.
- **Lab practices** from the university, with their files and checklists.

There is no second memory: the SQLite database holds only the conversation thread, and the search
index below is derived and can be rebuilt from the vault at any time.

## Remembering without being told

After each exchange, a keeper asks a model whether something durable was said: a preference, a
correction, a fact about a project. It checks that the owner really wrote it, not that the model
inferred it, and stores it as a memory, announcing it in one line. Asking to forget something, in
plain words, removes it from the vault and from the index at once.

Before every task, the memories related to the message are added to the prompt, so they are used
without being asked for.

## Finding things by meaning

Words are not enough: "the gym thing" should find GymBuddy, and "uni" should find the Moodle
notes. Recall is hybrid and fully local:

- **Full-text search** with SQLite FTS5 (BM25), which is better for proper names, acronyms and
  paths.
- **Vectors** from a small multilingual embedding model in Ollama, stored with `sqlite-vec` in the
  same database, which is better for paraphrases.
- **Reciprocal rank fusion** merges the two lists without weights to calibrate.
- **A deterministic graph** in the same database: notes, memories, projects and days as nodes;
  wiki links, frontmatter properties, project names and dates as edges. Results bring their direct
  neighbours along.

Notes are split by heading, memories stay whole, and every chunk keeps its path and heading so it
can be cited. Indexing is incremental by hash after every sync. If the embedding model is not
available, search falls back to BM25 and says so in the log.

The same search is a tool, so Gemini, Claude, the local model and both calls use it.

## The thread

The conversation belongs to Palo, not to an engine. One SQLite history is shared by the three
models and by the calls: a sentence spoken on the call is in the thread when the next Telegram
message arrives. Older turns are summarised locally instead of being dropped.

## Ideas against what has been built

With dozens of repositories, nothing tells the owner that an open idea already has code behind it.
Every few hours Palo lines up the open ideas and notes on one side and every cloned project on the
other (the start of its README and the names of its modules), and Gemini proposes pairs.

Embeddings were measured first and rejected: short Spanish titles against English READMEs gave
noise, with a few sections attracting everything and no threshold that kept the real matches.
Numbered ids work instead, and the code drops anything not in the catalogue. Each group has a
fingerprint, so only what changed is asked again. The results are labelled as suggestions, stay out
of the memory graph (whose edges are all written by someone) and reach the chat at most once a day.
