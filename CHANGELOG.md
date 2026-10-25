# Changelog

All notable changes to BuildPort. Versions follow [Semantic Versioning](https://semver.org).

## [0.1.0] - 2026-10-25

First public release.

### Added

- Desktop app for Windows with Steam, VK Play and RTGames tabs, shared version, game name and build
  description, and **Upload all** to publish to every enabled store at once.
- One-click install of steamcmd, bstool and buildtool from their official sources.
- Steam branch management and Steam Guard code prompt.
- English and Russian interface.
- `buildport` CLI for Windows and Linux: `publish` (with `--dry-run` and `--retry-failed`), `validate`,
  `login`, `secret`, `doctor`, `tools`.
- Release manifest targets: `steam`, `vk`, `rtgames`, `sftp`, `yandex-disk`, `google-drive`, `exec`.
- Secrets in the OS key store (DPAPI, Keychain, secret-tool) or environment variables, redacted from
  every log line and report.
