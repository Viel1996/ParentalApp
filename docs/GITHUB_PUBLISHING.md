# GitHub publishing notes

These notes prepare the Windows app's repository page. They do not create a repository or publish a release.

## Suggested description

> Native Windows app for household network monitoring and device internet controls through MikroTik hEX S on RouterOS 7, with monitoring-only setup for other RouterOS 7 models.

Suggested topics:

```text
parental-controls mikrotik routeros windows flutter network-monitoring bandwidth-management
```

Use the root README as the landing page. Link its detailed Windows guide and compatibility matrix. The public documentation describes the desktop application only.

When copying the documentation into a repository, retain these paths so the links and images work:

```text
README.md
docs/WINDOWS_APP_GUIDE.md
docs/ROUTER_COMPATIBILITY.md
docs/GITHUB_PUBLISHING.md
docs/images/dashboard.png
docs/images/device-information.png
docs/images/rules.png
docs/images/activity.png
assets/branding/parentalapp.png
licenses/WINDOWS_COMPONENTS.md
```

The prepared documentation ZIP contains these files only; it is not an application installer or a complete source release.

## Source and screenshots

Review the source, locked dependencies, scripts and required notices before committing. Do not upload the entire development folder without review.

Exclude:

- `.tools/`, build/staging/run folders, `node_modules/`, SDKs and downloaded binaries.
- SQLite databases, `runtime-state/`, vault state and household backups.
- Environment files, passwords, RouterOS exports and access tokens.
- Private signing keys, PFX/P12, keystores, SSH/VPN keys and machine-specific local configuration.
- Real-network screenshots/logs containing household identities, addresses, device names or sensitive details.

The root `.gitignore` excludes common artifacts, but exclusions are not a security audit. Review the actual staged files. Existing audit/test reports can contain development-machine paths; use the prepared public guides for the landing page.

Images in `docs/images/` use simulated data. Keep that caption and replace them with newer safe images as the interface changes. They are not live hardware acceptance evidence.

## Compatibility wording

Use:

> Primary control target: MikroTik hEX S with RouterOS 7. The current household deployment reports working controls on hEX S (2025), RouterOS 7.24.5 stable. Other RouterOS 7 models can enroll for available monitoring; their controls remain unverified and disabled.

Avoid “all MikroTik routers supported,” “all RouterOS 7 features work,” “disconnects any Wi-Fi client” or “production ready.” Regular hEX, earlier hEX S and hAP lite are not interchangeable hardware.

## Release assets

| Asset | Purpose |
| --- | --- |
| `ParentalApp-Setup.exe` | Complete native app/service installer |
| SHA-256 checksum file | Integrity for that exact final installer |
| Release notes | Version, changes, compatibility, known issues and recovery notes |
| Signing information | Actual publisher/signature status and development-signing limits |
| Required component notices | Notices for the exact shipped runtime/dependency set |

Keep generated binaries out of ordinary source commits; attach reviewed builds as release assets. Do not publish only `ParentalApp.exe` or `data/app.so` as a complete installation.

Current installers are locally development-signed. Running on the development PC does not establish trust on another machine. Public publisher signing and clean-machine acceptance remain open.

## Suggested release summary

> **ParentalApp 0.1.0 — Windows development preview**
>
> Native Windows app and local background service with measured traffic/daily charts, device inventory, reported restrictions, saved Activity and supported individual internet controls through MikroTik.
>
> Primary target: hEX S on RouterOS 7. Other RouterOS 7 devices have setup/best-effort monitoring only until qualified. Current transport is unencrypted TCP 8728 and must remain on a trusted private management network. Public signing, broader hardware qualification and the full release pilot are incomplete.

Generate checksums after final signing:

```powershell
Get-FileHash .\ParentalApp-Setup.exe -Algorithm SHA256
Get-AuthenticodeSignature .\ParentalApp-Setup.exe
```

Do not reuse a checksum from another build or publish a development certificate's private key.

## License and publication status

No project-wide root `LICENSE` file is present. The owner must select the intended license before describing the project's source reuse terms. Preserve all third-party component notices. Existing dependency/audit records are not a completed release-compliance assessment.

Before publishing, verify documentation/build agreement, working links/images, safe screenshots, absence of secret/runtime files, accurate compatibility, the license decision and visible signing limitations. No repository URL, download badge or issue link has been invented; add the real links after the repository exists.
