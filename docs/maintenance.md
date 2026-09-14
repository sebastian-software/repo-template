# Maintain a repository created from this template

New repositories receive a snapshot of this template. Later template changes
are not copied automatically. Shared configuration evolves through standards
releases; dependency updates come through Renovate.

## File ownership

| Files                                                                                     | How to change them                                                          |
| ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Managed configuration such as `.oxfmtrc.json`                                             | Update standards or run the pinned `standards apply`; avoid manual edits    |
| Standards marker section inside `AGENTS.md`                                               | Regenerate through standards; keep project instructions outside the markers |
| Seeded configuration such as `tsconfig.json`, workflows, package scripts and project docs | Edit and review in this repository                                          |
| `README.md`                                                                               | Edit `README.md.src`, then run `mise run readme:write`                      |
| `.repometa.json`                                                                          | Maintain project metadata; let migrations advance the standards stamp       |

The [standards model](https://github.com/sebastian-software/standards/blob/main/docs/standards-model.md)
explains managed files, seeds, markers and scope detection. Repo-specific
formatting exclusions belong in `.prettierignore`, not managed `.oxfmtrc.json`.

## Local checks

`pnpm agent:check` runs the complete local gate. Use `pnpm check:ci` while
working on code, and `pnpm check:standards` to inspect standards drift. The
README generator has a separate `mise run readme:check` gate.

The standards file check alone does not inspect pending agent work. CI also
checks `.standards/pending.json`, `.standards/blocked.json` and CLI/stamp
alignment. Preserve pending markers until the migration has been completed.

## CI and branch protection

The CI workflow has two independent lanes:

- `CI / Check` runs the code gate.
- `Standards / Consistency` runs the standards guards and file check.

A final `check` job requires both lanes to succeed. It also fails if either is
cancelled or skipped. This keeps older branch protection working during the
transition. To retire it, first require both named lanes in repository settings,
verify a PR reports them, then remove the aggregate in a separate change.
`Generated README` is provided by the README workflow.

The current pinned standards CLI predates the shared `standards ci` command.
Keep the inline guards until a published CLI shipping standards version 16 or
newer is installed. That migration replaces the copied guards with one command;
it must update the exact CLI pin and lockfile before removing them.

## Dependency and standards updates

`renovate.json` opts into both the general policy and the `:standards` preset.
Keep both. The general preset groups routine dependency updates; the standards
preset also discovers the migration stamp and workflow CLI pins.

Two versions have different jobs:

- The exact npm version in `package.json` selects the CLI used locally and in CI.
- `.repometa.json#standards` records the applied migration level.

They can arrive in separate PRs. A migration PR may need to raise the CLI pin
before checking the new stamp. Review the numbered migration instructions and
run all repository gates before merging. If a newer CLI includes new standards,
a CLI-only dependency PR may also need those migration steps.

Follow the [update and recovery guide](https://github.com/sebastian-software/standards/blob/main/docs/cli.md)
for the target CLI version. The resumable sync and read-only preview behavior
documented there require the release shipping standards version 16 or newer.
