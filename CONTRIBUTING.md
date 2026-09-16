# Contributing

## No AI-authored pull requests

Pull requests authored or submitted by AI agents/bots (automated code-generation tools, third-party AI PR services, etc.) are **not accepted** on this repository. All contributions must be authored and submitted by a human contributor who can take responsibility for the change. Human contributors may use AI tools to assist their own work, but the PR must represent their own reviewed, understood contribution — not an unreviewed automated submission.

PRs identified as bot/AI-submitted without a human author taking ownership will be closed without review.

## Content structure

Each screen in a module's `content.json` may have at most one `video`. If a screen's material would need two videos to cover, split it into two screens instead - one topic, one matching video, per screen. This keeps each screen's video directly tied to the content it's on, rather than a screen covering more ground than its single video does.

Each screen also has a stable `id` slug (e.g. `"alt-text"`) - a plain array index isn't safe to depend on since screens get inserted/reordered/split over time, so anything that needs to point at a specific screen references this `id` instead. A quiz question in `questions.json` can set a `screenId` matching one of these to link it back to the lesson screen that taught it - the app uses this for the results page's "Back to Lesson" link, so a question should only set `screenId` when it's actually testing that one screen's material. Not every question needs one, and a topic can have more than one question if it's substantial enough to warrant it (`screenId` doesn't need to be unique across questions).

## Quiz answer pools

Each question in `questions.json` has an `answerPool` array instead of a
fixed choice list - **item 0 is always the correct answer**. The app
draws 4 choices from this pool and shows them in random order each time
someone takes the quiz - so retaking it (or a recertification retake
later) doesn't show the same 4 choices in the same order every time, and
a person can't just memorize "the answer in position C" without knowing
the material.

- Minimum 4 items total (the correct answer plus at least 3 distractors)
  - that's the fewest that lets the app always show 4 choices.
- Target **12 distractors** (13 items total), split into two groups by
  position:
  - **Items 1-4: the close group.** Genuine near-misses - a real common
    mistake, half-right reasoning, or a fact that sounds relevant but
    isn't the actual reason. The app always draws exactly **one** of
    these four, so a real near-miss is guaranteed on every attempt (which
    one varies, so it's not memorizable either).
  - **Items 5-12: the rest.** Still real distractors (never something
    eliminable on sight), just not held to the same "could easily be
    mistaken for correct" bar as the close group. The app draws **two**
    of these eight.
  - A pool with fewer than 6 distractors (no room for a real close/rest
    split - e.g. a module that's still just a placeholder) skips this
    split entirely and the app draws 3 distractors at random from
    whatever's there instead.
- Order *within* each group doesn't matter - only the group boundary
  (item 4 vs. item 5) does. No need to balance where the correct answer
  "usually" falls either, since its position is randomized per attempt,
  not fixed by the JSON.
- Avoid writing a distractor that's actually just a reworded version of
  the correct answer (or of another distractor already in the pool) -
  true for both groups, but especially easy to slip into by accident in
  the close group.

## Take-home materials

A module can optionally offer downloadable supplementary files - a
checklist, a quick-reference sheet, anything worth keeping after the quiz
is over. List them in that module's [manifest.json](manifest.json) entry
as a `resources` array:

```json
"resources": [
  { "name": "MS Word Accessibility Checklist", "file": "resources/ms-word-accessibility-checklist.docx" }
]
```

- `name` is the exact link text shown to the employee.
- `file` is the path to the actual file, relative to that module's own
  folder - put the file itself under a `resources/` subfolder inside the
  module (e.g. `modules/word-accessibility/resources/`), matching how
  `videos/` already works for lesson videos.
- The app resolves `file` into a real download URL itself (same
  `raw.githubusercontent.com` pattern as videos) - don't include the repo
  URL here.
- Omit `resources` entirely for a module with nothing to offer - it's
  optional, not every module needs one.

These show up in two places: the results page once an employee submits
that module's quiz, and the Dashboard's Completed History for anyone who
completed it before.

## Highlighting the correct answer in the lesson text

A screen's `body` markdown can mark the sentence that gives away a
specific question's correct answer, so the app can highlight it when
someone arrives via that question's "Back to Lesson" link after getting
it wrong:

```
{{answer:word-q3}}The alt text should describe the image's purpose, not just its appearance.{{/answer}}
```

- The id inside the marker (`word-q3` above) must match that question's
  `id` in `questions.json` exactly.
- Wrap only the specific sentence (or clause) that actually states the
  correct answer - not the whole paragraph, and not a Do/Don't bullet in
  its entirety unless the bullet *is* that one sentence.
- A screen can hold markers for more than one question if it teaches
  more than one (each question's marker only lights up for that
  question's own "Back to Lesson" link, never all at once).
- This markup is invisible during normal lesson browsing - the app only
  renders the highlight when someone lands on the screen from a specific
  question's review link, and strips the marker to plain text otherwise.
- Every question whose material appears in the lesson body should have
  a marker somewhere in the relevant screen. If a question's answer
  currently has no single sentence that states it outright, add one
  rather than skipping the marker - the lesson text should always
  actually contain the answer, not just imply it.

## Recertification cadence

Each module's `recertDays` in [manifest.json](manifest.json) is how long an employee's completion stays current before the app prompts them to retake it - a rolling window from their own completion date, not a fixed calendar date. Defaults to `365` (one year); set a different value per module if a topic needs a different cadence.

## Minimum passing score

A module can optionally require a minimum score to actually count as
complete, via `passingScore` in [manifest.json](manifest.json) (a whole
number, e.g. `80` for 80%):

- Omit `passingScore` entirely for a module where any completed attempt
  counts, regardless of score - it's optional, matching `recertDays`.
- A completed attempt scoring below `passingScore` is treated as
  immediately due for a retake (not gated behind the normal `recertDays`
  cycle), with the same fixed grace window the app already gives any
  overdue recertification before actually flagging it overdue - see the
  app's own `GRACE_PERIOD_DAYS`.
- Read from the current manifest at the time someone views their
  results/dashboard, not frozen at the moment they completed it - the
  same convention `recertDays`/`resources` already follow, so lowering or
  raising the bar takes effect for everyone immediately, past completions
  included.

## Minimum required app version

If a module's content (or the schema it's authored in - a new
`content.json`/`questions.json`/`manifest.json` field the app needs to
understand) depends on app behavior that only exists from a certain
release onward, set that module's `minAppVersion` in
[manifest.json](manifest.json) to that version (e.g. `"0.9.0"`):

- Omit `minAppVersion` entirely for a module with no such dependency -
  it's optional, matching `recertDays`/`passingScore`/`resources`. Most
  modules never need it.
- An employee running an older Accessibility Training Tool build can't
  start, retake, or Update & Restart onto that module at all - they see
  "Update required" instead, naming the version they need. This is a hard
  block, not a warning, since the whole point is preventing the app from
  processing content it doesn't know how to handle correctly.
- Resuming an already-in-progress attempt is never affected - it keeps
  using whatever version it already started with, which was necessarily
  compatible or it couldn't have started.
- This is a floor on the *app*, not on the *content's own* `version` field
  above it - bump `version` as usual for the actual content/question
  change; `minAppVersion` only needs to change when that change also
  requires newer app code to read it correctly.

## Versioning

Each module has its own `version` field in [manifest.json](manifest.json):

- Any content/question change bumps the module's **patch** version (`0.1.0` -> `0.1.1`).
- A major restructure of a module bumps its **minor** version (`0.1.x` -> `0.2.0`).
- A module's first production-ready release starts at `1.0.0`.

Update `manifest.json` as part of any PR that changes a module.
