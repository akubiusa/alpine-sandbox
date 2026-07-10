# CLAUDE.md

## Overview

Spins up a disposable Alpine Linux sandbox inside Docker with a single command,
primarily for use on Windows. Commands run in the container and their output are
recorded either with the `script` command or with [asciinema](https://asciinema.org/),
so the session can be replayed later. Handy for quickly validating steps before
committing them to a real Dockerfile.

## Repository layout

- `alpine-sandbox.bat` — Windows launcher. Creates a timestamped `data/<datetime>/{work,logs}`
  directory, builds the image from `Dockerfile`, then runs it interactively with `work` and
  `logs` mounted as volumes.
- `Dockerfile` — default image (asciinema variant). `ENTRYPOINT` records the session to
  `/logs/record.cast` via `asciinema rec`.
- `Dockerfile.script-cmd-ver` — alternative image that records with the `script` command
  (from `util-linux`) to `/logs/commands.log` and `/logs/timing.log`. Not referenced by
  `alpine-sandbox.bat`; build it manually if you want this variant.
- `README.md` — user-facing install/usage notes (Japanese).

## Working with this project

There is no build system, package manager, test suite, or linter — the whole project is
two Dockerfiles and a batch launcher.

- Build (default): `docker build --tag alpine-sandbox:1.0 .`
- Run manually: `docker run -it --rm -v <logdir>:/logs -v <workdir>:/work alpine-sandbox:1.0`
- On Windows, `alpine-sandbox.bat` does the build + run in one step.

## Conventions

- Container working directory is `/work`; recordings are written under `/logs`. Keep both
  paths consistent across the Dockerfiles and the launcher — the volume mounts depend on them.
- `Dockerfile` pins `alpine:3` (major tag only); keep the two Dockerfiles on the same base
  unless there is a reason to diverge.

## Documentation update rules

- If you rename the image tag, change the mount paths (`/work`, `/logs`), or add a new
  Dockerfile variant, update `alpine-sandbox.bat` and `README.md` together so the launcher
  and the docs stay in sync. These values are also referenced in `CLAUDE.md` and
  `.github/copilot-instructions.md`, so update those two agent docs in the same change to
  keep them from going stale.
