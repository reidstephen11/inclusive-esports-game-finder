# Inclusive Esports Game Finder

A single-file web app that helps educators choose the right esports game for an inclusive
school program. Built as the practical activity for the QLD DoE IT Conference session on
Para Esports.

**All the code lives in `index.html`**, with game key art in `images/`. No frameworks, no build
step, no backend, no requests to anyone else's server. Double-click the file to run it, or drop
the two items on any static host.

## Running it

- **Locally:** open `index.html` in any modern browser. Works fully offline.
- **On a USB stick / shared drive:** copy `index.html` and the `images` folder together.
  `index.html` on its own still runs — every card just falls back to its illustration.
- **On GitHub Pages:** https://reidstephen11.github.io/inclusive-esports-game-finder/ 

## Department position on game titles

Every card is flagged against the Queensland Department of Education's assessed game list:

- **✅ Permitted for use** — assessed and permitted, subject to local decision making.
- **📋 Not yet assessed** — not on the list. Several of the strongest inclusive titles sit here
  (Minecraft, Overcooked! 2, Moving Out). Self-assess them with the flowchart in the
  [Esports in Schools QLearn course](https://catalogue.qlearn.eq.edu.au/browse/education-futures-institute/courses/esports-in-schools-supporting-the-implementation-of-school-esports-programs).
- **⛔ Not permitted** — Call of Duty, Counter-Strike 2, Rainbow Six. Excluded from the database
  and never recommended, regardless of settings.

A **Department-permitted only** toggle on the results and All games screens is **on by default**,
restricting everything to assessed titles (7 of the 21 games qualify). Turn it off to include
not-yet-assessed titles; the choice is remembered per device. Searching for a permitted title that is not in the
database — Fortnite, VALORANT, League of Legends — returns its status with an explanation, since
those are a poor fit for inclusive settings and are deliberately not included.

Status is derived at load time by exact title match against `DEPT_PERMITTED` and
`DEPT_NOT_PERMITTED` near the top of the script. Update those two arrays when the department
publishes a revision and every card, filter and recommendation re-flags itself. `DEPT_NOTES` holds
per-game caveats (for example: FIFA 23 is permitted, but its successor EA Sports FC is not
separately listed).

## What it does

- **Finder** — seven questions (goal, student needs, platforms, budget, group size, style,
  event type), then scores every game and returns a best match, excellent alternatives and
  honourable mentions, with the reasons it fits *those* answers.
- **All games** — the full database with search and platform/style filters.
- **Compare** — two games side by side.
- **Saved** — favourites kept in `localStorage` (this browser only, nothing uploaded).
- Dark mode, print / save-as-PDF of recommendations, random "discover a game", keyboard
  arrow navigation through the wizard.

## How the scoring works

Nothing is filtered out. Every game carries 0–5 scores against each criterion, and the
weights are set in `WEIGHTS`:

| Criterion | Weight | Why |
|---|---|---|
| Platform | 4 | A perfect game you cannot run is not a recommendation |
| Primary goal | 3 | Drives what the session is actually for |
| Student needs | 3 | Drives whether students can take part at all |
| Budget, group size, style, event type | 2 each | Practical fit |
| Practicality (offline play) | 1 | School networks fail on the day |

The total is normalised to a percentage match.

## Adding or editing a game

Each entry in the `GAMES` array near the top of the `<script>` block is a plain object.
Copy an existing block, keep every key, and adjust:

- `goals`, `needs`, `playerFit`, `events` — 0–5 scores against each wizard option
- `platforms` — any of `switch`, `pc`, `xbox`, `playstation`
- `styles` — must match the style options in `STEPS`
- `cost` — approximate AUD (0 = free to play); `costLabel` is the text shown on the card
- `features` — accessibility options to switch on
- `settings` — the configuration to use on the day
- `c1` / `c2` — the two gradient colours for the card artwork
- `art` — which illustrated scene to draw underneath (see below)
- `image` — path to the game's key art, e.g. `images/mk8.jpg`; omit it to show only the illustration

Do **not** set a department status by hand — it is derived from the title. If the title matches an
entry in `DEPT_PERMITTED` exactly, the game is flagged permitted automatically.

## Artwork and icons

Each card draws its art in two layers, so it always looks like something even if the images go
missing.

- **Key art** — `images/<id>.jpg`, one per game, referenced by the `image` field. Publisher
  promotional art collected from the Nintendo eShop, PlayStation and Steam store pages and stored
  locally, so nothing is fetched over the network at run time. A blurred copy fills the strip and a
  sharp copy sits inside it, uncropped, which is why mixed image shapes still line up.
- **Illustrations** — the fallback layer underneath. 18 original vector scenes (`track`, `circuit`,
  `blocks`, `kitchen`, `rhythm`, `brick`, `crew`, `clash`, `wobble`, `nature`, `cake`, `loot`, …)
  live in `MOTIFS`, tinted with the game's own `c1`/`c2` colours and jittered by a hash of the game
  id so two games sharing a scene don't look cloned. Set `art:"..."` to pick one, or add a function
  to `MOTIFS`.
- **Removing the key art** — delete the `images` folder, or drop the `image` field from a game.
  `gameArt()` catches the failed load and the illustration shows through. Nothing else changes.
- **Licensing** — the key art belongs to the respective publishers and is used to identify each
  game. The permission to copy and adapt this tool covers the code and the illustrations, not those
  images. Swap in your own files at the same paths if your school would rather not redistribute
  them.
- **UI icons** are line icons on a 24×24 grid in the `ICONS` object, rendered by `icon(name, size)`
  and inheriting the surrounding text colour. Static markup uses `<span data-icon="name">`, filled in
  at boot.

## Notes

Prices are indicative Australian RRP and change often. Age guidance is a classroom
recommendation, not a formal classification — check the rating and your school's device,
network and purchasing policies before committing.

Free to copy and adapt for your own school.
