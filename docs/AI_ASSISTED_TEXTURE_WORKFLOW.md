# AI-assisted texture workflow (design)

**Status:** Design only. No implementation in Phase 1.

## Existing foundation

The upstream codebase already contains substantial neural infrastructure under [`paint/sources/neural_nodes/`](../paint/sources/neural_nodes/):

| File | Capability |
|---|---|
| `neural_node.c` | Core neural node infrastructure |
| `neural_node_models.c` | Model registry |
| `text_to_image_node.c` | Generate textures from prompts |
| `inpaint_image_node.c` | Inpainting (fill regions) |
| `outpaint_image_node.c` | Outpainting (extend canvas) |
| `image_to_pbr_node.c` | Photo → PBR map decomposition |
| `image_to_depth_node.c` | Depth estimation |
| `image_to_normal_map_node.c` | Normal map estimation |
| `image_to_3d_mesh_node.c` | Image → mesh |
| `vary_image_node.c` | Variations |
| `tile_image_node.c` | Tileable conversion |
| `edit_image_node.c` | Targeted edits |
| `upscale_image_node.c` | Super-resolution |

**Implication:** The fork's AI workflow extends this. It does **not** introduce a parallel neural stack. Any new feature should be implementable as either (a) a new neural node, (b) a higher-level orchestration of existing nodes, or (c) a UI surface that exposes existing nodes more ergonomically.

## Design principles

1. **Manual artist control first.** AI features are assistants, not authors. Every AI suggestion is a draft that the user accepts, edits, or discards. The destination is always a regular layer/material the user owns.
2. **Local-first by default.** Where the existing neural nodes run locally, new features should too. Cloud models are opt-in per session, with a clear indicator that data is leaving the machine.
3. **Transparency.** Every AI-produced layer is tagged in its metadata with the source model, prompt, and timestamp. Reproducibility matters.
4. **No silent network calls.** A fork-wide offline switch (Phase 3) disables every feature that would reach the network.
5. **No vendor lock.** Model integration goes through a thin abstraction so swapping providers (or going fully local) is a config change, not a refactor.

## Proposed features

### AI Material Assistant

User types `"rusty scratched sci-fi metal"`; produces a draft material — albedo, roughness, metallic, normal, height — wired into the node graph. User adjusts.

Built on: `text_to_image_node` (albedo) → `image_to_pbr_node` (decompose) → `image_to_normal_map_node` and `image_to_depth_node` (refine).

### AI Texture Critic

Reviews the active material and reports:
- Flat or near-flat roughness / metallic maps
- Normal map strength outliers (over-baked / under-baked)
- Missing AO when one would matter
- Tiling artifacts at common UV seams

Built on: existing model inference; no generation needed. Outputs a structured report alongside `malloy_texture_validator` (see [PLUGIN_GUIDE.md](PLUGIN_GUIDE.md)).

### AI Export Assistant

User selects target engine (Unity, Unreal, Godot, Blender). The assistant checks naming, map presence, and color-space conventions for that target, then proposes corrections. Pairs with `malloy_texture_validator` — validator finds issues, assistant proposes fixes.

### AI Brush Generator

Generates **brush parameter sets**, not pixels. The user describes intent ("a rough chalk that flickers at edges") and gets brush settings (flow, opacity jitter, spacing, falloff, alpha texture choice) wired into a new brush preset.

Built on: small parameter model, not image generation. May not need a new neural node — could be a lookup against curated presets.

### AI Smart Mask Helper

Suggests procedural masks based on mesh + active layers: edge wear, cavity dirt, ambient occlusion, height-driven gradients. Surfaces existing capabilities more ergonomically.

## Privacy and security rules

Non-negotiable for any feature that ships under this design:

- **No telemetry tied to user content.** Crash reports may include stack traces; never material data, prompts, or output images.
- **Network access requires explicit user consent per session.**
- **Models bundled with the app must be reproducible** — link to their source, document their license, include their hash.
- **User prompts are never logged to disk by default.** A session log opt-in exists for debugging.
- **Cloud-mode features must display a persistent "online" indicator** while the network connection is active.

## What this design does not specify

- Specific model choices (those evolve faster than this design)
- Specific UX placements (waits for [MALLOY_LAB_DESIGN.md](MALLOY_LAB_DESIGN.md) and Phase 3)
- Pricing or licensing of optional cloud providers
- Performance budgets (depends on chosen models)

These will be decided at the start of Phase 6.
