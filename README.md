# Accessibility Training Modules

Training content consumed by the [Accessibility Training](https://github.com/benetech/Accessibility-Training) app. Kept public so the app can fetch modules via `raw.githubusercontent.com` at runtime without authentication.

## Structure

Each module lives in `modules/<module-id>/` and is listed in [manifest.json](manifest.json) with an `id`, `title`, `version`, and `active`. The app compares the manifest's versions against its local cache to decide what to download or update.

A module folder contains:

- `content.json` — instructional screens (optionally with a `video` - at most one per screen, see [CONTRIBUTING.md](CONTRIBUTING.md))
- `questions.json` — quiz questions and answers, optionally linked back to a `content.json` screen via `screenId` (see [CONTRIBUTING.md](CONTRIBUTING.md))

## Current modules

1. Microsoft Word documents (`modules/word-accessibility`)
2. Microsoft PowerPoint presentations (`modules/powerpoint-accessibility`)
3. Microsoft Excel spreadsheets (`modules/excel-accessibility`)

## Adding a module

1. Create `modules/<module-id>/` with content + questions.
2. Add an entry to `manifest.json`, including `"active": true`.
3. Bump the module's `version` on every content change; see [CONTRIBUTING.md](CONTRIBUTING.md) for the project-wide versioning scheme.

## Publishing to specific employees only (`active`, `beta-testers.json`)

A module entry with `"active": false` is hidden from the app's module list for everyone except the emails listed in [beta-testers.json](beta-testers.json) - use this to work on a module (or a content update) without it appearing for every employee yet. Omitting `active` entirely defaults to visible, so it only needs to be set explicitly while something is deliberately held back.

`beta-testers.json` is a flat allow-list (`{"emails": [...]}`) checked against whichever email an employee logged into the app with - not a setting they can toggle themselves. If this file is ever missing or unreachable, the app treats it as an empty list (nobody sees inactive modules) rather than failing open.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). AI-authored/bot pull requests are not accepted.
