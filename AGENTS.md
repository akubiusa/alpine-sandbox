# AGENTS.md

Guidance for AI coding agents and contributors. `CLAUDE.md` imports this file, and `.github/copilot-instructions.md` adds the review checklist.

## Language

- All deliverables are written in **English**: commit messages, PR/Issue titles and bodies, review comments, code comments, and documentation (`README.md`, `AGENTS.md`, etc.).
- Exceptions (keep as is): quotations, technical identifiers, generated files, and external source text.
- Runtime messages (logs, errors) are English.
- Do not rewrite Git history to change the language of past commits.

## Overview

Spins up a disposable Alpine Linux sandbox inside Docker with a single command, primarily on Windows. The session is recorded with `asciinema` (default) or the `script` command so it can be replayed later.
The project is two Dockerfiles and a batch launcher: no build system, package manager, tests, linter or CI.

## Structure

- `alpine-sandbox.bat`: Windows launcher. Creates a timestamped `data\<datetime>\{work,logs}` directory next to the script, builds `Dockerfile` (from the current directory, not the script directory) as `alpine-sandbox:1.0`, then runs it interactively with `work` mounted at `/work` and `logs` at `/logs`.
- `Dockerfile`: default image (asciinema). The `ENTRYPOINT` records the session to `/logs/record.cast`.
- `Dockerfile.script-cmd-ver`: alternative image that records with `script` (from `util-linux`) to `/logs/commands.log` and `/logs/timing.log`. Not referenced by the launcher; built manually. Currently broken: `CMD ["/bin/sh"]` is passed to `script` as an extra file argument and it exits with `unexpected number of arguments`.
- `README.md`: user-facing install and usage notes.

## Commands

- Build (default): `docker build --tag alpine-sandbox:1.0 .`
- Run manually: `docker run -it --rm -v <logdir>:/logs -v <workdir>:/work alpine-sandbox:1.0`
- On Windows, `alpine-sandbox.bat` does the build and run in one step.

## Conventions

- The container working directory is `/work` and recordings go under `/logs`. Keep these consistent across both Dockerfiles and the launcher, because the volume mounts depend on them.
- Both Dockerfiles use `alpine:3` (major tag only); keep them on the same base unless there is a reason to diverge.
- When you rename the image tag, change the mount paths, or add a Dockerfile variant, update `alpine-sandbox.bat`, `README.md` and this file together.
- Commits: [Conventional Commits](https://www.conventionalcommits.org/). Branches: [Conventional Branch](https://conventional-branch.github.io) short form (`feat`, `fix`, `docs`, ...).
- Do not push commits to Renovate PRs. Never commit secrets.
