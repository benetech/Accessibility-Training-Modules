# Contributing

## No AI-authored pull requests

Pull requests authored or submitted by AI agents/bots (automated code-generation tools, third-party AI PR services, etc.) are **not accepted** on this repository. All contributions must be authored and submitted by a human contributor who can take responsibility for the change. Human contributors may use AI tools to assist their own work, but the PR must represent their own reviewed, understood contribution — not an unreviewed automated submission.

PRs identified as bot/AI-submitted without a human author taking ownership will be closed without review.

## Content structure

Each screen in a module's `content.json` may have at most one `video`. If a screen's material would need two videos to cover, split it into two screens instead - one topic, one matching video, per screen. This keeps each screen's video directly tied to the content it's on, rather than a screen covering more ground than its single video does.

## Versioning

Each module has its own `version` field in [manifest.json](manifest.json):

- Any content/question change bumps the module's **patch** version (`0.1.0` -> `0.1.1`).
- A major restructure of a module bumps its **minor** version (`0.1.x` -> `0.2.0`).
- A module's first production-ready release starts at `1.0.0`.

Update `manifest.json` as part of any PR that changes a module.
