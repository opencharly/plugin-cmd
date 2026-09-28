# plugin-cmd

The `charly cmd` command for OpenCharly — run a single command in a running
container with an optional desktop notification on completion. Served as a
charly `command:cmd` plugin (compiled-in).

## What it provides

| Capability | Surface |
|---|---|
| `command:cmd` | the `charly cmd <box> <command>` CLI — a single container exec with an optional `--notify` |

## What it owns

The plugin owns the command end to end: the kong grammar, the container resolve
(`deploykit.ResolveContainer` / `ResolveSidecarContainer`), and `--notify` (the
venue's gdbus session-bus call, driven directly on the deploykit container-chain
executor). The one thing it cannot do is the interactive exec, which is
deploy-lifecycle machinery — it dispatches the `op="cmd"` pod-lifecycle action
over the reverse channel with inherited stdio, so the `-i` interactive stream
reaches the operator.

`cmd` is compiled-in because its `Invoke(OpRun)` needs the in-process reverse
channel; the out-of-process path has no reverse channel and errors.

## How to use it

Compose the plugin candy in a box's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-cmd/candy/plugin-cmd:<tag>'
```

## Layout

- `candy/plugin-cmd/` — the plugin module: `plugin.go`, `provider.go`,
  `command.go`, `notify.go`, `schema/cmd.cue`, `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-core:cmd` — the `charly cmd` reference.
- `/charly-internals:plugin` — the plugin/provider model, including the
  `command` provider class.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
