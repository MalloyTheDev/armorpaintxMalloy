# ArmorPaint x Malloy

A personal experimental fork of [ArmorPaint](https://github.com/armory3d/armorpaint) focused on improved developer workflow, modern UI/UX experiments, asset-pipeline improvements, plugin-first extensibility, and future AI-assisted 3D texturing tools.

> **This is not the official ArmorPaint.** For the upstream project, official releases, and the paid distributions that fund ongoing development, visit [armorpaint.org](https://armorpaint.org) and the canonical repository at [armory3d/armorpaint](https://github.com/armory3d/armorpaint).

## Goals

- Game-development texture workflow improvements (Unreal, Unity, Godot, Blender pipelines)
- Better export presets and naming conventions
- Plugin-first experimentation — extend the app without surgery on core
- UI/UX experiments contained behind a dedicated "Malloy Lab" surface
- A careful, opt-in AI-assisted material/texturing workflow built on the existing [`paint/sources/neural_nodes/`](paint/sources/neural_nodes/) infrastructure

## Documentation

- [Building](docs/BUILDING.md) — platform-specific build steps and troubleshooting
- [Architecture](docs/ARCHITECTURE.md) — folder map and risk zones
- [Roadmap](docs/ROADMAP.md) — phased plan
- [Plugin guide](docs/PLUGIN_GUIDE.md) — how plugins work here
- [Feature ideas](docs/FEATURE_IDEAS.md) — backlog by category and risk
- [AI-assisted texture workflow](docs/AI_ASSISTED_TEXTURE_WORKFLOW.md) — design notes
- [Malloy Lab design](docs/MALLOY_LAB_DESIGN.md) — future in-app tab spec
- [CI plan](docs/CI_PLAN.md) — automation roadmap
- [Contributing](CONTRIBUTING.md)
- [Changelog](CHANGELOG.md)

## Quick build

See [docs/BUILDING.md](docs/BUILDING.md) for the full guide. Short version, from the [`paint/`](paint/) directory:

| Platform | Command |
|---|---|
| Windows | `..\base\make` then open `build\ArmorPaint.sln` |
| Linux | `../base/make --run` |
| macOS | `../base/make` then open `build/ArmorPaint.xcodeproj` |
| Android | `../base/make --target android` |
| iOS | `../base/make --target ios` |
| WASM | `../base/make --target wasm --compile --embed` |

Requires a C23 `#embed`-capable compiler (clang 19+).

## License

[zlib/libpng](license.md), inherited from upstream ArmorPaint. This is a personal altered fork; any future source modifications will be plainly marked as required by the license. The original software's authors retain full credit — nothing in this fork claims authorship of the upstream engine or application.

## Credits

All upstream ArmorPaint and Iron engine work belongs to the original authors. See [armory3d/armorpaint](https://github.com/armory3d/armorpaint) for the canonical project. Fork-specific changes are tracked in [CHANGELOG.md](CHANGELOG.md).
