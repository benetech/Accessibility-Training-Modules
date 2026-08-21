# Accessibility Training Modules

Training content consumed by the [Accessibility Training](https://github.com/benetech/Accessibility-Training) app. Kept public so the app can fetch modules via `raw.githubusercontent.com` at runtime without authentication.

## Structure

Each module lives in `modules/<module-id>/` and is listed in [manifest.json](manifest.json) with an `id`, `title`, and `version`. The app compares the manifest's versions against its local cache to decide what to download or update.

A module folder contains:

- `content.md` (or `content.json`) — instructional pages
- `questions.json` — quiz questions and answers

## Current modules

1. Microsoft Word documents (`modules/word-accessibility`)

## Adding a module

1. Create `modules/<module-id>/` with content + questions.
2. Add an entry to `manifest.json`.
3. Bump the module's `version` on every content change; see [CONTRIBUTING.md](CONTRIBUTING.md) for the project-wide versioning scheme.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). AI-authored/bot pull requests are not accepted.
