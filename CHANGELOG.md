# Changelog

All notable firmware releases for the Claude Wall Monitor are documented
here. Each entry corresponds to a signed GitHub release whose assets the
cwm-mcp broker discovers and stages automatically.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com);
versions follow [Semantic Versioning](https://semver.org) (`MAJOR.MINOR.PATCH`,
the same packed 8.8.16 value the firmware uses for its anti-rollback floor).

## [Unreleased]

- Release channel bootstrapped. No firmware published yet — the first
  `python -m cwmtools.ota.publish` run will append an entry above this line
  and attach the `cwm-<SKU>-<version>.bin` + `update-<SKU>.json` assets.

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
