# Copilot code review instructions

Follow `AGENTS.md` at the repository root (structure, conventions, language rules).

## Language

- Write all deliverables (PR/Issue text, review comments, code comments, docs) in English.
- Runtime messages are English. Do not flag English text in `README.md`.

## Review checklist

- **Dockerfile / launcher consistency**: `alpine-sandbox.bat` builds from `Dockerfile` and mounts volumes at `/work` and `/logs`. Flag changes that break this contract, e.g. renaming `WORKDIR`, moving the recording output paths, or changing the image tag in only one place.
- **Batch correctness**: in `alpine-sandbox.bat`, watch for quoting and path issues around `%~dp0`, `%date%`/`%time%` parsing, and `mkdir` on paths that may contain spaces.
- **Docker hygiene**: prefer `--no-cache` for `apk add`, avoid leaving package caches in the final image, and keep both Dockerfiles on the same Alpine base tag.
- **Docs in sync**: `README.md` and `AGENTS.md` must match the launcher and Dockerfiles.

## Do not flag

- The absence of tests, CI, linters, or a package manifest; this project intentionally has none.
- `Dockerfile.script-cmd-ver` not being referenced by `alpine-sandbox.bat`; it is an intentional variant built manually.
