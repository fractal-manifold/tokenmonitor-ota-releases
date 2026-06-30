# Changelog

All notable firmware releases for the TokenMonitor are documented
here. Each entry corresponds to a signed GitHub release whose assets the
tokenmonitor-mcp broker discovers and stages automatically. Both channels share this
one chronological history; **dev** (prerelease / canary) entries are tagged
`(dev)`, **stable** entries are not.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com);
versions follow [Semantic Versioning](https://semver.org) (`MAJOR.MINOR.PATCH`,
the same packed 8.8.16 value the firmware uses for its anti-rollback floor).
Dev canaries carry a `-dev.<YYYYMMDDhhmm>` prerelease suffix.

## [0.9.4] - 2026-07-01

- Replace the Gemini provider card with Antigravity (agy): the third usage card now tracks Antigravity quota and spend.

## [0.9.3] - 2026-06-30

- WiFi credential integrity (atomic save, self-heal, portal fallback, WPA3/PMF), spend-screen Day theme fix, settings/provisioning polish.

<!-- Releases before 0.9.3 (pre-rebrand "cwm" builds, 0.6.0–0.8.2) were
     removed during the TokenMonitor rebrand cleanup; 0.9.3 is the first
     release under the TokenMonitor brand kept in this repo. -->
