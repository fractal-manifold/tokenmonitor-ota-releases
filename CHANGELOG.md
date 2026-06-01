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

## [0.6.0] - 2026-06-01

- Firmware 0.6.0 published for SKU(s): DEV.

