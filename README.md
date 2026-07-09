# marksman-lsp-cc-plugin

A Claude Code plugin marketplace that ships
[Marksman](https://github.com/artempyanykh/marksman) — a language server for
Markdown — as a drop-in LSP for Claude Code.

## Plugins

| Plugin | Description |
|--------|-------------|
| [`marksman-lsp`](./plugins/marksman-lsp/) | Registers `marksman server` as the Markdown language server for `.md` / `.markdown` files. |

## Install

```text
/plugin marketplace add michael-denyer/marksman-lsp-cc-plugin
/plugin install marksman-lsp@marksman-lsp-cc-plugin
```

You must install the `marksman` binary yourself — see the plugin
[README](./plugins/marksman-lsp/README.md) for details.

## What you get

Markdown **navigation** through Claude Code's LSP tool: document outline,
go-to-definition on headings, `[[wiki-links]]`, and cross-file links,
find-references, and hover. Marksman does not feed lint warnings to Claude
Code's LSP tool (it has no diagnostics operation) — pair it with
`markdownlint-cli2` in pre-commit for rule checks.

## Releases

Versioning is automated via
[Release Please](https://github.com/googleapis/release-please). Commits on
`main` follow the [Conventional Commits](https://www.conventionalcommits.org/)
spec:

- `feat: …` → minor bump
- `fix: …` → patch bump
- `feat!: …` or a `BREAKING CHANGE:` footer → major bump
- `chore: …`, `docs: …`, `ci: …` → no release

Release Please watches `main` and opens a *"chore: release x.y.z"* PR that
accumulates unreleased changes. Merging that PR tags the release, publishes a
GitHub Release, updates [`CHANGELOG.md`](./CHANGELOG.md), and propagates the
version into `plugins[0].version` in
[`.claude-plugin/marketplace.json`](./.claude-plugin/marketplace.json).

## License

MIT. See [LICENSE](./LICENSE).
