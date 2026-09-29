# plugin-dns

The `dns:` check verb for OpenCharly — a name-resolution probe, compiled into
charly as a host-coupled kit provider (`verb:dns`).

The verb resolves a hostname and optionally asserts the result: in-container
`getent hosts` under `charly check box`, host-side `net.LookupIP` under
`charly check live`, with optional required-address matching. Its `RunVerb` runs
against the live check engine, so it is COMPILED-IN-ONLY.

## What it provides

| Capability | Surface |
|---|---|
| `verb:dns` | the declarative `dns:` check step |

## The verb

An authored `dns: <hostname>` step (scalar sugar) or `dns: {dns: …,
resolvable: …}` (map form). The dns-exclusive fields live in the plugin's own
`#DnsInput` (`schema/dns.cue`); the shared matchers (`exit_status`, `stdout`,
`stderr`) ride the base step op.

| Field | Meaning |
|---|---|
| `dns` | the hostname to resolve (the verb discriminator) |
| `resolvable` | whether the hostname is expected to resolve (default `true`) |
| `addrs` | optional required resolved addresses; a resolved IP must match one |
| `server` | optional advisory resolver hint |

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-dns/candy/plugin-dns:<tag>'
```

Then author the verb in a plan:

```yaml
- check: the service name resolves
  dns: {dns: localhost, resolvable: true}
  context: [runtime]
```

## Layout

- `candy/plugin-dns/` — the plugin module: `plugin.go` (the provider +
  `NewCheckVerb()` / `NewMeta()` / `RunVerb`), `schema/dns.cue` (the
  self-contained `#DnsInput`), `params/cue_types_gen.go`, `plugin_test.go`,
  `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-check:check` — the check verb catalog. This candy
  carries no `skill:` entity of its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-internals:plugin` — the plugin/provider model.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
