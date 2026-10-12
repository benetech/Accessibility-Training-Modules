# Accessibility Training Modules

Training content consumed by the [Accessibility Training](https://github.com/benetech/Accessibility-Training) app. Kept public so the app can fetch modules via `raw.githubusercontent.com` at runtime without authentication.

## Structure

Each module lives in `modules/<module-id>/` and is listed in [manifest.json](manifest.json) with an `id`, `title`, `version`, `active`, and `recertDays`. The app compares the manifest's versions against its local cache to decide what to download or update, and uses `recertDays` to prompt a retake once that many days have passed since an employee's last completion (see [CONTRIBUTING.md](CONTRIBUTING.md)).

A module folder contains:

- `content.json` — instructional screens (optionally with a `video` - at most one per screen, see [CONTRIBUTING.md](CONTRIBUTING.md))
- `questions.json` — quiz questions and answers, optionally linked back to a `content.json` screen via `screenId` (see [CONTRIBUTING.md](CONTRIBUTING.md))

## Current modules

1. Microsoft Word documents - Basic (required, `modules/word-accessibility`) and Advanced (optional, `modules/word-accessibility-advanced`)
2. Microsoft PowerPoint presentations (`modules/powerpoint-accessibility`)
3. Microsoft Excel spreadsheets (`modules/excel-accessibility`)

## Adding a module

1. Create `modules/<module-id>/` with content + questions.
2. Add an entry to `manifest.json`, including `"active": true`.
3. Bump the module's `version` on every content change; see [CONTRIBUTING.md](CONTRIBUTING.md) for the project-wide versioning scheme.

## Publishing to specific employees only (`active`, `beta-testers.json`)

A module entry with `"active": false` is hidden from the app's module list for everyone except the emails listed in [beta-testers.json](beta-testers.json) - use this to work on a module (or a content update) without it appearing for every employee yet. Omitting `active` entirely defaults to visible, so it only needs to be set explicitly while something is deliberately held back.

`beta-testers.json` maps each module id to its own allow-list (`{"<module-id>": ["email", ...]}`), checked against whichever email an employee logged into the app with - not a setting they can toggle themselves. Being a beta tester for one module doesn't grant access to any other inactive module; add the same email to more than one module's list if they need to see several. If this file is ever missing or unreachable, or a module has no entry at all, the app treats it as an empty list for that module (nobody sees it) rather than failing open.

## Required vs. optional (Advanced) modules (`optional`)

Every module is required of every employee by default: it shows under "Modules In Need of Taking", has a renewal period (`recertDays`), and counts toward the admin Dashboard's Who's Overdue report. A module entry with `"optional": true` is an **Advanced** module nobody is required to take - it's assigned by simply telling someone to go take it:

- It appears in the app's "Browse All Modules" list under an **Advanced Optional Modules** heading, below the required ones.
- It has no renewal date or deadline of any kind (`recertDays` is ignored), is never "in need of taking," and is never listed as overdue.
- `passingScore` and `resources` still work normally - a below-passing attempt is reported as failed and doesn't unlock take-home materials, it just has no retake deadline.
- Omitting `optional` entirely keeps the module required, so it only needs to be set on Advanced modules.
- Requires Accessibility Training Tool v0.9.47 or later. Set the module's `minAppVersion` to `0.9.47` when marking it optional, since an older build would otherwise show it as required.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). AI-authored/bot pull requests are not accepted.
