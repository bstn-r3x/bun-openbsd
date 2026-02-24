# UNOFFICIAL `bun-openbsd`

OpenBSD porting fork/snapshot of Bun used by the OpenCode-on-OpenBSD project.

- Upstream Bun: [oven-sh/bun](https://github.com/oven-sh/bun)
- Bun docs: [bun.com/docs](https://bun.com/docs)

## Purpose

This repo tracks Bun-side OpenBSD changes required to run and compile OpenCode on OpenBSD.

It is one of three repos:
- `openbsd-opencode-port` (docs/scripts/packaging workflow)
- `bun-openbsd` (this repo)
- `opencode-openbsd` (OpenCode fork)

## What This Repo Is Not

- not an official Bun release channel
- not a substitute for upstream Bun docs
- not an OpenBSD package

## Maintainer Workflow

Use `openbsd-opencode-port` for build/test/release procedures.
Make Bun-side fixes here, validate on OpenBSD using the orchestration workflow, then publish tested commits/tags.

## Scope of Changes

Typical changes in this repo include:
- OpenBSD runtime/syscall compatibility
- event loop / kqueue fixes
- build/linker compatibility workarounds
- installer/runtime behavior needed for OpenCode on OpenBSD
