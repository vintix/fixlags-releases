# FixLags for Windows: official releases

This repository is the public download and update channel of **FixLags**, a Windows app that
routes selected game traffic through a WireGuard relay and measures whether that actually helps
(https://fixlags.com).

**Download:** open the [latest release](https://github.com/vintix/fixlags-releases/releases/latest)
and download `FixLags_<version>_x64-setup.exe`, or use https://fixlags.com/download.

The app is free to download and use for account management, game selection and network
diagnostics. Routing game traffic through the FixLags relay requires an active subscription
(https://fixlags.com/pricing); access is checked by the FixLags servers, not by the app.

## What this repository contains

Only distribution files, attached to each GitHub Release:

| File | Purpose |
|---|---|
| `FixLags_<version>_x64-setup.exe` | Windows installer (NSIS, 64-bit, per-machine) |
| `FixLags_<version>_x64-setup.exe.sig` | Update signature of the installer (minisign / Tauri updater) |
| `latest.json` | Update manifest read by the app (version, notes, download URL, signature) |
| `SHA256SUMS.txt` | SHA-256 checksums of the files above |

It does **not** contain FixLags source code, server code or any configuration or credentials.
The source code is proprietary and kept private. The "Source code" archives that GitHub adds to
every release are generated from this repository and contain only this README and the files in
`channels/`.

## Verify a download

**1. Checksum.** In PowerShell, in the folder with the installer:

```powershell
Get-FileHash .\FixLags_0.10.1_x64-setup.exe -Algorithm SHA256
```

The hash must match the line for that file in the release's `SHA256SUMS.txt` (and on
https://fixlags.com/download).

**2. Windows code signing (Authenticode).** Current releases are **not Authenticode-signed**.
Windows SmartScreen therefore shows *"Windows protected your PC"* for the installer. Run it only if
the checksum matched: *More info* → *Run anyway*. Each release's notes state its signing status.

**3. Update signature.** Every installer is signed with the FixLags update key. The app embeds
the public key below and installs an update only if the signature verifies (and the signed
version matches the announced version). You can check it yourself with
[minisign](https://jedisct1.github.io/minisign/):

```text
untrusted comment: minisign public key: 6C6431C92E5E7C2C
RWQsfF4uyTFkbGESnlHZb+ohYvfBii9gm38dJ9opopT5vau+c2JCYcaV
```

```powershell
# the .sig asset is base64-encoded minisign text; decode it first
[IO.File]::WriteAllBytes("$PWD\setup.minisig", [Convert]::FromBase64String((Get-Content .\FixLags_0.10.1_x64-setup.exe.sig -Raw)))
minisign -Vm .\FixLags_0.10.1_x64-setup.exe -x .\setup.minisig -P RWQsfF4uyTFkbGESnlHZb+ohYvfBii9gm38dJ9opopT5vau+c2JCYcaV
```

## Automatic updates

FixLags checks `releases/latest/download/latest.json` in this repository shortly after start
and every 6 hours. When a newer version is found it shows *"FixLags vX.Y.Z is available"*;
nothing is downloaded or installed until you click **Update**, and the app disconnects the relay
and restores your normal routes before the installer runs. Releases are published once and never
replaced; a correction is always a new version.

`channels/test/` holds the manifest of an internal test channel used to verify updates before a
stable release. Test-channel releases are marked *pre-release* and are not meant for general use.

## Requirements

Windows 10 (1809 or newer) or Windows 11, 64-bit; administrator rights (FixLags creates a
network adapter and routes). WebView2 is installed from Microsoft if missing.

## Support and security

Support: https://fixlags.com/contact. Please report security issues privately through the same
page rather than in public.
