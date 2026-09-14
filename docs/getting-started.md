# Set up your new repository

Use this checklist after creating a repository from the template. You should
finish with a project-specific README and a passing first CI run.

## 1. Install and verify

Install Node.js 24 or newer and the pnpm version declared in `package.json`:

```sh
pnpm install --frozen-lockfile
pnpm agent:check
```

This template includes tool configuration, not an application. Its recursive
build/test scripts begin doing application work when you add workspace packages.

## 2. Give the project its identity

Edit these project-owned files:

| File                                                  | What to change                                                                                                       |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `package.json`                                        | `name`, `description` and `repository.url`; keep `private: true` unless this root package is intentionally published |
| `README.md.src`                                       | Title, CI badge URLs, purpose, setup and a runnable example                                                          |
| `.repometa.json`                                      | `visibility` (`oss` or `private`), original `since` year and `platform`; preserve the standards stamp                |
| `SECURITY.md` and `.github/ISSUE_TEMPLATE/config.yml` | Project links and reporting routes                                                                                   |
| `.github/CODEOWNERS`                                  | The team or people who maintain this project                                                                         |

Keep `readme.owner` set to `mdtheme`: this template uses a generated README.
[Install the pinned README tool](readme-theme.md), then run:

```sh
mise run readme:write
mise run readme:check
```

Review and commit `README.md.src` together with `README.md`. Do not edit the
generated file directly. If you changed the copyright metadata, use the pinned
standards CLI to apply the corresponding managed changes and review the diff.

## 3. Add the first package

Create `packages/<name>/package.json` with that package's own build, test and
typecheck scripts. The root workspace pattern already includes `packages/*`.
Choose the runtime and build tool required by your project; the template does
not prescribe or install one. Extend the shared TypeScript configuration where
appropriate, and keep generated build output out of Git.

Documentation can remain plain Markdown in `docs/`. Add a documentation site
only when the project needs one.

## 4. Enable repository automation

The template includes both Renovate presets. To activate the organization's
self-hosted worker, add the `managed-deps` topic and ensure the worker has access
to the repository. Standards migrations additionally need the external agent
and allowed post-upgrade command described in the
[rollout guide](https://github.com/sebastian-software/renovate-config/blob/main/docs/standards-rollout.md).

After the first successful CI run, protect `main` with required checks:

- `CI / Check`
- `Standards / Consistency`
- `Generated README`

The workflow also retains `check` for repositories whose protection already
uses that name. See [the transition](maintenance.md#ci-and-branch-protection)
before removing it.

Apply the shared [repository labels](https://github.com/sebastian-software/standards/blob/main/docs/labels.md).
If the project links to GitHub's private vulnerability reporting, enable that
repository setting and verify its reporting route.

## 5. Validate the first project change

```sh
pnpm agent:check
mise run readme:check
git diff
```

Commit the reviewed changes and open a pull request. If a standards migration
is pending, complete it before expecting the standards CI lane to pass.
