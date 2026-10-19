# Router compatibility

Updated 19 October 2026. This guide describes the Windows app and its MikroTik network adapter.

## Separate support levels

| Level | Meaning |
| --- | --- |
| Setup | An authorized API connection returns an authenticated model and RouterOS version. |
| Monitoring | The account can read applicable client, interface and traffic information. Missing menus/permissions can result in partial information. |
| Controls | A matching control profile and household checks permit individual actions. Login or a RouterOS 7 version alone is insufficient. |
| Hardware acceptance | Behavior has been observed on the actual model/build, including restoration and effects on other clients. Fixtures do not establish this. |

## Current matrix

| Device / software | Setup and reads | Controls | Evidence boundary |
| --- | --- | --- | --- |
| hEX S (2025), E60iUGS, RouterOS 7.24.5 stable | Available | Primary target | Household reports confirm block/unblock and speed controls; full dual-stack/recovery/load acceptance remains pending. |
| Earlier hEX S, RB760iGS, RouterOS 7 | Eligible | Recognized by the existing control profile | Not independently tested on this hardware revision in the current deployment. |
| Regular hEX and other RB models, RouterOS 7 | Best effort | Unverified; disabled by the generic profile | Similar names do not establish hEX S compatibility. |
| hAP lite, hAP, cAP, CRS, CCR, CHR and other named RouterOS 7 models | Best effort | Unverified; disabled by the generic profile | Software fixtures exercise identities/read handling, not every physical model. |
| Generic `RouterOS` platform without a reliable board model | Basic identity and available reads | Does not inherit hEX controls | Model uncertainty stays visible. |
| RouterOS 6, unknown or malformed version | No qualified RouterOS 7 profile | Disabled | No automatic upgrade is performed. |
| Non-MikroTik gateway | No active adapter | Unsupported | Passive AP use is a separate role. |

The current hEX profile recognizes identifiers including `hEX S`, `RB760iGS` and `E60iUGS`. That catalogue match is broader than the exact household-tested hardware/firmware combination. Firmware changes need a compatibility recheck. No universal throughput or client-count guarantee is published.

## API and account requirements

The adapter uses RouterOS's existing **binary API on TCP 8728**. WinBox TCP 8291 and WebFig HTTP/HTTPS are separate services. Pasting a WebFig page address does not mean the app uses WebFig or its SSL certificate.

The API must be enabled and reachable before login. Setup does not automatically provision services, accounts, firewall access or WAN forwarding. RouterOS also offers API-SSL on 8729, but ParentalApp's current adapter has no qualified TLS mode. See the [official API documentation](https://manual.mikrotik.com/docs/developer-guides/api/).

Use a dedicated authorized account and restrict the service to the intended management source. Read-only and write permissions are different. Interfaces may be readable even when queues or firewall data are not; that is partial access, not proof of control support.

## Network placement

For an individual-device action, MikroTik must be the in-path gateway and must bind the target's observed MAC/IP to the approved household scope.

- Bridging APs can pass individual clients through to the gateway.
- NAT on a downstream router can hide multiple clients behind one upstream identity.
- A NAT branch must not become a substitute target for one hidden client.
- Same-LAN access to an AP's settings page may bypass MikroTik.
- Built-in MikroTik Wi-Fi does not enable an unimplemented station-disconnect action.
- Shared PisoWiFi, hotspot/voucher and ISP/WAN configuration must remain outside ordinary household controls.

## Older and low-memory models

The original [hAP lite specifications](https://mikrotik.com/product/RB941-2nD) list 32 MB RAM and 16 MB storage. API availability does not establish acceptable monitoring or rule load.

Generic models and routers reporting 64 MB RAM or less start traffic sampling at a minimum five-second interval. This is a conservative polling choice, not a performance guarantee. Permissions, free memory, CPU load, existing rules and actual workload still require evaluation. Missing measurements stay unavailable.

## Qualifying another model

Record the exact board identifier, RouterOS version/channel, architecture, network role and account permissions. Validate inventory, directional measurements, one-client blocking/restoration, separate speed caps, established traffic/FastTrack, IPv6 coverage, parent/recovery access, unaffected siblings and router resource use.

Keep failing/unverified actions disabled. Do not enable all RouterOS 7 writes by loosening a model-name check alone. The app's current source and test records distinguish generic setup compatibility from actual control evidence.
