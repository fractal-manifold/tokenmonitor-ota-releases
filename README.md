# Claude Wall Monitor — OTA release channel

This is the **public** firmware release channel for the
[Claude Wall Monitor](https://github.com/fractal-manifold) (cwm), an
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

## Layout

- `CHANGELOG.md` — human‑readable release notes.
- `update-<SKU>.json` — the per‑SKU index (also attached to each release as
  an asset; the copy in the repo tree is for diff‑friendly history).
- Firmware `.bin` files ship **only** as GitHub release assets, never in the
  git tree.

Releases are published manually with `python -m cwmtools.ota.publish` from
the monorepo, which signs the manifest locally and uploads the assets here.
