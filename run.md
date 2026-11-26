# Book Summary: the claude.ai run (phone, no Mac)

This is the claude.ai version of the /book-summary skill. It produces the same summary as the Mac version: a new
tab in the user's "Book Summaries" Claude Doc, retelling the whole book so well that the user can read it instead
of the book. It follows `summary-spec.md` exactly (read it first, every run): every beat of the story, the world and
the characters woven into the story (no separate world tour or cast list), no pictures in fiction, one short
"What it all means", a Sources line with the check counts. Nothing is invented: every fact comes from a researched
story bible, and an independent check in a separate chat confirms it.

There are two chats per book, because a chat cannot check its own writing (test runs that did both in one chat
missed 16 to 21 errors a book):
- **Writing chat**, started with "Book summary: <title>": sections 1 to 5 below. It ends by telling the user to
  open a new chat and send "Book summary check: <title>".
- **Check chat**, started with "Book summary check: <title>" (or "check", "fact-check" or "verify" plus a book
  already in the library): section 6 only. It never saw the writing and judges only what is in the doc.

The user usually starts these from the Claude phone app and checks back later. Work through the whole run without
asking anything, unless the title could mean two different books.

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
  first matters. Keep a copy of every section you write in `/tmp/book/summary.md` as you go.
- Follow the spec's style rules: tell scenes, don't list events; give the big scenes room; match the book's voice;
  short quotes only with wording confirmed in the bible; cut anything that could describe any book.
- Write only what the bible says happened. Don't fill a gap with a plausible detail (a color, a gesture, who
  spoke, how many) that you haven't confirmed: a test run lost most of its accuracy this way.
- Call each character by one name throughout (the book's nickname for them can be mentioned once).
- After each part, reread it once as an editor: any paragraph that reads like a study guide ("X happens. Then Y.
  Then Z.") gets rewritten as a scene, with what someone wants or fears, one concrete image or line of dialogue
  from the bible, and the turn that leads into the next paragraph.

## 5. Finish the writing chat

- Read the finished tab back from the doc (a docs read, not your notes) and save it as `/tmp/book/summary.md`.
  Count the words with `wc -w` (never estimate).
- Quick self-pass only: every quotation matches the bible word for word and is given to the right speaker; every
  name is spelled one way; no paragraph reads like a study guide. Do not run the fact check here and do not report
  check counts: the check belongs to the separate check chat.
- Replace the status line with the standfirst (a hook, the real word count, "full spoilers"). End the tab with the
  Sources section the spec describes, but with this italic line until the check chat replaces it:
  *Not yet independently checked. To check it, send "Book summary check: <title>" in a new chat.*
  Then the source links.
- Contents tab: add one row at the top of the table, under the header (Book as a mention chip of the new tab ·
  Author · Year · Added (today's date) · a one-line hook).
- Coverage notes are for the reader: say what the sources do and don't cover, not which sites were paywalled or
  which tools failed. Leave biography trivia out of "In brief".
- Tell the user, briefly: a three-sentence teaser, the word count (from `wc -w`), anything the sources couldn't
  cover, and, on its own last line, exactly what to send next: `Book summary check: <title>` in a new chat.

## 6. The check chat ("Book summary check: <title>")

You are the independent checker. You did not write this summary and have none of the writer's notes: judge only
the tab as the reader sees it, against sources you open yourself. Read `summary-spec.md` first. Work in
`/tmp/check/` in the sandbox and keep your reply text short.

1. **The tab.** Open the library doc, find the book's tab in its tab list, read it with a docs read, and save its
   text as `/tmp/check/summary.md`, one paragraph per line, numbered P1, P2, …. Run `wc -w` on it.
2. **Sources.** If the book is public domain, get its full text by the same route as section 3 (sandbox `curl`,
   then web fetch, then a chapter-by-chapter copy) and save it to `/tmp/check/text.txt`; it outranks everything
   else. Also open at least two chapter-by-chapter study guides (three if there is no full text) and save each to
   a file. Grep the files; don't read whole pages into the conversation.
3. **Your own beat list, before judging.** From the sources, not from the summary, write
   `/tmp/check/beats.md`: every story beat in order, by chapter, numbered, essential ones starred (two to five
   per chapter for a novel). Then mark each covered, partial or missing in the summary.
4. **Every claim, paragraph by paragraph.** Go through the summary one paragraph at a time and append one row per
   factual claim to `/tmp/check/claims.tsv`: paragraph number, the claim (short), verdict (confirmed / wrong /
   unverifiable), evidence (line number in the text or the guide's wording). A claim is any name, relationship,
   event, its order, number, who says or does what, quotation, setting detail or critic's view. Expect one claim
   every 25 to 40 words. After each chapter, run a count of rows per paragraph against its words divided by 40,
   and go back to any paragraph that falls short before moving on.
5. **Where errors hide.** Earlier test runs got whole scenes right but put details in the wrong place, so check
   placement, not just existence: is this detail in this scene, at this time of day, in this order? Is this line
   said by this person, at this moment? Is this quotation from this scene? Did the writer add a simile, a gesture,
   a number or a motive the sources don't have? A true detail in the wrong scene is "wrong".
6. **Fix the doc.** For each wrong claim, replace the whole paragraph (a docs update replacing that block) with a
   corrected version grounded in the sources, keeping the writing as good or better. Add each missing or partial
   beat into the paragraph where it belongs, told as a scene. Cut or hedge what stayed unverifiable. Rewrite any
   paragraph that reads like a study guide or lists events.
7. **Finish.** Read the tab back from the doc, run `wc -w` again, and correct the standfirst's word count. Replace
   the "Not yet independently checked" line with one italic line: the writing date, "checked on <date> in a
   separate chat against <the sources>", and the counts (beats: total, fully covered before fixes; claims:
   checked, wrong, unverifiable; "all fixed"). Then tell the user, briefly: the counts, the three biggest fixes,
   the new word count, and anything you could not check.
