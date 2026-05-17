# CI modernization plan

A proposal for evolving the three current GitHub Actions workflows. The **already-landed** changes in this fork are minimal (checkout `v3` → `v4`, artifact upload). This document covers the full future shape.

## Current state

Three near-identical workflows in `.github/workflows/`:

| File | Platform | Backend |
|---|---|---|
| `linux_vulkan.yml` | `ubuntu-latest` | Vulkan |
| `windows_direct3d12.yml` | `windows-latest` | Direct3D12 |
| `macos_metal.yml` | `macos-latest` | Metal |

Each runs:

```bash
cd paint && ../base/make --compile
cd base/tests/cube     && ../../../base/make --compile
cd base/tests/fall     && ../../../base/make --compile
cd base/tests/triangle && ../../../base/make --compile
```

**Already landed in this fork:**
- `actions/checkout@v3` → `@v4` (mechanical, drop-in)
- `actions/upload-artifact@v4` step for `paint/build/` on all three platforms

## Proposed: unified matrix workflow

Replace the three workflow files with a single matrix workflow:

```yaml
name: build

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    name: ${{ matrix.os }} (${{ matrix.backend }})
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        include:
          - { os: ubuntu-latest,  backend: vulkan }
          - { os: windows-latest, backend: direct3d12 }
          - { os: macos-latest,   backend: metal }
    steps:
      - uses: actions/checkout@v4
      - name: Install Linux deps
        if: matrix.os == 'ubuntu-latest'
        run: sudo apt-get update && sudo apt-get install -y make clang libvulkan-dev libgtk-3-dev
      - name: Compile
        working-directory: paint
        run: ../base/make --compile
      - name: Build engine tests
        run: |
          cd base/tests/cube     && ../../../base/make --compile
          cd base/tests/fall     && ../../../base/make --compile
          cd base/tests/triangle && ../../../base/make --compile
      - uses: actions/upload-artifact@v4
        with:
          name: armorpaint-${{ matrix.os }}-${{ matrix.backend }}
          path: paint/build/
          retention-days: 7
```

**Benefits:**
- One file to maintain instead of three
- `fail-fast: false` means a failure on one OS doesn't cancel the others — see all platform failures at once
- Easy to add a backend (e.g. WebGPU) by adding one matrix row

**Risks:**
- Cancellation behavior changes slightly
- Anything that depended on the named jobs (branch protection rules, status checks) needs to be re-pointed

**Mitigation:** Land the matrix conversion on a branch first; verify the new job names match what's expected before merging.

## Proposed: caching

Linux: cache APT packages so `sudo apt-get install` doesn't repeat the same work each run.

```yaml
- uses: awalsh128/cache-apt-pkgs-action@latest
  with:
    packages: make clang libvulkan-dev libgtk-3-dev
    version: 1.0
```

macOS: cache `~/Library/Developer/Xcode/DerivedData/` keyed on `paint/project.js` hash.

Windows: less to cache — Visual Studio runners come pre-warmed.

## Proposed: format check

A separate, fast workflow that runs only `clang-format`:

```yaml
name: format
on: [pull_request]
jobs:
  clang-format:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: |
          find paint/sources base/sources -name '*.c' -o -name '*.h' \
            | xargs clang-format --dry-run --Werror
```

`.clang-format` already exists at the repo root, so this works without extra config.

## Proposed: markdown lint

Catch broken cross-links in `docs/`:

```yaml
- uses: lycheeverse/lychee-action@v1
  with:
    args: --no-progress --exclude '^https?://armorpaint.org' 'docs/**/*.md' '*.md'
```

`armorpaint.org` is excluded because it's external and not the fork's responsibility.

## Proposed: nightly upstream sync check

Pull `armory3d/armorpaint:main`, three-way merge into a draft branch, open a draft PR if there are non-trivial conflicts or changes. Helps the fork stay close to upstream without surprise merges.

This is non-trivial — defer until Phase 2 is otherwise complete.

## Proposed: release workflow

Triggered by a tag push (`v*`):

1. Builds all three platforms
2. Uploads artifacts to a GitHub Release
3. Generates release notes from `CHANGELOG.md` `## [Unreleased]` section
4. Moves `[Unreleased]` to a new versioned section in `CHANGELOG.md`

This is Phase 2 work, after the matrix conversion lands.

## Things to deliberately not add

- **CodeQL / dependency scanning** — this is a graphics app with a tightly controlled native dep set; CodeQL adds noise without value here
- **Docker builds** — out of scope; the project ships native binaries
- **Auto-merge bots** — small project, prefer human review
- **Automated changelog generation from commits** — `CHANGELOG.md` is hand-curated by design
