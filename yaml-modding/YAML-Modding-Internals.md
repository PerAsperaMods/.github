# How Per Aspera YAML Modding Actually Works

> Reverse-engineered from the decompiled game code (`PerAspera.YAML` namespace, patch 1.8.x).
> This is the authoritative explanation of the loading pipeline — read this before
> debugging any "silent" NullReferenceException.
>
> Companion docs: [Schema Reference](schemas/README.md) (every type, every field) ·
> [Error Decoder](YAML-Error-Decoder.md) · [Tooling Roadmap](YAML-Tooling-Roadmap.md)

---

## 1. The big picture

Per Aspera's entire datamodel (buildings, resources, technologies, quests…) is loaded
at startup from YAML files by a single class: **`PerAspera.YAML.YAMLLoader`**, built on
the **SharpYaml** library with game-specific extensions.

```
Game start
   │
   ├─ YAMLLoader.Init()
   │    ├─ CollectYamlTypeInfo()         ← reflection scan, builds the type registry
   │    │     • every class with [GameStaticData]  → "table item" (has a YAML tag)
   │    │     • every class extending YAMLSetup<T> → "setup singleton"
   │    └─ CustomTypeConverter registered ← handles !resource / !building / … tags
   │
   ├─ LoadManifest(core datamodel)        ← datamodel/manifest.yaml (modId: Core)
   ├─ LoadManifest(each DLC)              ← e.g. BlueMars/manifest.yaml
   ├─ LoadManifest(each mod, in order)    ← StreamingAssets/Mods/<Name>/manifest.yaml
   │     └─ optionalManifests loaded if their requiredMods are all present
   │
   └─ CompleteLoading()
         ├─ ConnectCrossReferencePlaceholders()  ← resolves every !tag reference
         └─ PostInitialize() / AssignMaxIndex()  ← per-type finalization (indices, atlases)
```

Three facts fall out of this design that explain 90 % of modding pain:

1. **References are resolved late.** While a file is being parsed, `!resource resource_x`
   does NOT look anything up — it creates a *placeholder* object. All placeholders are
   swapped for real objects only at `CompleteLoading()`. A typo in a reference therefore
   never fails at parse time; it fails much later as a bare `NullReferenceException`
   with no filename and no line number.
2. **The schema is the C# class, not a spec.** Deserialization is driven by reflection
   over fields annotated `[YamlMember]` (any visibility — including `private`) plus
   SharpYaml's default conventions for plain public members. There is no validation
   layer: an unknown field name is silently ignored, a wrong enum value throws deep
   inside SharpYaml.
3. **Asset names are resolved during deserialization.** Fields typed `Sprite` in C#
   (e.g. `iconName`) are declared as plain strings in YAML; the converter loads the
   sprite immediately and logs `Sprite at path X couldn't be found` if missing —
   non-fatal, but it leaves a `null` sprite that can crash UI code later.

---

## 2. The manifest (`manifest.yaml`)

Deserialized into `ModManifest`. The **complete** field list (several are absent from
all community docs so far):

| Field | Type | Notes |
|-------|------|-------|
| `modId` | string | Unique ID, checked by `requiredMods` of other mods |
| `compatibleGameVersions` | string[] | e.g. `["1.8.x"]` |
| `requiredMods` | string[] | Mod is skipped if any is missing |
| `optionalManifests` | string[] | Sub-manifests, each loaded only if *its* `requiredMods` are present |
| `generalSetup`, `initialSetup`, `droneSetup`, `scatterSetup`, `frontendSetup`, `planetSetup`, `combatSetup`, `waySetup`, `maintenanceSetup` | string | Single filename each → a `YAMLSetup<T>` singleton |
| `building`, `resource`, `technology`, `knowledge`, `buildingCategory`, `enhancements`, `drone`, `way`, `poi`, `site`, `quest`, `project`, `rule`, `randomEvent`, `popup`, `tooltip`, `hazardAsteroid`, `hazardDevil`, `hazardSandstorm`, `river`, `lake`, `terraformingPlanCategory`, `terraformingGraphSettings`, `terraformingProjectSettings`, `languages` | `ModFileList<T>` | `{ filenames: [...], replace: bool }` |
| `person` | `ModFileList<Person>` | Characters (campaign) |
| `aiplayer` | `ModFileList<AIPlayerType>` | AI opponents |
| `behavior` | `ModFileList<BehaviorTreeDefinition>` | AI behavior trees — **moddable in pure YAML** |
| `perspective` | `ModFileList<Perspective>` | AI perspectives (campaign dialogue AI) |
| `transition` | `ModFileList<TransitionTable>` | AI transition tables |

`ModFileList<T>` has exactly two fields:

```yaml
building:
  filenames: [building.yaml, building-extra.yaml]   # paths relative to the manifest
  replace: false    # false = merge over what's loaded so far, true = wipe the table first
```

**A misspelled section name is silently ignored** — no warning, your file simply never
loads. This is the #1 "my mod does nothing" cause.

### Load & merge order

1. Core (`modId: Core`) → 2. DLCs (`BlueMars`) → 3. mods in declared order →
4. each mod's `optionalManifests`.

Per file list, entries are merged into the static table
`StaticDataCollectionItem<T>.table` (a `Dictionary<string, T>`) via
`StaticDataCollectionItem.Patch()`:

- **Same key as an existing entry** → fields you set overwrite, fields you omit keep
  their previous value (this is what makes `!replace`-style patching of single fields work).
- **New key** → new entry appended.
- **`replace: true`** → the table is cleared before your file is read. Almost never
  what you want in a mod.

---

## 3. Table items, keys and tags

Every moddable data class derives from `StaticDataCollectionItem<T>` and declares a
constant `YAML_TAG_NAME` (e.g. `ResourceType.YAML_TAG_NAME = "!resource"`).
The 25 table-item types in patch 1.8.x:

`AIPlayerType, Achievement, BehaviorTreeDefinition, BuildingCategoryType, BuildingType,
DroneType, EnhancementType, HazardAsteroidType, HazardDevilType, HazardSandstormType,
InteractionRule (rule), KnowledgeType, Lake, MultiplayerSettings, Person, Perspective,
PointOfInterest (poi), QuestType, RandomEvent, ResourceType, River, SpecialProjectType
(project), SpecialSite (site), TechnologyType, TransitionTable, WayType`

In a data file, the **top-level mapping key is the entry's `key`** (the string you use
in `!tag` references everywhere else):

```yaml
resource_my_metal:          # ← this IS the key. No "id" field exists.
  name: "My Rare Metal"
  materialType: Placeholder
```

Inherited by every table item (from `StaticDataCollectionItem`):

| Member | YAML? | Meaning |
|--------|-------|---------|
| `key` | no (it's the mapping key) | Entry ID |
| `isPlaceholder` | ✅ `[YamlMember]` | Marks a stub entry (used internally; you can declare one) |
| `resolved`, `isMock`, `rootPath`, `table` | runtime only | — |

### Cross-references (`!resource x`, `!building y`, …)

`CustomTypeConverter.ConvertFrom` intercepts any scalar with a registered tag and calls
a *placeholder factory*: it returns a mock instance (`Mock(key)`) carrying just the key.
After **all** manifests are loaded, `ConnectCrossReferencePlaceholders()` walks every
loaded object graph and replaces each mock with the real table entry.

Consequences:

- **Forward references and cross-mod references work** — order within/between files
  doesn't matter, as long as the target exists *somewhere* by the end of loading.
- **A reference to a key that never gets defined resolves to nothing** → the field
  stays a mock/null → first code that touches it throws `NullReferenceException`
  **far away from the YAML that caused it**, often during scene load or first frame.
  There is no "unknown reference" error message in the vanilla game.

### `!replace` on individual fields

```yaml
building_drone_factory:          # existing vanilla entry
  !replace droneCapacity: 3      # only this field is patched
```

`!replace` marks a field-level overwrite on lists/maps that would otherwise be merged.
For plain scalars on an existing key, simply re-declaring the field already overwrites it.

---

## 4. Setup singletons

The nine `*Setup` files (`InitialSetup`, `PlanetSetup`, `DroneSetup`, `ScatterSetup`,
`GeneralSetup`, `FrontendSetup`, `CombatSetup`, `WaySetup`, `MaintenanceSetup`) each map
to a class extending `YAMLSetup<T>` with a static `instance`. Loading a setup file from
a mod **deserializes over the existing instance** — fields you set overwrite, fields
you omit keep the previous (Core/DLC) value. Each class may implement
`ValidateConstraints()`; on failure the value is rejected (usually silently logged).

---

## 5. What is serializable — the three-state rule

For any field/property of a game data class (see the generated
[Schema Reference](schemas/README.md) for the per-type ground truth):

| C# declaration | Loaded from YAML? |
|----------------|-------------------|
| `[YamlMember]` on field **of any visibility** (incl. `private`, and property backing fields) | ✅ Yes — confirmed |
| Plain `public` field, or `public` property with public getter+setter, no attribute | 🟡 Yes by SharpYaml default conventions — works, but the game may overwrite it in `PostInitialize()` |
| `[YamlIgnore]`, `static`, `const`, or property with private setter | ⛔ No |

Type mapping cheat-sheet:

| C# type | YAML syntax |
|---------|-------------|
| `Sprite` / `Texture2D` | string path (`Resource Icons/Iron`, `BuildIcons/Icon_Port`) — resolved at load |
| `Color` | hex string `8B4513` or mapping |
| `XxxType` (table item) | `!tag key` reference |
| `List<T>` | YAML sequence |
| `Dictionary<K,V>` | YAML mapping (keys may themselves be `!tag` refs: `!resource resource_steel: 3`) |
| enum | bare value name (`Placeholder`) — wrong value = SharpYaml exception naming the enum |

---

## 6. Finalization (`PostInitialize`) — where "hidden" constraints live

After resolution, each type runs static finalizers. Known effects:

- **`ResourceType.AssignMaxIndex` / `PostInitialize`** — assigns dense indices used by
  fixed-size arrays all over the engine (BuildPanel display tables, scatter system…).
  This is why custom resources must use `materialType: Placeholder` and why patching a
  vanilla building's `outputResource` to a mod resource crashes with
  `ArgumentOutOfRangeException`: vanilla-sized arrays are indexed with mod indices.
- **`ResourceType.GenerateIconsAtlas`** — bakes vein icons into an atlas; bad
  `veinIconsName` entries surface here.
- **`index` golden rule** — never set `index` manually on existing entries (saves
  serialize indices), and omit it for new entries (auto-assigned after max).

---

## 7. Built-in dump facility (huge for tooling)

`YAMLLoader` ships **`DumpTable<T>(filename, table)`** and `DumpInstance<T>` — the same
serializer run in reverse. A BepInEx plugin can call these after `CompleteLoading()` to
write the **fully merged datamodel** (Core + DLC + all mods) back to YAML. That gives:

- ground-truth view of what your mod actually changed,
- diffable before/after snapshots,
- canonical examples of every field's serialized syntax.

This is the highest-leverage missing tool — see the
[Tooling Roadmap](YAML-Tooling-Roadmap.md).

---

## 8. Mental model summary

```
manifest.yaml ──(section name must match ModManifest field EXACTLY, else silently ignored)
   └─ data file ──(top-level key = entry ID)
        └─ fields ──(must match C# [YamlMember]/public names, else silently ignored)
             └─ !tag refs ──(resolved only at end of loading; bad key = delayed NRE)
                  └─ PostInitialize ──(index/atlas/array sizing; mod resources need Placeholder)
```

When something breaks: parse error → your YAML syntax; loud typed exception → wrong
value type/enum; **silent nothing** → misspelled section or field name; **delayed
NullReferenceException** → unresolved `!tag` reference or missing prefab/sprite.
Full mapping in the [Error Decoder](YAML-Error-Decoder.md).
