# Feature ideas

A grouped backlog. Items here are not commitments — they're a parking lot, sorted by **where they fit** and **how risky they are**.

When you want to work on one, open an issue first. If it survives discussion, it moves to [ROADMAP.md](ROADMAP.md).

## Beginner-safe

Ideas that touch only docs, assets, or self-contained tabs.

- New keymap preset that matches Photoshop
- New keymap preset for Blender 4.x conventions
- Material library — add `default_brush_*.arm` variants for common use cases
- Doc: troubleshooting tree for first-run problems on Windows/Linux/macOS
- Doc: contributor walkthrough that builds and ships a "hello world" plugin
- Asset: example HDRI presets for studio / outdoor / dark scenarios

## App-layer

Self-contained additions to tab files or new tabs.

- Project size budget panel (warn when textures exceed a target)
- Recent files quick-switcher with search
- Per-project notes tab — markdown notes saved alongside `.arm` files
- Layer-tag filter to show/hide groups quickly
- Right-click "duplicate as adjustment" on any layer

## Plugin-layer

New plugins under `paint/plugins/`.

- `malloy_texture_validator` (Phase 5) — see [PLUGIN_GUIDE.md](PLUGIN_GUIDE.md)
- `malloy_batch_export` — apply a preset across all materials in a project
- `io_ktx2` — KTX2 import/export (modern GPU texture container)
- `io_webp` — WebP import/export
- `io_dds` — DDS import/export (legacy game pipelines)
- `io_usdz` — USDZ import (Apple AR pipeline)

## Export pipeline

Survey first — `paint/assets/export_presets/` already includes `unity` and `unreal`.

- Godot export preset (PBR, naming convention)
- Blender Principled BSDF preset
- Stylized / hand-painted preset (matte, no metallic)
- Roblox / low-poly export preset
- Per-engine pre-export validation hook

## UI/UX

Higher risk because users notice immediately.

- "Malloy Lab" tab (Phase 3) — see [MALLOY_LAB_DESIGN.md](MALLOY_LAB_DESIGN.md)
- Optional compact mode for the 2D view
- Inline help overlays for first-time users
- Theme system extension (additional dark variants)

## AI-assisted (future)

Design-only until Phase 6 — see [AI_ASSISTED_TEXTURE_WORKFLOW.md](AI_ASSISTED_TEXTURE_WORKFLOW.md). Note that `paint/sources/neural_nodes/` already implements inpaint, text-to-image, image-to-PBR, depth/normal map generation, upscaling, and more — these features should extend that infrastructure, not duplicate it.

- AI Material Assistant (prompt → material preset)
- AI Texture Critic (review current material)
- AI Export Assistant (validate per target engine)
- AI Brush Generator (procedural brush settings, not images)
- AI Smart Mask Helper (edge wear, dirt, cavity suggestions)

## Deferred / probably never

These would benefit the project but require core engine work that's out of scope for a personal fork.

- New render backend (e.g. WebGPU desktop)
- Rewriting `iron_gpu.*` or `iron_ui.*`
- Replacing the node graph implementation
- Switching the build system off `base/make`
