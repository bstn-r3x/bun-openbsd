# UNOFFICIAL `bun-openbsd` (OpenBSD Porting Fork/Snapshot)

This is **not** the official Bun repository.

- Official Bun repository: [oven-sh/bun](https://github.com/oven-sh/bun)
- Official Bun docs: [bun.com/docs](https://bun.com/docs)

This repository is a **porting-focused fork/snapshot** used to develop and track OpenBSD-specific Bun changes required for the OpenCode-on-OpenBSD project.

## What This Repo Is

Use this repo for:
- OpenBSD compatibility changes in Bun
- reproducible source history for the OpenBSD porting effort
- patch review and publication of Bun-side port fixes

This repo is part of a 3-repo setup:
1. `openbsd-opencode-port` (orchestration/docs/scripts)
2. `bun-openbsd` (this repo)
3. `opencode-openbsd` (OpenCode porting fork/snapshot)

## What This Repo Is Not

- not an official upstream Bun release channel
- not a substitute for Bun upstream documentation
- not an OpenBSD `pkg_add` package
- not guaranteed to track Bun upstream `main` continuously

## Current Role in the OpenBSD Port

The OpenCode port depends on a patched Bun runtime for OpenBSD.
This repo contains the Bun-side changes needed for:
- event loop / kqueue behavior fixes
- OpenBSD syscall/runtime compatibility
- build and linker workarounds
- support required to run and compile OpenCode on OpenBSD

## How To Use This Repo (Maintainer View)

1. Start with the orchestration repo (`openbsd-opencode-port`) for the full workflow.
2. Use that repo's build guide and test plan to build and validate on OpenBSD.
3. Make Bun runtime/toolchain changes in this repo.
4. Re-run baseline and interactive tests from the orchestration repo.
5. Publish tested commits/tags after validation.

## Build Instructions

Build instructions are intentionally maintained in the orchestration repo:
- `openbsd-opencode-port/OPENCODE-PORT-BUILD-GUIDE.md`
- `openbsd-opencode-port/COMPREHENSIVE-TEST-PLAN.md`

This keeps the multi-machine workflow documented in one place.

## Upstream Sync Policy (Recommended)

When updating from upstream Bun:
1. fetch/merge or rebase from `oven-sh/bun`
2. preserve OpenBSD-specific commits in a reviewable series
3. re-run the OpenCode regression workflow on OpenBSD
4. update release notes/status in the orchestration repo

## Support Boundaries

If an issue is specific to this OpenBSD porting fork/snapshot, track it here.
For general Bun usage/support, use the official Bun channels and the upstream repository.
