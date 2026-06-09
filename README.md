# C Wall Monitor — OTA release channel

This is the **public** firmware release channel for the
[C Wall Monitor](https://github.com/fractal-manifold) (cwm), an
ESP32‑S3 desk display. It doubles as the user‑facing changelog (see
[`CHANGELOG.md`](CHANGELOG.md)).

## How updates reach a device

The cwm‑mcp broker polls this repo on a schedule. For each hardware SKU it
fetches the stable "latest" asset:

```
https://github.com/fractal-manifold/cwm-ota-releases/releases/latest/download/update-<SKU>.json
```

That `update-<SKU>.json` index looks like:

```json
{
  "version": "0.6.0",
  "manifest_b64": "<canonical OTA manifest, base64>",
  "signature_b64": "<64-byte Ed25519 signature, base64>",
  "bin_url": "https://github.com/fractal-manifold/cwm-ota-releases/releases/download/v0.6.0/cwm-<SKU>-0.6.0.bin"
}
```

The broker base64‑decodes `manifest_b64`, **verifies the Ed25519 signature
over those exact bytes**, parses `sku` / `version` / `sha256` /
`min_secure_version`, and — for every registered device of that SKU running
an older version — stages a pending update pointing at `bin_url`. The device
picks it up on its next control‑plane sync, **verifies the same signature
again on‑device**, downloads the `.bin`, checks the SHA‑256, enforces the
anti‑rollback floor and the SKU gate, then installs it (with automatic
rollback if the new image fails to boot).

## Why public hosting is safe

Trust is anchored in the **Ed25519 signature on the manifest**, not in the
transport or the host. A `.bin` served from an unauthenticated public URL
cannot be installed unless its manifest was signed by the offline OTA
signing key. The broker holds only the **public** verification key; the
private signing key never leaves the maintainer's machine. The firmware
binary itself is additionally protected by Secure Boot v2 on factory units.

## Release channels: stable vs dev

There are two channels, so test builds only ever reach development units:

- **stable** (end users): a normal per‑version release tagged `vX.Y.Z`.
  GitHub serves the newest non‑prerelease as `latest`, which is what the
  `latest/download/update-<SKU>.json` URL above resolves to. Production
  units only ever follow this channel.
- **dev** (development units): each dev build is its own **immutable
  prerelease** tagged `vX.Y.Z-dev.<YYYYMMDDhhmm>` (the canary suffix makes
  every build a distinct tag — releases are never deleted, so every prior dev
  `.bin` stays downloadable, which is what lets `revert_firmware` point back at
  an earlier one). Because it's marked **prerelease**, GitHub never serves it
  as `latest`, so a stable device can't fetch it. There is **no** "latest
  prerelease" redirect: the broker discovers the newest dev build via the
  GitHub Releases API and fetches that tag's asset:

  ```
  https://github.com/fractal-manifold/cwm-ota-releases/releases/download/vX.Y.Z-dev.<ts>/update-<SKU>.json
  ```

  The dev manifest additionally carries `"channel":"dev"`, and the firmware
  **refuses** it on a production unit (only units whose serial FAC == `DEV`
  accept it). The per-SKU dev index is also committed under `dev/` in this repo
  for diff-friendly history.

Publish with `--channel dev`; dev units must be registered with
`channel = "dev"` in the broker. Reverting a bad dev build (within its
canary window) uses `wall_monitor_revert_firmware` against the previous
index — see the monorepo CLAUDE.md "Release channels + canary".

### Dev version naming

Dev builds toward the next stable carry a SemVer prerelease suffix
`<next-stable>-dev.<YYYYMMDDhhmm>`. While developing 0.6.8 you publish
`0.6.8-dev.202606021930`, then `0.6.8-dev.202606022100`, and so on. Under
SemVer `X.Y.Z-dev.<ts>` is always *older* than `X.Y.Z` final, and among
prereleases the larger timestamp wins — so the newest dev build installs
during development, and cutting `0.6.8` (no suffix) graduates over every
`0.6.8-dev.*`. The version's base (0.6.8) drives the anti‑rollback floor;
the `-dev.<ts>` suffix only orders builds. `cwmtools.ota.publish --channel
dev --version 0.6.8` reads the exact `-dev.<ts>` from the firmware binary
(it's baked at build time), so you pass only the base. A `-dev.<ts>` build
is **only** valid on the dev channel; the firmware refuses a prerelease
version that isn't marked `channel:"dev"`.

## Layout

- `CHANGELOG.md` — single human‑readable history for BOTH channels (dev
  entries tagged `(dev)`). There is no separate `dev/CHANGELOG.md`.
- `update-<SKU>.json` — the per‑SKU stable index (root); `dev/update-<SKU>.json`
  for dev. Each is also attached to its release as an asset; the copy in the
  repo tree is for diff‑friendly history.
- Firmware `.bin` files ship **only** as GitHub release assets, never in the
  git tree.

Releases are published manually with `python -m cwmtools.ota.publish` from
the monorepo, which signs the manifest locally and uploads the assets here.
