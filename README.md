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


## OpenBSD `getFdPath` Strategy (Robustness Plan)

### Current State

OpenBSD lacks the common fd-to-path helpers used on other platforms (`F_GETPATH`, `/proc/self/fd` path resolution).
The current Bun fallback in `src/sys.zig` uses:
- save cwd fd
- `fchdir(fd)`
- `getcwd()`
- restore cwd

This works for directory FDs only. Regular file FDs return `ENOTDIR`.

Recent hardening in this fork:
- preserves `getFdPath` errno semantics on OpenBSD
- avoids `getFdPath()` on several regular-file FD callsites by propagating known paths
- serializes the cwd-swapping fallback with a process-global mutex (risk reduction)

### Preferred Direction (Most Robust)

The long-term fix is not a more complex cwd workaround. It is to avoid needing fd->path reverse lookup wherever the path is already known.

Preferred pattern:
- carry path strings forward when opening files/directories
- avoid calling `getFdPath()` on regular file descriptors on OpenBSD
- treat `getFdPath()` as a directory-FD-only / last-resort API on OpenBSD

### Practical Roadmap

1. Continue replacing high-impact regular-file FD `getFdPath` callsites with path propagation.
2. Keep OpenBSD command-level fd-path regression smokes in the maintainer workflow (`run`, install migrations, standalone compile, fd-based copy path).
3. Add helper patterns for “open file + keep path” flows to reduce regression risk.
4. Reassess whether any non-cwd-mutating directory-FD path strategy is realistically available on OpenBSD; if not, keep the serialized fallback but make it rare.
