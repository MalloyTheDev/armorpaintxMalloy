# Contributing to ArmorPaint x Malloy

Thanks for your interest. A few things to know before you open a PR.

## What this repo is

This is **a personal experimental fork** of [ArmorPaint](https://github.com/armory3d/armorpaint), maintained by one person for personal projects. It is not the official ArmorPaint.

Most contributions that improve the core painting application, fix engine bugs, or add broadly useful features belong **upstream**. Please open issues and PRs at [armory3d/armorpaint](https://github.com/armory3d/armorpaint) first.

This fork exists to experiment with:
- Game-development texture pipelines (Unreal, Unity, Godot, Blender)
- Export presets and texture validators
- Plugin-first features
- A future AI-assisted material/texturing workflow

If your contribution aligns with those goals, you're in the right place.

## Before you start

1. Read [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) to find the **safe**, **medium-risk**, and **high-risk** zones of the codebase. Default to safe zones unless you have a specific reason to go deeper.
2. Read [docs/ROADMAP.md](docs/ROADMAP.md) to see what phase the project is in. Out-of-phase work is unlikely to be merged.
3. Open an issue first for anything beyond a small fix. Larger changes need design alignment.

## Pull requests

- Keep PRs **focused** — one concern per PR.
- Don't reformat unrelated files. `.clang-format` exists at the repo root; respect it.
- **Don't touch `license.md`** — the zlib license explicitly forbids alteration of its notice.
- If you modify C source under `paint/sources/` or `base/sources/`, add an entry to [CHANGELOG.md](CHANGELOG.md) under `## [Unreleased]`.
- New plugins go under `paint/plugins/`. See [docs/PLUGIN_GUIDE.md](docs/PLUGIN_GUIDE.md).
- All three CI workflows (Linux Vulkan, Windows Direct3D12, macOS Metal) must pass.

## Style

- C source: follow the existing pattern. Globals are explicit (no hidden state via macros). Use `tr()` for any UI string, not raw literals.
- Documentation: prefer plain prose over jargon. Cross-link aggressively. Avoid duplicating content from upstream `paint/readme.md`.
- Commit messages: present-tense imperative, scoped (e.g. `docs: add plugin guide`, `paint: fix layer ordering`, `ci: bump checkout to v4`).

## Expectations

This is a personal project. Review may be slow, and not every PR will be merged. If you want a faster path to impact, upstream contributions almost always make more sense.

## License

By contributing, you agree that your contributions are licensed under the same [zlib/libpng license](license.md) as the rest of the project.
