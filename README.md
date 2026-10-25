<div align="center">

<img src="images/logo.png" width="96" alt="BuildPort logo">

# BuildPort

**One manifest, one command, every store.**

Publish a game build to Steam, VK Play, RTGames, Yandex.Disk, Google Drive and your own server
from one window or one command — with the version in exactly one place and no secret ever written to disk in the clear.

[![Latest release](https://img.shields.io/github/v/release/JeffectH/BuildPort-releases?style=flat-square&color=ff5a1f)](https://github.com/JeffectH/BuildPort-releases/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/JeffectH/BuildPort-releases/total?style=flat-square&color=ff5a1f)](https://github.com/JeffectH/BuildPort-releases/releases)
![Platforms](https://img.shields.io/badge/platforms-Windows%20%7C%20Linux%20CLI-333?style=flat-square)
![License](https://img.shields.io/badge/license-freeware-333?style=flat-square)

[**Download**](#download) · [Features](#features) · [Quick start](#quick-start) · [Report a bug](https://github.com/JeffectH/BuildPort-releases/issues/new/choose) · [Русский](README.ru.md)

<img src="images/steam.png" width="820" alt="BuildPort desktop app, Steam tab">

</div>

## Download

| | Platform | File |
|---|---|---|
| **Desktop app** | Windows 10/11 x64 | [BuildPort-win-x64.zip](https://github.com/JeffectH/BuildPort-releases/releases/latest/download/BuildPort-win-x64.zip) |
| **CLI** | Windows x64 | [buildport-cli-win-x64.zip](https://github.com/JeffectH/BuildPort-releases/releases/latest/download/buildport-cli-win-x64.zip) |
| **CLI** | Linux x64 | [buildport-cli-linux-x64.tar.gz](https://github.com/JeffectH/BuildPort-releases/releases/latest/download/buildport-cli-linux-x64.tar.gz) |

Every build is a single self-contained executable: no installer, no .NET runtime to install.
Checksums are in `SHA256SUMS.txt` on each [release page](https://github.com/JeffectH/BuildPort-releases/releases).
macOS builds are coming.

> [!NOTE]
> The executables are not code-signed yet, so Windows SmartScreen may show *“Windows protected your PC”*
> on first launch. Click **More info → Run anyway**. Verify the file against `SHA256SUMS.txt` if in doubt.

## Features

- **Every store from one place.** Steam, VK Play and RTGames in the desktop app; plus SFTP, Yandex.Disk,
  Google Drive and any custom command in the CLI.
- **One version, everywhere.** Set it once — it flows into every store's build description, every archive
  name and every path on your server.
- **Secrets stay secret.** Tokens and passwords live in the OS keystore (DPAPI on Windows, Keychain,
  secret-tool) or in environment variables for CI. Every value is scrubbed from logs, command echoes and reports.
- **Check before you upload.** Every target is verified first: a missing token never surfaces after twenty
  minutes of uploading somewhere else.
- **Honest partial releases.** If one target of six fails, the other five still ship, the report says
  exactly what went where, and `--retry-failed` re-runs only what failed.
- **Vendor tools in one click.** steamcmd, bstool and buildtool are downloaded from their official sources —
  nothing is bundled.
- **Steam Guard handled.** The code is asked for in a dialog when steamcmd needs it.
- **English and Russian interface.**

## Screenshots

<table>
  <tr>
    <td><img src="images/vk.png" alt="VK Play tab"></td>
    <td><img src="images/rt.png" alt="RTGames tab"></td>
  </tr>
  <tr>
    <td align="center">VK Play</td>
    <td align="center">RTGames</td>
  </tr>
  <tr>
    <td><img src="images/tools.png" alt="Vendor tools tab"></td>
    <td><img src="images/steam-ru.png" alt="Russian interface"></td>
  </tr>
  <tr>
    <td align="center">Vendor tools, installed in one click</td>
    <td align="center">Russian interface</td>
  </tr>
</table>

## Quick start

### Desktop app

1. Download and unzip `BuildPort-win-x64.zip`, run `BuildPort.exe`.
2. Open **Tools** and press **Install** for the store tools you need (or point to existing ones).
3. Fill in the version, the game name and the build folder on each store tab.
4. Press **Check**, then **Upload** — or **Upload all** to ship to every enabled store at once.

### CLI

Describe the release once in `release.yaml` and commit it next to your project:

```yaml
version: "1.4.0"
game: "My Game"
description: "Update build to v{version}"

artifacts:
  steam-win:  { path: "Builds/Windows", type: directory }
  public-zip: { from: steam-win, type: zip, file_name_template: "{game}_{version}.zip" }

targets:
  - type: steam
    artifact: steam-win
    app_id: "480"
    depot_id: "481"
    branch: default

  - type: sftp
    artifact: public-zip
    host: "builds.example.com"
    username: "deploy"
    remote_dir: "/var/www/downloads"
    private_key: "keystore:buildport/server/ssh-key"
```

```bash
buildport login steam             # store credentials in the OS keystore, once
buildport publish --dry-run       # print the exact plan and commands, touch nothing
buildport publish                 # check every target, then upload
buildport publish --retry-failed  # re-run only what failed
buildport doctor                  # find missing tools and plaintext tokens left on disk
```

The manifest holds *pointers* to secrets (`keystore:…`, `env:…`), never values — so it is safe to commit,
and the same file works on your machine and on a CI runner.

## Supported targets

| Target | Desktop app | CLI | Notes |
|---|:---:|:---:|---|
| Steam | ✓ | ✓ | via steamcmd, Steam Guard supported |
| VK Play | ✓ | ✓ | via bstool, Windows only |
| RTGames | ✓ | ✓ | via buildtool, Windows only |
| SFTP / own server | | ✓ | atomic replace |
| Yandex.Disk | | ✓ | optional public link |
| Google Drive | | ✓ | |
| Any command | | ✓ | `exec` target |

## Known limitation

VK Play's `bstool.exe` takes its token as a command-line argument, which makes it visible in the process
list, and the vendor writes the full command line to its own log. BuildPort cannot fix this and adds no
leaks of its own; `buildport doctor` shows where plaintext tokens sit on your disk.

## Feedback

Found a bug or missing a store? [Open an issue](https://github.com/JeffectH/BuildPort-releases/issues/new/choose).
Release notes for every version are in [CHANGELOG.md](CHANGELOG.md).

## License

BuildPort is free to use, including for commercial projects. It is closed-source software distributed under
the [BuildPort License](LICENSE.md). steamcmd, bstool and buildtool belong to their vendors and are covered
by their own terms.
