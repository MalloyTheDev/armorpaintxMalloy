# Changelog

All notable changes to **ArmorPaint x Malloy** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This project is a fork of [ArmorPaint](https://github.com/armory3d/armorpaint); entries below describe **fork-specific** changes only. For upstream history, see the canonical repository.

## [Unreleased]

### Added
- Fork identity: new root `readme.md` introducing "ArmorPaint x Malloy" and clearly distinguishing this work from upstream ArmorPaint.
- `docs/` directory with foundation documents:
  - `ARCHITECTURE.md` — folder map and risk zones for `base/` (Iron engine) and `paint/` (app)
  - `BUILDING.md` — expanded build instructions per platform, with troubleshooting and CI notes
  - `ROADMAP.md` — phased plan (stabilization → branding → presets → plugins → AI workflow)
  - `PLUGIN_GUIDE.md` — plugin architecture overview and first custom plugin proposal
  - `FEATURE_IDEAS.md` — grouped backlog by category and risk
  - `AI_ASSISTED_TEXTURE_WORKFLOW.md` — design-only notes for AI-assisted material tooling
  - `MALLOY_LAB_DESIGN.md` — design-only spec for a future in-app "Malloy Lab" tab
  - `CI_PLAN.md` — modernization proposal (matrix, caching, format check, release)
- `CONTRIBUTING.md` at repo root with fork-specific contribution guidance.
- CI: `actions/checkout` upgraded from `v3` to `v4` across all three platform workflows.
- CI: build artifact upload for `paint/build/` on all three platforms.

### Changed
- `.github/FUNDING.yml` no longer redirects to the upstream `armorpaint.org/download` link. Donations should go to upstream directly, not via this fork.

### Unchanged (deliberately)
- `license.md` — zlib/libpng license preserved verbatim. The license forbids alteration of its notice.
- `paint/readme.md` — upstream build instructions remain authoritative; `docs/BUILDING.md` cross-links rather than duplicating.
- All source code under `base/` and `paint/sources/`. No app behavior changes in this entry.
