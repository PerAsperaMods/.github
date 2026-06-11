# YAML Modding Tooling — Critical Review & Roadmap

> Honest assessment (June 2026) of the YAML tooling around Per Aspera modding, and the
> prioritized plan to make YAML modding *actually* easy. The core problem statement:
> **the game gives no usable error messages** (silent ignores, delayed NREs), so every
> hour invested in *pre-flight validation* and *post-load introspection* saves ten
> hours of in-game bisecting.

Related: [Internals](YAML-Modding-Internals.md) · [Error Decoder](YAML-Error-Decoder.md) ·
[Schema Reference](schemas/README.md)

---

## 1. What exists today — critical state

### `Tools/validate_yaml_mods.py` (validator) — ★ the right idea, incomplete coverage

**Good:** parses game tags, handles tabs, checks field names and cross-references,
catches the #1 killer (unresolved `!tag` refs) before launching the game.

**Critical gaps:**

| Gap | Impact |
|-----|--------|
| Hand-written schemas for only **6 of 25+ loadable types** (building, resource, technology, knowledge, enhancements, buildingCategory) | quest/rule/project/popup/scatter/poi errors — the most error-prone types — are unchecked |
| Schemas hardcoded in the script | drift from the game on every patch; nobody will maintain 25 hand-written field lists |
| No manifest-section validation | the #1 silent failure (misspelled section like `buildings:`) is not caught |
| No enum value checking | `materialType: placeholder` (wrong case) passes validation, crashes in game |
| No sprite/prefab existence check | `Sprite not found` floods and `Object to instantiate is null` not caught |
| Known crash rules (R001 `materialType: Placeholder`, R003 vanilla `outputResource` patch…) not encoded | the most expensive known crashes are documented in skills but not enforced |

**→ Fix direction:** the validator must consume **`Tools/yaml-schemas.json`**, generated
from the decompiled game by `Tools/extract_yaml_schemas.py` (done — June 2026). One
command regenerates ground truth after every game patch; coverage jumps from 6 to all
36 root types (25 table types + 10 setups + manifest) with zero hand maintenance.

### `E:\per-aspera-mod-creator` (web app) — ★ ambitious, wrong layer first

**Good:** full-stack mod editor with DB, admin panel, 495 vanilla items imported.

**Critical issues:**

1. **It re-implements the YAML pipeline** (TypeScript regex parser → failed; Python
   PyYAML loader → works) instead of sharing one schema source. Result: three parallel
   schema definitions exist (game C#, validator, mod-creator DB) and all three disagree.
2. **Database-first design adds friction**: YAML in → SQL → YAML out risks losing
   tags, comments, and field ordering; round-trip fidelity is unproven.
3. **Effort is going into data *import* completeness (Phases 1-5) before the export
   path** — but a mod creator's only real success metric is "generates a mod that loads".
4. The integration guides (YAML-INTEGRATION-GUIDE.md, DATAMODEL-INTEGRATION-PLAN.md)
   track item counts (495/650/2000) — a vanity metric. 100 % of vanilla data imported
   with no validated export is 0 % of a mod creator.

**→ Fix direction:** make `yaml-schemas.json` the single shared schema contract.
The mod-creator backend should *generate forms from it* (types, enums, references) and
*validate exports with the same validator*. Drop the goal of mirroring all vanilla data
in SQL; load vanilla YAML read-only for reference/autocomplete instead.

### ModDevHelper (BepInEx plugin) — ★ right concept, underused

Post-load audit (R1–R4 rules, `sdk-digest.txt`) exists, but it doesn't yet exploit the
single biggest discovery in the loader: **`YAMLLoader.DumpTable<T>` / `DumpInstance<T>`
ship in the game** and can serialize the fully-merged datamodel back to YAML.

### Documentation — fixed by this doc set

Until now the knowledge lived in one skill (`/per-aspera-yaml-modding`, good but
example-based) and scattered crash notes. What was missing — *how the pipeline works*,
*why errors are silent*, *the complete field truth* — is now:
`YAML-Modding-Internals.md`, `YAML-Error-Decoder.md`, `schemas/` (generated).

---

## 2. Roadmap — priority order

### P0 — Wire the generated schemas into the validator *(highest value/effort ratio)*

1. `validate_yaml_mods.py`: load `Tools/yaml-schemas.json`; delete the hardcoded
   `BUILDING_FIELDS`/`RESOURCE_FIELDS`/… blocks.
2. Add **manifest section validation** against `manifest_sections` in the JSON
   (unknown section = ERROR, the game's silent ignore becomes loud).
3. Add **enum value validation** (`enum_values` are in the JSON).
4. Encode the known crash rules as first-class checks: R001 (custom resource must be
   `Placeholder`), R003 (vanilla `outputResource` → mod resource), prefab/materialType
   family mismatch, `index` set on existing entries.
5. Regeneration ritual after each game patch:
   `Generate-InteropDump.ps1` → `extract_yaml_schemas.py` → commit JSON + schemas.

*Estimated effort: one session. Eliminates ~80 % of in-game trial-and-error.*

### P1 — Merged-datamodel dump via ModDevHelper

Add a ModDevHelper command/config flag that, after `CompleteLoading()`, calls the
game's own `DumpTable<T>` for every table into `BepInEx/DataDump/<type>.yaml`.

- Ground truth of *applied* state (after all merges/patches) — ends "did my `!replace`
  apply?" debugging forever.
- Diff vanilla-only dump vs. mods-enabled dump = exact effect of your mod set.
- The dump also reveals canonical serialized syntax for every field — feeds back into
  schema docs (defaults!), which static stubs can't provide.

*Estimated effort: one session (typed interop call per table type, or one generic
reflection loop). This is the single most powerful debugging tool possible here.*

### P2 — Loud-loader patch (turn silent failures into log lines)

Small Harmony patch set (candidate: ModDevHelper):

- Postfix `ConnectCrossReferencePlaceholders` → log every placeholder that stayed
  unresolved **with its key and the referencing entry**. Converts the worst delayed-NRE
  class into a named, actionable error.
- Postfix `LoadManifest` → log each section loaded with file count; warn when a
  manifest key didn't match any `ModManifest` field (read raw YAML keys, compare).

### P3 — Mod-creator realignment

1. Backend consumes `yaml-schemas.json` (forms, enums, `!tag` pickers from loaded
   vanilla keys).
2. Export path: generate manifest + data files → run validator → show errors in UI.
   *Ship this before importing more vanilla data.*
3. Sprite browser from `F:\ModPeraspera_Raw_Extrac\extrat\Sprite` (1 932 PNGs) with
   the correct path prefixes (`Resource Icons/`, `BuildIcons/`…) — kills the icon
   guessing game.

### P4 — Authoring comfort

- VS Code: JSON-schema-like completion is impossible for YAML custom tags, but a
  generated `yaml.customTags` list (all 25+ tags) and per-type snippet files
  (from the generated templates) are easy wins.
- `deploy-yaml-mods.ps1`: chain validator before deploy (refuse to deploy on ERROR,
  `-Force` to override).

---

## 3. Architecture target (one schema to rule them all)

```
Game patch
   │ Generate-InteropDump.ps1 / Il2CppDumper
   ▼
F:\ModPeraspera_Raw_Extrac (stubs with [YamlMember]/[GameStaticData])
   │ Tools/extract_yaml_schemas.py            ← single source of truth generator
   ▼
Tools/yaml-schemas.json ──────────────┬──────────────────┬─────────────────────┐
   │                                  │                  │                     │
   ▼                                  ▼                  ▼                     ▼
Organization-Wiki/yaml-modding/   validate_yaml_mods   per-aspera-mod-creator  VS Code
schemas/*.md (docs/templates)     (pre-flight checks)  (forms + export)        (snippets/tags)
```

Every consumer regenerates from the same JSON after a game patch. No hand-maintained
field list anywhere.

---

## 4. On "a dedicated workspace + Claude skills for other modders"

Recommendation: **don't fork a separate workspace yet.** The leverage is in shipping
the P0–P2 tooling and this wiki; a separate repo would duplicate skills that still
change weekly. Instead:

1. Keep `Organization-Wiki` as the public, self-contained face (it already is a git
   repo) — these YAML docs live here precisely so they can be published alone.
2. When P0–P2 are stable, extract a lean **starter kit repo** for modders:
   `validate_yaml_mods.py` + `yaml-schemas.json` + `deploy-yaml-mods.ps1` + wiki link +
   a `.claude/skills/per-aspera-yaml-modding` copy. That's the whole YAML modder
   experience in one clone — no SDK, no game dumps, no internal docs needed.
3. Claude skills for outsiders should be *thin* (point at the wiki + validator), not
   copies of internal skills that reference `F:\` paths.
