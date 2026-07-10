# Copilot code review instructions

This repository is a small tool: two Dockerfiles plus a Windows batch launcher build and
run a disposable Alpine sandbox that records the terminal session (via `asciinema` or the
`script` command). There is no application source, no package manager, and no test suite.

## What to focus on in review

- **Dockerfile / launcher consistency**: `alpine-sandbox.bat` builds from `Dockerfile` and
  mounts volumes at `/work` and `/logs`. Flag changes that break this contract, e.g. renaming
  `WORKDIR`, moving the recording output paths, or changing the image tag in only one place.
- **Shell / batch correctness**: in `alpine-sandbox.bat`, watch for quoting and path issues
  around `%~dp0`, `%date%`/`%time%` parsing, and `mkdir` on paths that may contain spaces.
- **Docker hygiene**: prefer `--no-cache` for `apk add`, avoid leaving package caches in the
  final image, and keep the two Dockerfiles on the same Alpine base tag.

## What NOT to flag

- The absence of tests, CI, linters, or a package manifest — this project intentionally has none.
- `Dockerfile.script-cmd-ver` not being referenced by `alpine-sandbox.bat` — it is an
  intentional alternative variant, built manually.
- Japanese text in `README.md` — the user-facing docs are written in Japanese by design.
