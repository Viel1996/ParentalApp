# Windows app guide

Updated 19 October 2026. ParentalApp is a native Windows application and local background service for household monitoring and supported MikroTik network controls.

## Requirements

| Component | Requirement |
| --- | --- |
| OS | Windows 11 x64; installer minimum build 22000. Other Windows/CPU combinations are unqualified. |
| Installation | Administrator approval; normal use from the enrolled Windows account |
| Control gateway | MikroTik hEX S on RouterOS 7, subject to the current profile and household checks |
| Other RouterOS 7 models | Setup and available monitoring until controls are qualified |
| API | Existing binary `api` service, TCP 8728, restricted to trusted management access |
| Account | Authorized RouterOS credentials with the required read/control permissions |
| Clients | Individually observed devices in the approved LAN; bridging APs preferred |
| Collection | Windows host remains awake and can reach the router |

No tested Windows RAM/storage floor or universal client-count limit has been established. The installer bundles application runtimes; developer tools are not an end-user prerequisite.

## Install and connect

Run `ParentalApp-Setup.exe`, approve the Windows administrator prompt and complete setup. Use the enrolled Windows account to open the application. The app's parent password is separate from the RouterOS password.

Enter a recognizable house name, actual gateway and LAN range. `192.168.88.1` / `192.168.88.0/24` are examples. Check API reachability, enter the RouterOS login locally and read the router. Inspect its model, firmware, architecture, available reads and control status before using actions. A monitoring-only result on another model is a valid setup outcome, not a failed promise of controls.

Setup does not create the API service, expose management on the WAN, update firmware, reset equipment, convert router modes or automatically isolate a shared network. The administrator must prepare authorized access and household boundaries separately.

### Signing and upgrades

Current installers are signed with a local development certificate. Their installation on the development PC does not establish public trust on other PCs. Check the supplied checksum/signing information; do not disable Windows security protections to run a blocked build. Public publisher signing and clean-machine/recovery/release-pilot acceptance remain open.

Upgrades retain the existing parent account and household state. Ship complete matching bundles, including executable, DLLs, assets and fonts; never replace only `data/app.so`.

## Dashboard

Summary cards show reported online clients, internet blocks, speed-limited devices and the latest recorded day's total. Infrastructure/AP rows are not counted as ordinary child devices in those summary counts.

The speed chart uses actual measurement timestamps, blue download and orange upload series, gaps, pointer inspection and a values view. A screen redraw is not a new sample. The daily chart shows recorded download/upload totals for available dates, with exact values and partial-coverage wording.

**Household daily totals currently use UTC. Per-device Today uses the collector PC's local calendar.** Sleep/offline periods leave incomplete coverage. These are not complete ISP billing totals.

Network overview shows returned router identity and saved connection information. The Device information selector shows names, IP/MAC, state, rates, limits, usage and observation details. Unknown data stays unknown. Use Open device for the Devices view, or the single Connection settings shortcut for Settings → Network.

## Devices and controls

Search by name/IP/MAC, filter and sort the table. Download/upload headings and value groups are centered. Hostnames can be absent; randomized/private MAC changes can create a different network identity.

| Action | Meaning |
| --- | --- |
| Block internet | Restrict the selected device's routed internet through the managed MikroTik |
| Unblock internet | Remove the applicable ParentalApp internet block |
| Set speed limits | Apply distinct positive download/upload caps in Mbps |
| Clear speed limit | Remove the applicable ParentalApp speed cap |
| Refresh status | Request a current supported inventory reading |
| Forget from list | Hide a saved row; this does not disconnect it or remove policies |
| Restore to list | Make a forgotten row visible again |

Gateway, parent-PC and recognized AP targets are protected from ordinary child-device controls. A stale/missing client binding can prevent a change. In RouterOS, zero can mean unlimited; use Block internet to cut access.

Readback confirms a router setting, not necessarily end-device behavior. Test with an authorized client and account for its mobile data/alternate connections. An internet block does not remove Wi-Fi association, delete a lease, erase a password or power off the device. Removing one ParentalApp restriction does not remove another administrator's policy.

## Rules and Activity

Rules prioritizes restrictions from the current reading, then lists available controls. Manual tests are under Connection tests → Open device test tools. **They are real changes after confirmation, not simulations.** They never run automatically. Verify the effect, then unblock/clear test limits. Planning/UI scaffolding for schedules or quotas is not active enforcement.

Activity lists saved action results, monitoring/connection events and alerts. Search/filter them and expand an event for details. Inventory observations are separate reports, not browsing history.

New control messages use concise wording:

- `Internet blocked for Child phone.`
- `Internet unblocked for Child phone.`
- `Speed limits updated for Child phone. Download: 10 Mbps. Upload: 2 Mbps.`
- `Speed limits removed for Child phone.`

Failed/unconfirmed results retain explanations. Historical audit wording is preserved across upgrades.

## Current setup and cancellation

Use Dashboard → Connection settings or Settings → Network.

- **Current setup → Edit connection:** edit the house name while retaining scope/history.
- **Manage:** reopen saved router sign-in/connection setup.
- **Monitored ports:** select household interfaces represented in the device view.
- **Add house:** create a separate connection rather than repurposing another router's rules/history.
- **Cancel setup / Close setup:** return an existing signed-in user to saved Network settings. Initial enrollment retains its protected paused landing.

A different gateway/LAN requires separate enrollment/recovery handling. Do not erase network configuration as a routine way to exit setup.

## Background service and privacy

The Windows service starts automatically under restricted account `NT SERVICE\ParentalApp`. Desktop communication uses a local owner-restricted named pipe. Collection can continue with the window closed while the PC is awake. Sleep/shutdown/router loss can interrupt collection; already-applied rules can remain active. Stopping monitoring does not remove blocks/caps.

State is stored locally in SQLite. Parent authentication and RouterOS credentials are separate. Protected local/service facilities exist, but full security qualification is incomplete. Do not upload databases, runtime/vault state, backups, credentials or raw household logs to a public repository. Use simulated/redacted screenshots.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Service unavailable | Installation/service status and the enrolled Windows account; do not disable security protections. |
| API connection fails | Actual private address, reachability, existing API service and allowed management source. WinBox/WebFig success does not prove API access. |
| Login rejected | RouterOS account/password and API permissions; hotspot/voucher users are different. |
| Monitoring only | Model/firmware and the compatibility matrix; generic controls are intentionally disabled. |
| Device disappeared | Search/filters, Forgotten, disconnection, changed private MAC or downstream NAT. |
| Missing/zero rates | Distinguish actual zero from unavailable data; check collection, timestamps and counters. |
| Phone still shows Wi-Fi | Expected: internet blocking is not radio disconnection. |
| Phone still has internet | Alternate networks/mobile data, target identity and qualified IPv6/FastTrack behavior. |
| Totals differ | Timezone, directions, units, coverage, counter source and unobserved periods. |

For an issue, supply app/Windows versions, MikroTik model, exact RouterOS version, affected action, expected/observed result and sanitized error text. Keep passwords, keys, tokens, serials and full router exports out of public issues.

## Developer information

```text
Flutter Windows desktop
        |
Local owner-restricted named pipe
        |
Restricted Windows service host
        |
Node/NestJS controller + SQLite
        |
Isolated Python RouterOS adapter
        |
Authorized private MikroTik binary API
```

| Source | Purpose |
| --- | --- |
| `apps/controller-windows/` | Desktop, charts, tables and setup |
| `services/controller/src/` | Authentication, compatibility, state, monitoring and action coordination |
| `workers/mikrotik-adapter/` | API adapter, vendored notices and local protocol patches |
| `platform/windows/service/` | Host, installation helper and service configuration |
| `platform/windows/inno_setup/` | Installer source |
| `tools/build/` | Build, staging, branding and signing helpers |
| `tests/` | Controller/adapter checks |
| `assets/branding/`, `licenses/` | Original branding and component notices |

The current toolchain uses Flutter 3.47.7, Node 24.19.x, pnpm with the checked-in lockfile, bundled Python 3.13.x, Visual Studio Build Tools 2022 with Windows C++ tooling/SDK, the .NET Framework C# compiler and Inno Setup 6.7.3. Runtime downloads/checksums are recorded in the build scripts and `audit/windows-runtime-artifacts.json`.

Several scripts expect project-local `.tools` paths, an Inno Setup compiler and a particular Visual C++ redistributable directory. Prepare/review those paths for your machine first. A clean checkout is not yet a qualified one-command build environment.

From the repository root:

```powershell
pnpm install --frozen-lockfile --ignore-scripts
pnpm build:controller
pnpm test
python -m unittest discover -s tests -p 'test_*.py'
```

From `apps/controller-windows`, using the matching Flutter SDK:

```powershell
flutter pub get
flutter analyze
flutter test
```

After preparing the existing local tool/runtime layout, build/stage/package from the root:

```powershell
./tools/build/Build-Windows.ps1
./tools/build/Stage-Windows.ps1
node tools/Test-StagedBundle.mjs
./tools/build/Build-Installer.ps1
```

The staging script compiles the controller and bundles app/service/runtimes/notices. Default installer output is `build/installer/ParentalApp-Setup.exe`. Scripts use a local development signing certificate; never publish its private key or automatically install trust certificates on another person's PC.

Tests use isolated fixtures and should not contact household hardware. Rendered screenshots use simulated data. Actual router acceptance requires a separately authorized test and does not follow from a passing fixture.
