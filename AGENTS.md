# AGENTS.md — plugin-cmd

Standalone plugin repo owning the externalized `charly cmd` command
(`command:cmd`, compiled-in). The plugin is a Go module at
`candy/plugin-cmd/` (module path
`github.com/opencharly/plugin-cmd/candy/plugin-cmd`); the root `charly.yml` only
declares `discover: candy` so the repo is a project and its candy is scanned.

Canonical files:

- `candy/plugin-cmd/charly.yml` — the `plugin-cmd:` candy entity
  (`plugin:` block, `plan:` check).
- `candy/plugin-cmd/plugin.go` / `provider.go` — `NewProvider()` / `NewMeta()` /
  `CliMain` and the `Invoke(OpRun)` surface.
- `candy/plugin-cmd/command.go` — the `charly cmd` kong grammar + container
  resolve.
- `candy/plugin-cmd/notify.go` — the `--notify` gdbus completion notification.
- `candy/plugin-cmd/schema/cmd.cue` — the self-contained plugin schema.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-core:cmd` — the `charly cmd` reference (single container exec with
  notification). Load before changing the command grammar.
- `/charly-internals:plugin` — the plugin authoring reference: the `plugin:`
  block, the `command` provider class, the per-plugin CUE-schema contract.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `go build ./...` in `candy/plugin-cmd/` — compile the plugin module.
- `go test ./...` in `candy/plugin-cmd/` — the plugin's Go tests (the schema-serve
  seam).
- `charly box validate` at the repo root — the structural check (the candy +
  `plugin:` block, CUE schema).
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate.
- The R10 witness is the disposable `check-commands-local` bed (in
  `opencharly/charly`), which exercises `charly cmd` against a running
  deployment.

## Modify this repo

- Edit the `plugin-cmd:` candy entity, the Go source, and `schema/cmd.cue`
  **together** — the schema is the single source for the plugin's served
  declaration surface.
- The interactive exec is deploy-lifecycle machinery: dispatch it as the
  `op="cmd"` pod-lifecycle action over the reverse channel; never re-introduce a
  hidden core reentry.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
