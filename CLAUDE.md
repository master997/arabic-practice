# Arabic-practice — project notes for Claude

Single static `index.html` (vanilla JS + `localStorage`, no build, runs by double-click,
must work offline). All app logic and seed data live in that one file. `sheets/` holds
scanned binder pages. The learner is studying **Egyptian colloquial** for fast
conversational fluency — not MSA.

## Cards
Schema (only `ar` + `en` required; everything else optional): `ar`, `en` (clean gloss —
usage asides go in `note`, not here), `pr` (Egyptian transliteration: `3`=ع, `'`=hamza/ق,
`g`=ج), `ex:[arExample, enExample]`, `root` (three spaced letters e.g. `"ر و ح"`), `note`,
`t100`, `kind` (`word`/`phrase`, auto-derived). `id`/`tags`/`deck`/`src` are assigned at
load — never hand-write them. **A card's identity is its position in its deck's array —
append new cards at the end; never reorder or delete mid-deck** or review history shifts.

## "Process my inbox" — the weekly enrichment routine
When the learner says **"process my inbox"** (or pastes a batch of words):

1. Read [inbox.md](inbox.md); take each non-empty line below its paste marker. A line is
   Arabic, optionally followed by a rough English meaning (any separator).
2. For each line, produce a full card object `{ar, en, pr, ex:[ar,en], root}` — plus `note`
   only if there's a genuine disambiguation, `t100` only if it's a top-100 function word:
   - `pr`: natural **Egyptian** pronunciation in the app's convention (`3 ' g`), not MSA
     (كتير = *keteer* not *katheer*; قهوة = *'ahwa* not *qahwa*).
   - `root`: the three-letter root, spaced (`"ك ت ب"`). Omit for particles/loanwords with
     no triliteral root rather than forcing one.
   - `ex`: one short, natural Egyptian sentence using the word, with an English translation.
   - Keep `en` a clean gloss; put "also means…/careful…" in `note`.
3. **Dedupe:** skip any word whose `ar` already exists anywhere in `DECKS` (including the
   `mine` deck). Report skips; never silently drop.
4. Append the finished card objects to the **`mine` deck** (`{ id:"mine", … cards:[ … ] }`,
   last deck in the `DECKS` array in `index.html`) — append at the end of its `cards`.
5. Clear the processed lines from `inbox.md` (leave the header + paste marker). If a line is
   too ambiguous to enrich confidently, leave it in `inbox.md` with a `<!-- ? … -->` note
   rather than guessing.
6. Summarise: how many added, how many skipped and why.

Words the learner quick-added offline (via the paste box in the **What to do** tab) live in
`localStorage`; the `mine` deck absorbs them at load. Enriching the same word into the file
supersedes the stub and its review history transfers automatically — so re-processing a word
the learner already quick-added is safe and expected.

## UI shape (keep it calm)
One warm-dark theme (`:root` only — no theme toggle, no light mode), no decorative emoji.
Top bar is exactly four buttons: **Today · Reviews · Hard · What to do**. Topic decks have no
chips — they're reached via Today's rotating focus and the "Browse a topic" list inside the
What-to-do tab. There is no Roots page / Drill / Review-all view (removed). Don't reintroduce
emoji, extra top-bar chips, or a second theme unless asked.

## Verifying changes
Open `index.html` in the browser (it's the working dir, so the preview is live) and check
the console for errors. Note: the in-app preview can serve a **stale `localStorage`
snapshot at script-init**, so to confirm persistence, reload and re-read, or exercise logic
directly in the page console. Run no build/test tooling — there is none.
