# Roadmap

A phased plan for ArmorPaint x Malloy. Each phase has a concrete deliverable. The project is currently in **Phase 1**.

Use the checkboxes to track progress; PRs that complete an item should tick it.

---

## Phase 1 — Repo stabilization & documentation

**Goal:** Make the fork buildable, documented, and safe to change.

- [x] Replace root `readme.md` with fork identity
- [x] Update `.github/FUNDING.yml` (don't misdirect upstream donations)
- [x] Add `docs/ARCHITECTURE.md`
- [x] Add `docs/BUILDING.md`
- [x] Add `docs/ROADMAP.md`
- [x] Add `docs/PLUGIN_GUIDE.md`
- [x] Add `docs/FEATURE_IDEAS.md`
- [x] Add `docs/AI_ASSISTED_TEXTURE_WORKFLOW.md`
- [x] Add `docs/MALLOY_LAB_DESIGN.md`
- [x] Add `docs/CI_PLAN.md`
- [x] Add `CHANGELOG.md`
- [x] Add `CONTRIBUTING.md`
- [x] CI: bump `actions/checkout@v3` → `@v4`
- [x] CI: upload build artifacts on all three platforms

**Deliverable:** A clean developer fork with obvious purpose, working CI, and a visible roadmap.

---

## Phase 2 — CI modernization

**Goal:** Catch regressions earlier; reduce CI time.

- [ ] Consolidate three workflows into a single matrix workflow
- [ ] Add `fail-fast: false` so one platform failure doesn't stop the others
- [ ] Cache APT packages (Linux) and Xcode derived data (macOS)
- [ ] Add `clang-format --dry-run --Werror` check (uses existing `.clang-format`)
- [ ] Add Markdown link-check for `docs/` and root `*.md`
- [ ] Add a nightly job that pulls upstream `armory3d/armorpaint` and opens a draft PR if there are changes
- [ ] Add release workflow (tagged releases → upload built binaries)

**Deliverable:** One unified matrix workflow, format-enforced source, and a path to publishing fork releases.

See [CI_PLAN.md](CI_PLAN.md) for the design.

---

## Phase 3 — Malloy branding & Lab tab

**Goal:** Make the fork visibly identifiable inside the app.

- [ ] Implement `paint/sources/tab_malloy_lab.c` per [MALLOY_LAB_DESIGN.md](MALLOY_LAB_DESIGN.md)
- [ ] Surface fork version and build SHA in the Lab tab
- [ ] Show enabled feature flags
- [ ] Show loaded plugins (read from `paint/plugins/plugins.c`)
- [ ] Show renderer/backend info
- [ ] Add experimental feature toggles (gated behind a runtime flag)
- [ ] Document the experimental flags in a new `docs/EXPERIMENTAL_FLAGS.md`
- [ ] Decide on app-displayed identity rename strategy (separate plan)

**Deliverable:** A visible in-app surface for fork-specific systems.

---

## Phase 4 — Presets & game pipeline tools

**Goal:** Add immediate value for game developers.

Existing presets in `paint/assets/export_presets/` already include `unity` and `unreal`. Inventory what's missing before adding more.

- [ ] Survey existing presets and gaps
- [ ] Add Godot export preset
- [ ] Add Blender-compatible PBR export preset
- [ ] Add stylized / hand-painted preset
- [ ] Add Roblox / low-poly preset
- [ ] Add a pre-export texture-naming validator (likely a plugin)
- [ ] Add an export-readiness checklist surfaced from the validator

**Deliverable:** Better defaults for the most common game-asset pipelines.

---

## Phase 5 — Plugin system expansion

**Goal:** Add functionality without breaking core.

- [ ] Extend `PLUGIN_GUIDE.md` with deeper internals (registration flow, lifecycle)
- [ ] Build a minimal sample plugin (`malloy_hello`) as a reference
- [ ] Build `malloy_texture_validator` — see [PLUGIN_GUIDE.md](PLUGIN_GUIDE.md)
- [ ] Build `malloy_batch_export` — apply a preset across many materials/objects
- [ ] Add plugin test harness notes

**Deliverable:** A real extension path with at least two useful custom plugins.

---

## Phase 6 — AI-assisted material workflow

**Goal:** Build on the existing `paint/sources/neural_nodes/` infrastructure carefully.

- [ ] Audit existing neural node coverage; identify gaps
- [ ] Implement AI Material Assistant prototype (prompt → preset)
- [ ] Implement AI Texture Critic prototype (review current material)
- [ ] Implement AI Export Assistant prototype (validate per target engine)
- [ ] Document privacy/security rules (local-first, no implicit upload)
- [ ] Decide on optional cloud-model abstraction

**Deliverable:** A future-facing AI-assisted texturing fork that respects manual artist control.

See [AI_ASSISTED_TEXTURE_WORKFLOW.md](AI_ASSISTED_TEXTURE_WORKFLOW.md) for the design.

---

## Out of scope (probably ever)

- Renderer rewrite
- Switching engine layers off of Iron
- Removing upstream features
- Anything that breaks parity with upstream import/export formats
