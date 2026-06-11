# YAML Modding — Documentation Hub

Per Aspera loads its entire datamodel (buildings, resources, technologies, quests,
rules, hazards…) from YAML files, and mods can add to or patch every part of it —
**no C# required**. The catch: the loader fails silently or with bare
`NullReferenceException`s. These docs exist so you never have to guess again.

| Doc | Read it when… |
|-----|---------------|
| **[YAML-Modding-Internals](YAML-Modding-Internals.md)** | You want to understand how loading *actually* works: manifest discovery, SharpYaml, tag/placeholder resolution, merge order, `PostInitialize` constraints |
| **[YAML-Error-Decoder](YAML-Error-Decoder.md)** | Something broke: silent no-op, crash at load, or a delayed NRE with no message |
| **[Schema Reference](schemas/README.md)** | You need the exact fields of any type — all 25 data types + 10 setups + manifest, **every field including hidden ones**, extracted from the game's C# (`[YamlMember]`) with vanilla usage counts |
| **[YAML-Tooling-Roadmap](YAML-Tooling-Roadmap.md)** | You're contributing to the tooling (validator, mod creator, dump plugin) |

## Quick start (new YAML modder)

1. Create `…\Per Aspera\Per Aspera_Data\StreamingAssets\Mods\MyMod\manifest.yaml`:

   ```yaml
   modId: "MyMod"
   compatibleGameVersions: ["1.8.x"]
   building:
     filenames: [building.yaml]
     replace: false
   ```

2. Copy the template for your type from [schemas/](schemas/README.md)
   (e.g. [BuildingType](schemas/BuildingType.md)) and fill in the fields you need —
   omitted fields keep vanilla/default values.
3. Validate **before** launching the game:
   `python Tools/validate_yaml_mods.py --mod MyMod`
4. Iron rules that prevent the known crashes:
   - custom resources → `materialType: Placeholder`, never set `index`
   - never patch a vanilla building's `outputResource` to a mod resource
   - manifest section names must match exactly (silently ignored otherwise)

Working examples: the [water_early + WaterWorkFix](https://github.com/PerAsperaMods)
duo (YAML + C# companion mod) is the reference pattern.
