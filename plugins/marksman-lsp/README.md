# marksman-lsp

Wires [Marksman](https://github.com/artempyanykh/marksman) — a language server
for Markdown — into Claude Code's built-in LSP integration.

## Prerequisites

Install the `marksman` binary on your host. The plugin does **not** bundle it.

```bash
brew install marksman
# or download a binary from https://github.com/artempyanykh/marksman/releases
```

Verify it is on your `PATH`:

```bash
marksman --version
```

## Install

From inside Claude Code:

```text
/plugin marketplace add michael-denyer/marksman-lsp-cc-plugin
/plugin install marksman-lsp@marksman-lsp-cc-plugin
```

Once installed, Claude Code will launch `marksman server` on stdio for any
`.md` or `.markdown` file you open.

## How it works

The plugin registers an LSP server that points at a small shim script bundled
with the plugin ([`bin/marksman-lsp`](./bin/marksman-lsp)). The shim:

1. Checks whether `marksman` is on `PATH`.
2. If missing, exits with a clear stderr message listing install commands — so
   Claude Code's LSP error surface tells you the fix rather than
   "command not found".
3. Otherwise `exec`s `marksman server`.

Marksman speaks LSP on stdio via its `server` subcommand.

Handles: `.md`, `.markdown`.

## What it gives you

Through Claude Code's LSP tool (navigation-only):

- **`documentSymbol`** — the heading outline of a document.
- **`goToDefinition`** — jump from a heading reference, a `[[wiki-link]]`, or a
  cross-file link to its target.
- **`findReferences`** — every place a heading or file is referenced.
- **`hover`** — preview a link target.

Marksman also emits **diagnostics** for broken internal links and references.
These show up in editors that consume LSP diagnostics (e.g. the Marksman VS
Code extension). Claude Code's LSP *tool* is navigation-only and has no
diagnostics operation, so it surfaces the features above, not lint warnings —
pair Marksman with `markdownlint-cli2` (e.g. in pre-commit) for rule checks.

## License

MIT. See [LICENSE](./LICENSE).
