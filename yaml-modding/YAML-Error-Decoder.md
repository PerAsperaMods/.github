# YAML Mod Error Decoder

> The game's YAML loader fails **silently or far from the cause**. This table maps every
> known symptom to its actual root cause and fix. Symptoms confirmed in-game on
> patch 1.8.x (BepInEx log: `Per Aspera/BepInEx/LogOutput.log`; vanilla player log:
> `%USERPROFILE%\AppData\LocalLow\Tlön Industries\Per Aspera\Player.log`).
>
> Background on *why* errors behave this way: [YAML-Modding-Internals](YAML-Modding-Internals.md).

---

## Quick triage flowchart

```
Mod seems to load but nothing changes        → §1 Silent ignores
Game crashes/throws WHILE loading mods       → §2 Parse & deserialize errors
Game throws AFTER loading (scene/first frame)→ §3 Delayed errors (the nasty ones)
Game runs but logs spam / visuals broken     → §4 Non-fatal degradations
```

---

## §1 — Silent ignores (no error at all)

| Symptom | Root cause | Fix |
|---------|-----------|-----|
| Entire data file never applies | **Misspelled manifest section** (`buildings:` instead of `building:`) — SharpYaml ignores unknown `ModManifest` fields without warning | Section names must exactly match the `ModManifest` field list — see [Internals §2](YAML-Modding-Internals.md#2-the-manifest-manifestyaml) |
| One field has no effect | **Misspelled field name** — unknown fields on a data entry are silently dropped | Check the field against the generated [schema](schemas/README.md) for that type |
| Whole mod skipped | A `requiredMods` entry is absent / wrong `modId` casing | IDs are compared as strings; match the other mod's `modId` exactly |
| `optionalManifests` file skipped | Same mechanism — its own `requiredMods` not satisfied | Intentional feature; verify the dependency is loaded *before* this mod |
| Field set in YAML but value differs in game | `PostInitialize()` or game code overwrites it at runtime (field is 🟡 "public non-annotated" in the schema docs) | Check the schema status column; if 🟡 and overwritten, you need a Harmony patch instead |

---

## §2 — Errors during mod loading (loud, near the cause)

| Log line | Root cause | Fix |
|----------|-----------|-----|
| `SharpYaml...YamlException ... line X` | Real syntax error: bad indentation, orphan `replace: false` after a commented-out section, stray tab in a *structural* position | Fix the YAML; comment/uncomment manifest sections as whole blocks |
| `YamlException: ... Unable to convert 'Foo' to enum EnumName` | Wrong enum value (e.g. `materialType: placeholder` — case matters) | Use exact enum value names from the [schema](schemas/README.md) enum sections |
| `YamlException: ... type mismatch / cannot convert scalar` | Scalar where the C# field expects a mapping/list (or vice-versa) | Compare with the field's C# type in the schema |
| `KeyNotFoundException` during load | Duplicate top-level key in the same file, or `!replace` on a key that doesn't exist yet (load order) | Keys must be unique per table; patch files must load after the entry exists |

---

## §3 — Delayed errors (thrown long after the bad YAML)

These are the infamous "NullReferenceException with nothing else". The stack trace
points at game code, not at your file. Decode by *when* and *where* they fire:

| Symptom | Actual root cause | Fix |
|---------|------------------|-----|
| `NullReferenceException` at end of loading / scene switch, stack mentions `PostInitialize`, `GenerateIconsAtlas`, or a `*Type` class | A `!tag` reference points to a key that **no loaded file defines** → placeholder never resolved, field left null | Run the validator (`Tools/validate_yaml_mods.py`) — its cross-reference check catches 100 % of these before launching the game |
| `NullReferenceException` when opening the build menu / a UI panel | A building/resource field needed by UI is null: missing `iconName` sprite (loaded as null), missing `categoryType`, or `name` missing localization | Validate sprites exist; every building needs a valid `categoryType` |
| `ArgumentException: Object to instantiate is null` (placing a building / spawning a resource cube) | `prefabName` doesn't exist, or doesn't match the `materialType` family (e.g. flying-drone prefab on a Manufactured resource) | Use a vanilla prefab of the same kind; `ModBuildings/...` paths do NOT resolve from YAML on 1.8.x |
| `IndexOutOfRangeException` during world generation | Custom resource with `materialType: Mined`/`Manufactured` → scatter/vein system indexes vanilla-sized arrays | Custom resources must use `materialType: Placeholder` |
| `ArgumentOutOfRangeException` opening build panel | A **vanilla** building's `outputResource` was patched to a **mod** resource — display tables are sized for vanilla resource indices | Never patch vanilla `outputResource` to a mod resource; create a new building |
| Corrupted/blank save, or entries swapped after game update | `index` manually set on existing entries, or collided with another mod | Never set `index`; let the game auto-assign |
| `NullReferenceException` only when a specific quest/rule/event fires | Bad `command`/`arguments` in an `actions:` block, or criterion referencing an undefined blackboard key | Test rules with `eventType: GevUniverseNewGameStarted` + immediate criterion to fail fast |

**Rule of thumb:** any NRE whose stack does not include your plugin's namespace, occurring
after mods load, is a data problem — start with the validator, not the debugger.

---

## §4 — Non-fatal degradations

| Log line | Cause | Fix |
|----------|-------|-----|
| `Sprite at path X couldn't be found` (often flooding) | `iconName`/`imageName`-type field with a wrong path; the field is typed `Sprite` in C# and resolved at load | Resource icons need the `Resource Icons/` prefix; build icons `BuildIcons/`; custom sprites must exist inside the mod folder |
| Name shows as `MY_KEY_NAME` raw | Localization key has no entry in any loaded CSV | Add the key to the mod's `languages:` CSV, or use plain text |
| Building buildable but invisible in a category | `categoryType` references a `hidden: true` category | Point at a visible category |

---

## Debugging workflow that actually works

1. **Validate before launching** — `python Tools/validate_yaml_mods.py --mod MyMod`
   (schema + cross-reference checks; seconds instead of a 2-minute game boot).
2. **Bisect with `optionalManifests`** — split content into sub-manifests so you can
   disable halves of the mod without editing data files.
3. **Watch the log live** — `Tools/Watch-BepInXLogs.ps1`, or the ModDevHelper plugin's
   post-load audit (`sdk-digest.txt` / `sdk-errors.txt`) which reports unresolved
   references with names.
4. **Dump the merged state** — (planned tooling) call `YAMLLoader.DumpTable` from a
   plugin to see exactly what the game ended up with after all merges.
