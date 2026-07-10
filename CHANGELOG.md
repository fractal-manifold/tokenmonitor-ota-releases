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

## [0.9.16] - 2026-07-10

- Unified dev/prod firmware on a single stable OTA channel. Fix: tcpip task stack overflow (3072->5120) that crash-looped SoftAP captive-portal provisioning when a phone's mDNS/DNS-SD burst hit during DHCP. Disable the app-startup Flash-Encryption tripwire (SECURE_FLASH_CHECK_ENC_EN_IN_APP=n) so the same signed image also boots trusted un-burned dev units.

## [0.9.15] - 2026-07-08

- Steadier LCD refresh that eliminates the scroll-time image shift and flicker on 4-inch units.

## [0.9.11] - 2026-07-07

- Fix NVS encryption key-protection scheme so flash-encrypted production units boot instead of crash-looping; factory burn tooling now verifies in Secure Download Mode.

## [0.9.9] - 2026-07-04

- First production-ready release: Secure Boot v2 signed image; provider footer dot hides when disabled at the broker; OTA proceeds on USB power or at 60%+ battery.

## [0.9.8-dev.202607041612] - 2026-07-04 (dev)

- Footer dot hidden for broker-disabled providers; OTA power gate opens on USB or >=60% battery (either).

## [0.9.8-dev.202607041608] - 2026-07-04 (dev)

- Footer dot hidden for broker-disabled providers; OTA power gate opens on USB or >=60% battery (either).

## [0.9.8] - 2026-07-03

- Custom panel: a swipe-up screen for your own broker-fed charts and tables (line/bar/pie/table/text, up to 4 tiles), rendered in a neutral grayscale theme so it is not tinted by the active provider. Reliability: OTA updates survive user-initiated reboots and retry failed downloads; provider polling no longer freezes on long uptimes. Antigravity quota errors now show "usage unavailable" instead of a fake 0%. New banner when your tokenmonitor-mcp broker is out of date.

## [0.9.7-dev.202607022355] - 2026-07-03 (dev)

- Custom panel: real multi-tile layout — 1 full-screen, 2 stacked, 3-4 in a 2x2 grid (compact tiles). Fix: 'Current' session card could stay hidden after a reauth/degraded episode until a reboot.

## [0.9.6-dev.202607020058] - 2026-07-02 (dev)

- Reliability round: OTA updates now survive user-initiated reboots and retry failed downloads, provider polling no longer freezes on long uptimes, and Antigravity quota errors show as 'usage unavailable' instead of a fake 0%.

## [0.9.5-dev.202607011836] - 2026-07-01 (dev)

- Broker version advisory: the device now shows a banner and a Settings line when the tokenmonitor-mcp broker on your computer is out of date.

## [0.9.4-dev.202607011430] - 2026-07-01 (dev)

- 0.9.4 dev canary for unit 02c4777c

## [0.9.4] - 2026-07-01

- Replace the Gemini provider card with Antigravity (agy): the third usage card now tracks Antigravity quota and spend.

## [0.9.3] - 2026-06-30

- WiFi credential integrity (atomic save, self-heal, portal fallback, WPA3/PMF), spend-screen Day theme fix, settings/provisioning polish.

<!-- Releases before 0.9.3 (pre-rebrand "cwm" builds, 0.6.0–0.8.2) were
     removed during the TokenMonitor rebrand cleanup; 0.9.3 is the first
     release under the TokenMonitor brand kept in this repo. -->
