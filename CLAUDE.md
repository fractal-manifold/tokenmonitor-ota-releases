# ota-releases — how to publish an OTA (for AI assistants)

This is the **public** firmware release channel for C Wall Monitor
(`fractal-manifold/cwm-ota-releases`), vendored in the monorepo as the
`ota-releases/` submodule. The cwm-mcp broker polls it, verifies the manifest
signature, and stages matching devices; the device verifies again and installs
under the rollback safety net. See the monorepo `CLAUDE.md` ("OTA pipeline",
"Broker-driven OTA", "Release channels") for the full design — this file is the
**operational runbook**.

## Golden rules

- **Never hand-edit** `update-<SKU>.json`, the `.bin` assets, or `CHANGELOG.md`
  here. They are generated and pushed by `python -m cwmtools.ota.publish`. The
  copies in the git tree are diff-friendly history; the live artifacts are the
  GitHub **release assets**.
- **One unified `CHANGELOG.md` at the repo root** for both channels (dev entries
  tagged `(dev)`). There is no longer a per-channel `dev/CHANGELOG.md`; only the
  `dev/update-<SKU>.json` index lives under the `dev/` subdir.
- **The signing key is offline.** Publishing signs the manifest locally with
  `firmware/secrets/ota_signing_key.pem`. The broker holds only the *public*
  key. A `.bin` on a public URL is useless without a valid signature.
- **The version is baked into the binary.** `cwmtools.ota.publish` reads the
  `.bin`'s `esp_app_desc.app_version` and treats it as authoritative — the
  manifest `version` is whatever the firmware was built with. If the baked
  version and the manifest disagree, the device's `clear_pending_if_satisfied`
  never clears the pending → re-download loop. Don't fight this; build the
  binary with the exact version you intend to ship.
- All `python -m cwmtools.*` commands run **from the monorepo root** with
  `PYTHONPATH=tools` (or from `tools/` with `PYTHONPATH=.`). Paths below are
  relative to the monorepo root.

## Prerequisites

- ESP-IDF env sourced (`source /opt/esp-idf/export.sh`) for building.
- `gh` authenticated against `fractal-manifold` (publish does `gh release …`).
- `firmware/secrets/ota_signing_key.pem` present. If missing, fetch from GCP
  Secret Manager (project `cwallmonitor`):
  ```sh
  gcloud secrets versions access latest --secret=cwm-ota-signing-key \
      --project=cwallmonitor --out-file=firmware/secrets/ota_signing_key.pem
  ```
- The broker's `cwm.toml` must carry the matching `[[ota.keys]]` pubkey (only
  needed for the auto-discovery path, not for manual staging). Get it with
  `python -m cwmtools.lib.manifest pubkey --key firmware/secrets/ota_signing_key.pem`.

## Channels

| | stable (end users) | dev (development units) |
|---|---|---|
| Version | `X.Y.Z` (no suffix) | `X.Y.Z-dev.<YYYYMMDDhhmm>` |
| GitHub | per-version release `vX.Y.Z`, auto-`latest` | per-version **immutable prerelease** tag `vX.Y.Z-dev.<ts>` (never `latest`, never deleted; newest found via the Releases API) |
| Index URL | `releases/latest/download/update-<SKU>.json` | `releases/download/vX.Y.Z-dev.<ts>/update-<SKU>.json` (no static "latest prerelease" URL) |
| Manifest `channel` | omitted (absent == stable) | `"channel":"dev"` |
| Who installs it | every unit | only units whose serial `FAC=="DEV"` (firmware refuses dev on production) |

Dev version semantics: `X.Y.Z-dev.<ts>` is always **older** than `X.Y.Z` final;
among prereleases the larger 12-digit timestamp wins. So the newest dev build
installs during development, and cutting `X.Y.Z` final graduates over every
`X.Y.Z-dev.*`. The base (`X.Y.Z`) drives the anti-rollback floor; the suffix
only orders builds.

## Changelog & release notes — what to write

`cwmtools.ota.publish` writes ALL the release prose for you; the only thing you
author is one short line passed via `--notes`. Two artifacts are generated from
it:

1. **`CHANGELOG.md`** (unified, repo root) — a `## [version] - <date>` section
   is prepended automatically, tagged `(dev)` for dev builds. Its body is:

   ```
   - Firmware <version> published for SKU(s): <skus>.
     <your --notes text, each line indented two spaces>
   ```

   **Omit `--notes` and the entry has NO description** — just the bare
   "Firmware … published" line (that's why several older entries read blank).
   Always pass `--notes` so the user-facing changelog says what changed.

2. **The GitHub release body** — auto-generated as:

   ```
   C Wall Monitor firmware <version>.

   SKU(s): <skus>.

   <your --notes text>
   ```

   The release **title** is the tag (`vX.Y.Z`, or `vX.Y.Z-dev.<ts> (dev)` for
   dev) and is set automatically. To replace the entire body with a
   hand-written file use `--notes-file PATH` (the CHANGELOG still only picks up
   `--notes`, so pass both if you want a rich body AND a changelog line).

**Brand-text rule:** user-facing prose is always **C Wall Monitor** or **cwm**,
never "Claude Wall Monitor" — the repo is public and must not lean on the Claude
trademark. The auto-generated body already complies; keep any `--notes` /
`--notes-file` text consistent.

**What to write:** one concise, present-tense, *user-facing* sentence about the
impact — not internal refactors. Match the existing style. Real examples:

- stable: `--notes "Device-authoritative display settings: report-back endpoint
  so the broker stops reverting the user's theme/brightness/volume. Promoted
  from dev canary (byte-identical to the soaked 0.7.2 build)."`
- dev: `--notes "First dev-channel iteration toward 0.6.8 (prerelease ordering
  test)."`

When a stable graduates a soaked dev canary, say so and note it's
byte-identical (as 0.7.2 did). Multi-line `--notes` is fine — every line is
indented under the CHANGELOG bullet and copied verbatim into the release body.

## Publish a STABLE release

```sh
# 1. Build with the exact version baked in.
#    Set CWM_VERSION_STRING in firmware/components/core/include/cwm_version.h
#    to "X.Y.Z", then:
source /opt/esp-idf/export.sh
( cd firmware && idf.py build )   # CHECK the real exit status — a trailing
                                  # echo can mask a failed build as exit 0.

# 2. Verify the baked version (defensive):
python3 -c "d=open('firmware/build/cwm_wall_monitor.bin','rb').read(); \
i=d.find(b'\x32\x54\xcd\xab'); print(d[i+16:i+48].split(b'\x00',1)[0].decode())"

# 3. Sign + publish (cuts vX.Y.Z, becomes 'latest'). --notes is the
#    user-facing line for BOTH the changelog and the release body (see
#    "Changelog & release notes"); omit it and the entry has no description.
PYTHONPATH=tools python -m cwmtools.ota.publish \
    --channel stable --version X.Y.Z --sku S1 \
    --bin firmware/build/cwm_wall_monitor.bin \
    --notes "One user-facing sentence describing the change."
```

For stable, `cwm_version.h` **is** committed + git-tagged `vX.Y.Z` in the
monorepo (it's a real release). `--channel stable` refuses a `-dev.*` version.

## Publish a DEV build

```sh
# 1. Bake a dev version TRANSIENTLY (do NOT commit cwm_version.h):
#    CWM_VERSION_STRING "X.Y.Z-dev.<YYYYMMDDhhmm>"   (next stable + timestamp)
( cd firmware && idf.py build )   # verify exit status

# 2. Publish. Pass only the BASE version; publish reads the exact -dev.<ts>
#    from the binary, signs channel:"dev", cuts an IMMUTABLE per-version
#    prerelease tag vX.Y.Z-dev.<ts>. Write the user-facing note with --notes
#    (see "Changelog & release notes" — without it the entry has no description).
PYTHONPATH=tools python -m cwmtools.ota.publish \
    --channel dev --version X.Y.Z --sku S1 \
    --bin firmware/build/cwm_wall_monitor.bin \
    --notes "One user-facing sentence describing the change."

# 3. Restore the source so the timestamp never lands in git:
git checkout firmware/components/core/include/cwm_version.h
```

To iterate, bump the timestamp (a larger `<ts>`), rebuild, publish again — the
broker's `CompareSemver` stages the newer dev over the older one.

**Bootstrap caveat:** firmware built *before* a new manifest grammar/field
(e.g. the `-dev.<ts>` suffix, or the `channel` field) can't parse it. The first
build that introduces it must reach a deployed unit via a plain `X.Y.Z` stable
manifest it already accepts (or USB). Ship the plain stable first, then iterate
dev builds on top.

## Get it onto a device

Two paths — both end at the same on-device gate:

- **Auto-discovery (preferred when the broker runs the new code):** the leader
  broker polls this repo every `[ota].poll_interval_minutes` and stages matching
  devices. Force/preview it now with the `wall_monitor_check_updates` MCP tool
  (`dry_run` to preview). Dev units must be registered with `channel = "dev"`.
- **Manual staging (broker-version-independent):** call
  `wall_monitor_set_device_pending` with `firmware_url`, `firmware_sha256`,
  `firmware_version`, `firmware_manifest_b64`, `firmware_manifest_sig_b64` — read
  those straight from the published `update-<SKU>.json` (the `sha256` lives
  inside the decoded `manifest_b64`). Use this when the running broker daemon
  predates the channel routing.

## Revert a bad dev build (within its 24 h canary window)

A dev unit defers the anti-rollback floor bump for 24 h after confirm, so the
previous version still passes the floor gate. Point
`wall_monitor_revert_firmware` at the **previous** channel index's
`firmware_url`/`firmware_sha256`/`firmware_manifest_b64`/`_sig_b64`/
`firmware_version`. No new wire field. Production units never enter probation —
they roll **forward** only (publish the fix as a higher version; the floor makes
silent downgrade impossible by design).

## After publishing — record the pointer in the monorepo

`publish` pushes this submodule's working tree, but the monorepo's recorded
submodule pointer doesn't move until you bump it:

```sh
git -C <monorepo> add ota-releases
git -C <monorepo> commit -m "chore(ota): record <version> (bump ota-releases pointer)"
git -C <monorepo> push
```

## Gotchas (learned the hard way)

- A USB/esptool flash does **not** bump `cwm_min_sv`, so the broker will keep
  re-staging the same OTA. Use the OTA path, or flash **both** OTA slots and let
  the image self-confirm (flashing only `ota_0` with rollback armed reverts to a
  stale `ota_1`).
- Always confirm the *running* version via the device boot banner / diagnostic
  logs (`wall_monitor_device_logs`), not the registry's `active.firmware_version`
  — the latter advances on config promotion, which can precede the actual reboot.
- `--dry-run` signs + builds the plan without touching git/GitHub; use it to
  sanity-check before a real publish.
