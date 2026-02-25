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

## OpenBSD Rebuild Prerequisites (Current State)

This fork is buildable for OpenBSD in the project workflow, but it is not a stock upstream Bun build.
A working rebuild currently depends on:

- the OpenBSD patch stack in this repo (runtime + installer + resolver + build support changes)
- the OpenCode orchestration workflow in `openbsd-opencode-port` (build/test/relink steps)
- a patched Zig toolchain/lib setup used by the project for OpenBSD cross-compilation
- generated Bun codegen outputs before the Zig `obj` build stage
- OpenBSD-side dependency/static library build artifacts for the final relink step

For the end-to-end build procedure, use `openbsd-opencode-port/OPENCODE-PORT-BUILD-GUIDE.md`.

## Minimal Patch Stack (What Is Actually Required)

A clean OpenBSD rebuild currently requires more than the latest syscall fixes. In practice, the minimum working patch set spans these areas:

- `build.zig` and build/config support for the OpenBSD target (the upstream base may reject `.openbsd` entirely)
- `src/sys.zig` and `src/bun.zig` runtime compatibility (syscall dispatch, fd-path handling, errno mapping)
- `src/install/*` package-manager/install path handling for OpenBSD (avoid `getFdPath` on regular file descriptors)
- `src/install/npm.zig` and `src/resolver/resolve_path.zig` platform OS/path mapping support for OpenBSD
- `src/install/isolated_install/Installer.zig` behavior needed for stable workspace installs in this workflow

This is why validating only one or two fresh commits against a clean upstream checkout is not enough yet; the local buildable OpenBSD workspace typically carries a broader patch stack.
