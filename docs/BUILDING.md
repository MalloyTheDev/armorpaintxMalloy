# Building ArmorPaint x Malloy

This guide cross-references the upstream build documentation in [`paint/readme.md`](../paint/readme.md). When upstream changes its build commands, **update `paint/readme.md` first** — this document mirrors it, not the other way around.

## Requirements

| Platform | Compiler | Notable dependencies |
|---|---|---|
| Windows | Visual Studio with clang tools | `git` |
| Linux | `clang` 19+ (for C23 `#embed`) | `make`, `libvulkan-dev`, `libgtk-3-dev` |
| macOS | Xcode (recent) | — |
| Android | Android Studio | NDK installed via Android Studio |
| iOS | Xcode (recent) | — |
| WASM | clang 19+ + emscripten | — |

> **C23 `#embed` is required for asset embedding.** Older clang versions will fail at the embed step. Check with `clang --version` — needs 19 or newer.

## Build commands

All commands run from inside the [`paint/`](../paint/) directory.

### Windows (x64)

```bash
..\base\make
```

Open `paint\build\ArmorPaint.sln` in Visual Studio. Build → Run.

### Linux (x64)

```bash
../base/make --run
```

Compiles and launches in one step.

### macOS (arm64)

```bash
../base/make
```

Open `paint/build/ArmorPaint.xcodeproj` in Xcode. Build → Run.

### Android (arm64)

```bash
../base/make --target android
```

Open `paint/build/ArmorPaint` in Android Studio. Connect a device, Build → Run.

### iOS (arm64)

```bash
../base/make --target ios
```

Open `paint/build/ArmorPaint.xcodeproj` in Xcode. Build → Run on a connected device.

### WASM

```bash
../base/make --target wasm --compile --embed
```

`--embed` is required because WASM cannot lazy-load assets from the filesystem.

## Common commands

| Action | Command |
|---|---|
| Compile only (no IDE / no run) | `../base/make --compile` |
| Generate a locale file | `./base/make --js base/tools/extract_locales.js <code>` (from repo root) |
| Embed asset blob | `../base/make --embed` |

## Troubleshooting

- **`clang: error: unknown argument: '#embed'`** — your clang is too old. Install clang 19+.
- **Windows: `make.bat` errors immediately** — make sure Visual Studio's clang tools are on `PATH`. Reopen the "Developer Command Prompt for VS".
- **Linux: linker error mentioning Vulkan** — `sudo apt install libvulkan-dev libgtk-3-dev`.
- **macOS: Xcode project won't build** — clean (`rm -rf paint/build`) and re-run `../base/make`.
- **WASM: build hangs at embed step** — `--embed` reads every file under `paint/assets/`; large additions there slow this down. Trim or temporarily exclude.

## CI build commands

The three GitHub workflows under `.github/workflows/` run, on a clean Ubuntu / macOS / Windows runner:

```bash
cd paint
../base/make --compile
```

Followed by:

```bash
cd base/tests/cube     && ../../../base/make --compile
cd base/tests/fall     && ../../../base/make --compile
cd base/tests/triangle && ../../../base/make --compile
```

A working local build using `--compile` matches what CI runs. See [CI_PLAN.md](CI_PLAN.md) for the planned modernization.

## Where to file build issues

This is a fork. If the build fails the same way against upstream `armory3d/armorpaint`, the issue likely belongs there. Reproduce against upstream first; only open a fork issue if the failure is fork-specific.
