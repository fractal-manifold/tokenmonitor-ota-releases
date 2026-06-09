# Changelog

All notable firmware releases for the C Wall Monitor are documented
here. Each entry corresponds to a signed GitHub release whose assets the
cwm-mcp broker discovers and stages automatically. Both channels share this
one chronological history; **dev** (prerelease / canary) entries are tagged
`(dev)`, **stable** entries are not.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com);
versions follow [Semantic Versioning](https://semver.org) (`MAJOR.MINOR.PATCH`,
the same packed 8.8.16 value the firmware uses for its anti-rollback floor).
Dev canaries carry a `-dev.<YYYYMMDDhhmm>` prerelease suffix.

## [0.8.0-dev.202606091938] - 2026-06-09 (dev)

- Firmware 0.8.0-dev.202606091938 published for SKU(s): S1.
  Pet mascot: dashboard tamagotchi that reacts to your token usage; the pet screen now re-themes live on Day / Night / Auto.

## [0.7.3-dev.202606090022] - 2026-06-09 (dev)

- Firmware 0.7.3-dev.202606090022 published for SKU(s): S1.

## [0.7.2] - 2026-06-08

- Firmware 0.7.2 published for SKU(s): S1.
  Device-authoritative display settings: report-back endpoint so the broker stops reverting the user's theme/brightness/volume/autorotate. Promoted from dev canary (byte-identical to the soaked 0.7.2 build).

## [0.7.2] - 2026-06-08 (dev)

- Firmware 0.7.2 published for SKU(s): S1.
  Device-authoritative display settings: report-back endpoint so the broker stops reverting the user's theme/brightness/volume/autorotate.

## [0.7.1] - 2026-06-08 (dev)

- Firmware 0.7.1 published for SKU(s): S1.

## [0.6.9-dev.202606031133] - 2026-06-03 (dev)

- Firmware 0.6.9-dev.202606031133 published for SKU(s): S1.

## [0.6.8] - 2026-06-03

- Firmware 0.6.8 published for SKU(s): S1.

## [0.6.8-dev.202606030731] - 2026-06-03 (dev)

- Firmware 0.6.8-dev.202606030731 published for SKU(s): S1.

## [0.6.8-dev.202606030727] - 2026-06-03 (dev)

- Firmware 0.6.8-dev.202606030727 published for SKU(s): S1.
  First dev-channel iteration toward 0.6.8 (prerelease ordering test).

## [0.6.7] - 2026-06-03

- Firmware 0.6.7 published for SKU(s): S1.
  Prerelease-aware build (parses -dev.<ts> dev versions). Bootstrap for the dev-channel iteration toward 0.6.8.

## [0.6.6] - 2026-06-02

- Firmware 0.6.6 published for SKU(s): S1.
  Release channels (stable/dev) + dev canary; TweetNaCl Ed25519 verify.

## [0.6.5] - 2026-06-02

- Firmware 0.6.5 published for SKU(s): S1.
  OTA verification test build — exercises the on-device TweetNaCl Ed25519 manifest verify end-to-end over the air. No functional changes vs 0.6.4.

## [0.6.4] - 2026-06-02

- Firmware 0.6.4 published for SKU(s): S1.
  Enforce signed OTA on dev builds (real Ed25519 verify via vendored TweetNaCl; IDF mbedtls 4.0 ships no EdDSA). Fix dashboard %-tearing and Claude night status colour. OTA boot-timing/clear_pending/tx-buffer fixes.

## [0.6.1] - 2026-06-02

- Firmware 0.6.1 published for SKU(s): S1.
  Fix stale 'Server unavailable' toast lingering on the dashboard; reflow loading splash so the error banner no longer overlaps the ambient strip; faster cold-boot reconnect; English UI strings.

## [0.6.0] - 2026-06-01

- Firmware 0.6.0 published for SKU(s): S1, DEV.
  Rebuild: esp_app_desc app_version now correctly reports 0.6.0 (prior 0.6.0 asset self-reported 0.5.8).

## [0.6.0] - 2026-06-01

- Firmware 0.6.0 published for SKU(s): DEV, S1.

## [0.6.0] - 2026-06-01

- Firmware 0.6.0 published for SKU(s): DEV.
