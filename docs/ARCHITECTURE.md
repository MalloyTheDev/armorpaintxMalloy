# Architecture

A folder map of ArmorPaint x Malloy with risk-zone guidance for contributors.

## High-level

| Layer | Folder | What lives here |
|---|---|---|
| Engine | `base/` | The Iron engine — low-level C, GPU backends, UI primitives, math, file I/O, input, system code |
| Application | `paint/` | ArmorPaint app on top of Iron — painting, materials, layers, import/export, UI panels |

## `base/` — Iron engine

```
base/
├── assets/         Engine-side default assets
├── docs/           Engine-level docs (upstream)
├── shaders/        Engine shaders
├── sources/        Engine C source
│   ├── backends/   Platform-specific: direct3d12, metal, vulkan, webgpu, ...
│   ├── iron_gpu.*  GPU abstraction
│   ├── iron_ui.*   UI primitives (immediate-mode)
│   ├── iron_ui_ext.c, iron_ui_nodes.c
│   └── ...
├── tests/          Engine smoke tests (cube, fall, triangle)
├── tools/          Build helpers, locale extractor, embed tools
├── make / make.bat / make.exe   Build entry points
└── project.js      Engine build configuration
```

## `paint/` — ArmorPaint application

```
paint/
├── assets/         Default data shipped with the app
│   ├── export_presets/   8 presets: base_color, generic, minecraft_mer, specular,
│   │                     unigine, unity, unreal, xplane
│   ├── keymap_presets/   blender, touch
│   ├── plugins/          Asset-side plugin metadata
│   ├── locale/           10+ localization JSON files
│   ├── meshes/, icons*.png, default_brush.arm, default_material.arm, world HDRs
│   └── ...
├── plugins/        First-class plugins (IO + utilities)
│   ├── io_exr, io_fbx, io_gltf, io_psd, io_svg, io_tiff
│   ├── uv_unwrap
│   └── plugins.c, project.js
├── shaders/        App-level shaders
├── sources/        App C source
│   ├── tab_*.c          14 tab implementations (layers, materials, scene, ...)
│   ├── nodes_brush.c, nodes_material.c
│   ├── render_path_*.c  7 render paths (base, deferred, forward, paint, preview,
│   │                    raytrace, raytrace_bake)
│   ├── import_*.c       ~13 importers (arm, blend, obj, fbx, gltf, ...)
│   ├── export_*.c       6 exporters
│   ├── make_*.c         ~13 make-helpers (bake, brush, paint, sculpt, ...)
│   ├── neural_nodes/    13 neural node implementations (inpaint, text-to-image,
│   │                    image-to-PBR, depth/normal maps, upscale, ...)
│   ├── config.c, context.c, strings.c, translator.c, plugin.c
│   └── global.h         Shared header used by every translation unit
├── readme.md       Upstream build instructions (authoritative)
└── project.js      App build configuration
```

## Risk zones

The codebase is large. Be deliberate about where you make changes.

### Safe (beginner-friendly)

Changes here rarely break compilation or runtime behavior beyond a single feature.

- `paint/assets/export_presets/` — JSON, additive
- `paint/assets/keymap_presets/` — JSON, additive
- `paint/assets/plugins/` — plugin manifests
- `paint/sources/tab_*.c` — each tab is largely self-contained
- `paint/sources/config.c` — append a new config field, follow JSON patterns
- `paint/plugins/<new>/` — new plugins are isolated by directory

### Medium

Touching these can cascade. Read related files first.

- `paint/sources/make_*.c` — interact with render paths and the GPU pipeline
- `paint/sources/render_path_*.c` — order-of-passes is load-bearing
- `paint/sources/nodes_*.c` — node graph behavior; UI nodes and material logic are coupled
- `paint/sources/import_*.c`, `export_*.c` — format-specific; mistakes corrupt user files

### High risk

Avoid unless you have engine experience or a clear plan.

- `base/sources/backends/` — platform-specific (D3D12, Metal, Vulkan, WebGPU)
- `base/sources/iron_gpu.*` — GPU abstraction
- `base/sources/iron_ui.*` — UI primitives used everywhere
- `base/project.js`, `paint/project.js` — build configuration
- Any low-level memory, library, or compiler integration code

## Recommended workflow for new contributors

1. Get a clean build first — see [BUILDING.md](BUILDING.md).
2. Pick a task from [ROADMAP.md](ROADMAP.md) Phase 1 or a "beginner-safe" item in [FEATURE_IDEAS.md](FEATURE_IDEAS.md).
3. If it's a plugin idea, read [PLUGIN_GUIDE.md](PLUGIN_GUIDE.md) first.
4. Make the change on a feature branch. Confirm all three CI workflows pass before opening a PR.
5. Add an entry to [CHANGELOG.md](../CHANGELOG.md) under `## [Unreleased]` for any user-visible change.
