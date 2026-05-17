# Plugin guide

This fork is **plugin-first**. The cheapest way to add a new feature without destabilizing the app is to ship it as a plugin.

## What's already there

Existing plugins under [`paint/plugins/`](../paint/plugins/):

| Plugin | Purpose |
|---|---|
| `io_exr` | OpenEXR import/export |
| `io_fbx` | FBX import/export |
| `io_gltf` | glTF import/export |
| `io_psd` | Photoshop PSD import/export |
| `io_svg` | SVG import |
| `io_tiff` | TIFF import/export |
| `uv_unwrap` | UV unwrapping utility |

Top-level files: [`paint/plugins/plugins.c`](../paint/plugins/plugins.c) and [`paint/plugins/project.js`](../paint/plugins/project.js).

## Where to look first

Before writing a new plugin, read at least one existing IO plugin end-to-end. `io_gltf` and `io_psd` are good starting points — they cover both import and export. Trace how they're registered in `plugins.c` and `project.js`.

Asset-side plugin metadata lives in [`paint/assets/plugins/`](../paint/assets/plugins/).

## Proposed first custom plugin: `malloy_texture_validator`

A non-destructive pre-export check that runs against the current material/layer stack.

**What it checks:**
- Texture resolution consistency across maps in a material
- Normal map format sanity (DirectX vs OpenGL convention, expected channel layout)
- Missing or all-flat roughness / metallic / AO maps
- Naming convention adherence per target engine (Unity, Unreal, Godot)
- Total VRAM cost estimate for the material set

**What it produces:**
- A summary panel (severity-coded: pass / warn / fail)
- A copyable text report
- An export-readiness score (0–100)

**Why it's a good first plugin:**
- Read-only — cannot corrupt user data
- No new engine APIs needed; reads existing material and texture state
- Useful from day one for any game-asset workflow
- Mirrors patterns already in the `io_*` plugins

## Plugin safety rules

1. **Never write outside `paint/plugins/<your_plugin>/`** during the plugin's own build steps. App data and user files are off-limits except through documented APIs.
2. **No background threads that survive plugin teardown.** The app must be able to disable your plugin cleanly.
3. **No silent network calls.** If a plugin reaches out to a network service (e.g. an AI critic that uses a remote model), it must surface that clearly in its UI and respect a fork-wide "offline" toggle (to be added in Phase 3).
4. **Respect the zlib license.** If you bundle third-party code, include its license alongside your plugin and add an entry to [`paint/assets/licenses/`](../paint/assets/licenses/).

## Naming conventions

- Fork-specific plugins: `malloy_<purpose>` (e.g. `malloy_texture_validator`).
- IO plugins for new formats: `io_<format>` matching the existing pattern.
- Inside a plugin directory, follow the file naming used by existing IO plugins.

## Testing checklist

Before opening a PR for a new plugin:

- [ ] App launches successfully with the plugin enabled and disabled.
- [ ] Disabling the plugin at runtime (when supported) returns the app to a clean state.
- [ ] No crashes when invoked against an empty project.
- [ ] No crashes when invoked against a complex project (many layers, all materials).
- [ ] Localization: any user-visible strings use `tr()`.
- [ ] CI passes on all three platforms.
- [ ] Plugin is documented under a new `docs/plugins/<name>.md` (or appended to this guide).
