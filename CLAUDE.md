# CLAUDE.md

Context for Claude Code sessions working on this repo.

## What this is

A Claude Code plugin *marketplace* (not a single plugin) that ships one
plugin: `marksman-lsp`. It wires
[Marksman](https://github.com/artempyanykh/marksman) — a Markdown language
server — into Claude Code's built-in LSP integration, giving Claude document
outline, heading / wiki-link / cross-file navigation, find-references, and
hover for `.md` / `.markdown` files.

Sibling repo, same shape: `pyrefly-lsp-cc-plugin`.

## Layout (what lives where)

- [.claude-plugin/marketplace.json](./.claude-plugin/marketplace.json) —
  the only artefact CC actually reads. `plugins[0].version` is the
  authoritative version string (bumped automatically by Release Please).
  `"strict": false` is **load-bearing**: the plugin has no `plugin.json`, so
  the marketplace entry IS the plugin definition (including the `lspServers`
  block). CC's default is `strict: true`, which would require a `plugin.json`
  — do not remove the field.
- [plugins/marksman-lsp/bin/marksman-lsp](./plugins/marksman-lsp/bin/marksman-lsp) —
  POSIX `sh` preflight shim. Checks `marksman` is on PATH; if not, prints
  install hints to stderr and exits 127. `exec`s `marksman server` on the
  happy path.
- [plugins/marksman-lsp/README.md](./plugins/marksman-lsp/README.md) —
  end-user install docs. The top-level [README.md](./README.md) is the
  marketplace intro.

## Non-goals (keep rejecting these)

- No bundled `marksman` binary. Users install it (`brew install marksman`).
- No skills, agents, commands, or hooks. Pure LSP wiring.
- No tests or lint CI. The only "code" is a short `sh` script and JSON.
  `release-please` is the only workflow.

If a request would add any of the above, push back before implementing.

## Diagnostics caveat

Marksman emits diagnostics for broken internal links, but Claude Code's LSP
tool is navigation-only (no diagnostics operation), so Claude never sees them.
Those diagnostics only surface in editors that consume LSP diagnostics (e.g.
the Marksman VS Code extension). Do not describe this plugin as giving Claude
markdown lint/broken-link warnings — it gives navigation.

## Releases

Automated via [Release Please](https://github.com/googleapis/release-please).

- Commit messages on `main` must follow Conventional Commits to trigger a
  bump: `feat:` (minor), `fix:` (patch), `feat!:` / `BREAKING CHANGE:`
  (major). `chore:` / `docs:` / `ci:` do not release.
- Release Please opens an accumulating "chore: release x.y.z" PR. Merging it
  tags, creates a GitHub Release, writes `CHANGELOG.md`, and propagates the
  version into `plugins[0].version` via the JSONPath updater in
  [release-please-config.json](./release-please-config.json).
- Current version tracked in
  [.release-please-manifest.json](./.release-please-manifest.json).
- The action is pinned by SHA (see
  [.github/workflows/release-please.yml](./.github/workflows/release-please.yml));
  Dependabot updates it weekly.
- **Never hand-edit** `plugins[0].version` — Release Please owns that field.

## The shim

When touching [plugins/marksman-lsp/bin/marksman-lsp](./plugins/marksman-lsp/bin/marksman-lsp):

- It's `/usr/bin/env sh` (POSIX). No bash-isms (`[[`, arrays, `pipefail`) —
  must work on every end-user's system.
- The happy path `exec`s so we don't leave a parent process hanging.
- Test locally with `PATH=/usr/bin:/bin ./plugins/marksman-lsp/bin/marksman-lsp`
  to simulate the missing-binary case; expect exit 127 and the install hints
  on stderr.
- It must stay executable (`chmod +x`). Git tracks the mode — don't
  accidentally drop it.

## Install path resolution

The `command` in `marketplace.json` uses `${CLAUDE_PLUGIN_ROOT}` — CC
substitutes this at runtime to the absolute install path. A bare `./bin/...`
relative path will NOT work; the `command` field either needs to be on PATH or
use `${CLAUDE_PLUGIN_ROOT}`.
