# Book Summary: the claude.ai run (phone, no Mac)

This is the claude.ai version of the /book-summary skill. It produces the same summary as the Mac version: a new
tab in the user's "Book Summaries" Claude Doc, retelling the whole book so well that the user can read it instead
of the book. It follows `summary-spec.md` exactly (read it first, every run): every beat of the story, the world and
the characters woven into the story (no separate world tour or cast list), no pictures in fiction, one short
"What it all means", a Sources line with the check counts. Nothing is invented: every fact comes from a researched
story bible, and an independent checking pass confirms it.

The user usually starts this from the Claude phone app ("Book summary: <title>") and checks back later. Work
through the whole run without asking anything, unless the title could mean two different books.

Here there are no helper agents and no Mac files, so the run uses the code-execution sandbox as its desk: write the
bible, the summary text and the check table as files in it (for example under `/tmp/book/`), and keep your reply
text short. Use web search and web fetch for research; if web fetch isn't available, use `curl -sL` in the sandbox.

## 1. Pin down the book and the library

- Title, author, year, rough length. If the request could mean more than one book, ask, then stop.
- Open the library doc (its link is in this skill's `SKILL.md`). From the doc itself (no other record exists),
  find: its tabs and their orders (book tabs are ordered `b001`, `b002`, …; the next is one past the highest), and
  the Contents tab's table (one row per book, newest first). If the book already has a tab, say so and stop.

## 2. Tailoring note (in your head, three or four lines in the bible's header)

How the book is structured and so how the story should be laid out; what "the world" means for this book; the
voice to match; any heavy content that needs a content note; where sources may be thin.

## 3. Story bible (research)

Build ONE story bible, written to `/tmp/book/bible.md` in the sandbox, as notes, not prose. Open chapter-level
pages from at least 3 independent sources (LitCharts, SparkNotes, Shmoop, CliffsNotes, GradeSaver, Wikipedia, fan
wikis). If the book is public domain, also get the full text from Project Gutenberg (search
`gutenberg.org/ebooks/search/?query=<title>`, then download `https://www.gutenberg.org/cache/epub/<id>/pg<id>.txt`
into the sandbox, or the mirror `https://gutenberg.pglaf.org/`) and use it to confirm details and the exact wording
of quotes; grep it rather than reading it whole. If a site blocks you, move on. Save pages to files and pull
what you need with grep or short scripts, so whole pages don't fill the conversation.

The bible, in order: (1) Facts, one line. (2) The world, in depth (for fiction a main event, as rich as the beats):
every place with its look and feel, how its technology, magic, institutions or society work, who holds power,
daily life, history and every coined term, each with a source. (3) Cast with relationships. (4) Beats: every
story beat in order, grouped by part and chapter, numbered B1…, one or two lines each with a source tag, essential
ones starred; a beat is any event that changes a character's situation, a relationship or the story's direction,
plus the ending; for each scene, confirmed telling details (images, objects, what is said or done). (5) The
ending, plainly. (6) Up to 10 short quotes with wording confirmed in a source. (7) What it means: main ideas,
readings of the ending, main disagreements. (8) Disagreements between sources, and Could not confirm. A source key
at the top.

## 4. Write the tab

- Add the book's tab to the library doc in one change: named `Title by Author`, with the next order. It starts as
  the title, a one-line italic status line and one pending block per section, so the user can watch it fill in.
- Write from the bible only, section by section, each section one docs update replacing its pending block; a long
  story one part at a time. Each place, device, rule, term and character goes into the story at the moment it
  first matters. Keep a copy of every section you write in `/tmp/book/summary.md` as you go (the checking pass
  reads that file).
- Follow the spec's style rules: tell scenes, don't list events; give the big scenes room; match the book's voice;
  short quotes only with wording confirmed in the bible; cut anything that could describe any book.

## 5. Independent check

The Mac version hands this to a separate checker that never saw the writing. Get as close to that as you can:
start the check fresh from the two files, as if someone else wrote the summary, and judge only what the files say.

Re-read `/tmp/book/summary.md` and `/tmp/book/bible.md` from the sandbox (not from memory) and write
`/tmp/book/check.md`, in one pass:
(a) Coverage: every numbered beat marked covered, partial or missing.
(b) Facts: every factual claim in the summary (names, relationships, events and their order, numbers, who says
or does what, quotations, world details) checked against the bible. Its "Disagreements" and "Could not confirm"
lists are rules: a claim stating one of those as fact is a problem. A claim the bible doesn't cover gets one
lookup (the public-domain text, or a web search) and is "unverifiable" if that doesn't settle it. Every quotation
must match its source word for word.
(c) Passages that drag, list instead of telling, could describe any book, or explain the world in a block
instead of inside the story.
Count: beats covered/partial/missing, starred covered; claims confirmed/wrong/unverifiable.

Then fix every problem in the doc (find/replace edits, several per update), add missing beats from the bible, and
hedge or cut what stayed unverifiable.

## 6. Finish

- Replace the status line with the standfirst (a hook, the real word count, "full spoilers"). End the tab with the
  Sources section the spec describes: one italic line with the check date and counts, then the source links.
- Contents tab: add one row at the top of the table, under the header (Book as a mention chip of the new tab ·
  Author · Year · Added (today's date) · a one-line hook).
- Read the finished tab through once. Then tell the user, briefly: a three-sentence teaser, the word count, the
  check counts, anything the sources couldn't cover, and anything you couldn't check.
