# Malloy Lab tab (design)

**Status:** Design only. No implementation in Phase 1.

## Purpose

A single in-app surface that shows:

- Fork version, build SHA, build date
- Currently enabled feature flags
- Loaded plugins and their status
- Active renderer / backend
- Toggleable experimental features
- Links to fork documentation

This makes it obvious which build you're running and what's experimental. Today there is no such surface — running this fork looks identical to running upstream.

## Proposed file

`paint/sources/tab_malloy_lab.c`

The naming follows the existing pattern. The 14 sibling tab files are:

```
tab_browser.c    tab_brushes.c     tab_console.c    tab_fonts.c
tab_history.c    tab_layers.c      tab_materials.c  tab_meshes.c
tab_plugins.c    tab_scene.c       tab_scripts.c    tab_swatches.c
tab_textures.c   tab_timeline.c
```

## Pattern to mirror

Study these first, in this order:

1. [`paint/sources/tab_scene.c`](../paint/sources/tab_scene.c) — clean tab that reads global state without modifying much
2. [`paint/sources/tab_plugins.c`](../paint/sources/tab_plugins.c) — already enumerates plugins; the Lab tab will reuse this pattern
3. [`paint/sources/tab_layers.c`](../paint/sources/tab_layers.c) — comprehensive example of UI patterns (`ui_button`, `tr()`, context menus)
4. [`paint/sources/config.c`](../paint/sources/config.c) — how `version`, `sha`, and other build metadata are loaded into `g_config`

From `tab_layers.c`, observed conventions:

```c
#include "global.h"

i32  tab_malloy_lab_some_state = 0;
bool tab_malloy_lab_show_advanced = false;

void tab_malloy_lab() {
    // sections, buttons, labels
    // use tr("display text") for any user-visible string
    // use ui_button, ui_label, ui_check, ui_text from iron_ui
}
```

Headers: there are **no per-tab `.h` files**. Declarations live in `paint/sources/global.h`. The Lab tab follows the same pattern.

## Sections (proposed)

### Build info

| Field | Source |
|---|---|
| Fork name | hardcoded "ArmorPaint x Malloy" |
| Fork version | `g_config->version` (already wired) |
| Build SHA | `g_config->sha` (already wired) |
| Build date | new field — needs to be set at build time in `paint/project.js` |
| Upstream baseline | git tag or commit of the upstream merge point |

### Feature flags

Read from `paint/project.js` flags. The build script already exposes physics, compression, raytrace, plugins, Kong. Add a "fork experimental" flag block.

### Loaded plugins

Reuse the enumeration in [`tab_plugins.c`](../paint/sources/tab_plugins.c). Show each plugin name, source path, enabled state.

### Renderer / backend

Iron knows the active backend. Surface it from the appropriate `base/sources/` API (one of `iron_gpu.*`).

### Experimental toggles

Each toggle:
- Has a name and description
- Persists into `config.json` via [`config.c`](../paint/sources/config.c) (add a `g_config->experimental_*` field per toggle)
- Is read by the relevant feature at runtime
- Defaults to off

A first toggle to scaffold the pattern: `experimental_malloy_lab` itself — turning it off hides the tab, useful for verifying tab registration code is clean.

### Documentation links

In-app links (open in default browser):
- `readme.md` on the GitHub repo
- `docs/ROADMAP.md`
- `docs/FEATURE_IDEAS.md`
- A "What's new" entry pointing at `CHANGELOG.md`

## Registration

Tabs are registered somewhere central — most likely in a tab list initialized at app startup. **Find the registration before implementing**; do not guess. Trace from any existing `tab_*()` function back through `grep` for its name to find the registration site.

## What this design does not cover

- Exact UI layout (waits for first prototype)
- Hot-reload of plugins from the Lab tab — that's a Phase 5 plugin-system concern
- Telemetry — there is none; this tab is purely an informational surface for the user

## Implementation checklist (Phase 3)

- [ ] Find the tab registration site
- [ ] Create `paint/sources/tab_malloy_lab.c` with stub `tab_malloy_lab()`
- [ ] Register the tab
- [ ] Confirm it builds on all three platforms (CI)
- [ ] Add build-info section
- [ ] Add plugins section (reuse `tab_plugins.c` enumeration)
- [ ] Add renderer/backend section
- [ ] Add first experimental toggle (`experimental_malloy_lab`)
- [ ] Wire toggle into `config.c` (load/save)
- [ ] Add documentation links section
- [ ] Update `docs/ROADMAP.md` Phase 3 checkboxes
- [ ] Add `## [Unreleased]` entries to `CHANGELOG.md`
