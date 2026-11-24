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
wikis). If a site blocks you, move on. Save pages to files and pull what you need with grep or short scripts, so
whole pages don't fill the conversation.

**The book's own text is the best source.** If the book is public domain, get the full text and confirm every
beat and every quote against it. The sandbox's own downloads may be limited to a few standard sites, so try in
this order until one works: `curl -sL` in the sandbox to `https://www.gutenberg.org/cache/epub/<id>/pg<id>.txt`
(find the id by searching "Project Gutenberg <title>") or the mirror `https://gutenberg.pglaf.org/`; then web
fetch of the same URLs; then web fetch of a chapter-by-chapter copy (Standard Ebooks, Lit2Go, Wikisource), one
chapter at a time as you need it. Note in the bible which copy you used. Study guides get details wrong (who says
a line, the order of small events, things that never happen); where a guide and the text differ, the text wins.

Beats are fine-grained: for a novel, expect two to five per chapter. A first fight, a rumor, a river crossing, a
death in passing each count. A list with far fewer beats than chapters times two has skipped some.

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
- Write only what the bible says happened. Don't fill a gap with a plausible detail (a color, a gesture, who
  spoke, how many) that you haven't confirmed: a test run lost most of its accuracy this way.
- Call each character by one name throughout (the book's nickname for them can be mentioned once).
- After each part, reread it once as an editor: any paragraph that reads like a study guide ("X happens. Then Y.
  Then Z.") gets rewritten as a scene, with what someone wants or fears, one concrete image or line of dialogue
  from the bible, and the turn that leads into the next paragraph.

## 5. Independent check

The Mac version hands this to a separate checker that never saw the writing. Get as close to that as you can:
start the check fresh from the two files, as if someone else wrote the summary, and judge only what the files say.

First read the finished tab back from the doc (a docs read, not your notes or memory) and save its text as
`/tmp/book/summary.md`, so you check what the reader will actually see. Then re-read it and `/tmp/book/bible.md`
from the sandbox and write `/tmp/book/check.md`, a table with one row per claim:
(a) Coverage: every numbered beat marked covered, partial or missing.
(b) Facts: go through the summary sentence by sentence and list EVERY factual claim in it (names, relationships,
events and their order, numbers, who says or does what, quotations, setting details, what critics said), not a
sample. A typical summary holds one checkable claim every 25 to 40 words, so a 7,000-word summary has roughly
200 to 280; if your count is far below that, you skipped sentences, so go back. Check each claim against the
book's own text where you have it, otherwise against the bible. Its "Disagreements" and "Could not confirm" lists
are rules: a claim stating one of those as fact is a problem. A claim neither covers gets one lookup and is
"unverifiable" if that doesn't settle it. Every quotation must match its source word for word, and every line
must be given to the right speaker.
(c) Passages that drag, list instead of telling, read like a study guide, could describe any book, or explain the
world in a block instead of inside the story.
Count: beats covered/partial/missing, starred covered; claims checked, confirmed, wrong, unverifiable. Then
count the words with `wc -w` (never estimate).

Then fix every problem in the doc (find/replace edits, several per update), add missing beats from the bible, and
hedge or cut what stayed unverifiable. Rewrite the study-guide passages (c) found as scenes.

## 6. Finish

- Replace the status line with the standfirst (a hook, the real word count, "full spoilers"). End the tab with the
  Sources section the spec describes: one italic line with the check date and counts, then the source links.
- Contents tab: add one row at the top of the table, under the header (Book as a mention chip of the new tab ·
  Author · Year · Added (today's date) · a one-line hook).
- Coverage notes are for the reader: say what the sources do and don't cover, not which sites were paywalled or
  which tools failed. Leave biography trivia out of "In brief".
- Read the finished tab through once, from the doc. Then tell the user, briefly: a three-sentence teaser, the word
  count (from `wc -w`), the check counts, anything the sources couldn't cover, and anything you couldn't check.
