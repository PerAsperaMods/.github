# Manifest.yaml Reference

## 📚 Introduction

This document covers the **manifest.yaml** structure for Per Aspera mods based on the official ISA modding manual. The manifest is the entry point that defines mod metadata, dependencies, and content files.

**⚠️ Warning:** This documentation is based on the official manual v2.0 but may contain obsolete information. Always validate against current game version.

**Source:** ISA Quantum AI Laboratory Modding Manual v2.0

---

## 🏗️ Basic Manifest Structure

```yaml
# Basic mod manifest example
modId: "YourUniqueModId"
compatibleGameVersions:
  - "1.8.x"
requiredMods: []
optionalManifests: []

# Content definitions
building:
  filenames:
    - building.yaml
  replace: false

resource:
  filenames:
    - resource.yaml
  replace: false
```

---

## 🔧 Core Properties

### modId (Required)
**Type:** String  
**Description:** Unique identifier for your mod

```yaml
modId: "MyAmazingMod"
```

**Best Practices:**
- Use descriptive, unique names
- Avoid spaces and special characters
- Consider namespace prefixes for large projects

---

### compatibleGameVersions (Recommended)
**Type:** Array of Strings  
**Description:** Game versions compatible with this mod

```yaml
compatibleGameVersions:
  - "1.8.x"
  - "1.7.x"
```

**Version Format:**
- Use semantic versioning: `major.minor.patch`
- Use `x` for wildcards: `1.8.x` matches any 1.8.x version
- Game version shown in main menu upper-left corner

---

### requiredMods (Optional)
**Type:** Array of Strings  
**Description:** List of mod IDs this mod depends on

```yaml
requiredMods:
  - "CoreEnhancements"
  - "ExtendedResources"
```

**Behavior:**
- Mod won't load if dependencies missing
- Load order automatically handled
- Creates dependency chain validation

---

### optionalManifests (Optional)
**Type:** Array of Strings  
**Description:** Sub-manifests loaded conditionally

```yaml
optionalManifests:
  - "manifest-campaign.yaml"
  - "manifest-sandbox.yaml"
```

**Use Cases:**
- Mode-specific content (Campaign vs Sandbox)
- Optional feature modules
- DLC-dependent content

**Example Sub-Manifest:**
```yaml
# manifest-campaign.yaml
requiredMods:
  - "Campaign"
building:
  filenames:
    - buildings-campaign-only.yaml
  replace: false
```

---

## ⚙️ Setup Files

### Setups (Optional)
**Description:** Global configuration and world initialization

```yaml
generalSetup: GeneralSetup.yaml
initialSetup: InitialSetup.yaml
planetSetup: PlanetSetup.yaml
```

**Common Setup Types:**
- `generalSetup` - Game mechanics parameters
- `initialSetup` - Starting conditions
- `planetSetup` - World generation settings
- `droneSetup` - Drone behavior configuration

**⚠️ Note:** Setup file structure may be obsolete. Validate against current game files.

---

## 📁 Type Lists

### Building Configuration

```yaml
building:
  filenames:
    - building.yaml
    - building-advanced.yaml
  replace: false
```

### Resource Configuration

```yaml
resource:
  filenames:
    - resource.yaml
    - resource-custom.yaml
  replace: false
```

### Technology Configuration

```yaml
technology:
  filenames:
    - technology-engineering.yaml
    - technology-biology.yaml
    - technology-space.yaml
  replace: false
```

### Knowledge Base Configuration

```yaml
knowledge:
  filenames:
    - knowledge.yaml
  replace: false
```

### Building Categories

```yaml
buildingCategory:
  filenames:
    - buildingCategory.yaml
  replace: false
```

---

## 🔄 Replace vs Additive Behavior

### replace: false (Additive - Recommended)

```yaml
building:
  filenames:
    - my-custom-buildings.yaml
  replace: false
```

**Behavior:**
- Adds new content to existing game content
- Preserves base game functionality
- Allows multiple mods to coexist
- Can override specific entries by using same keys

### replace: true (Complete Override - Dangerous)

```yaml
building:
  filenames:
    - complete-building-replacement.yaml
  replace: true
```

**Behavior:**
- Completely replaces base game content
- Can break compatibility with other mods
- Requires defining ALL entries, not just new ones
- Use only when necessary

---

## 🌐 Localization Support

```yaml
languages:
  filenames:
    - language.yaml
  replace: false
```

**Language YAML Structure:**
```yaml
# language.yaml
language_english_custom:
  name: "English (Custom)"
  systemLanguage: "English"
  files:
    - "Localization/MyMod_EN.csv"

language_french_custom:
  name: "Français (Custom)"
  systemLanguage: "French"  
  files:
    - "Localization/MyMod_FR.csv"
```

**CSV Format:**
```csv
Context,ID,Text,Comments
Frontend,my_building_name,Advanced Solar Panel,Building name
Frontend,my_building_desc,Highly efficient solar panel,Building description
```

---

## 📋 Complete Manifest Example

```yaml
# Complete manifest.yaml example
modId: "TerraformingEnhanced"
compatibleGameVersions:
  - "1.8.x"
requiredMods:
  - "CoreGameMechanics"
optionalManifests:
  - "manifest-military.yaml"

# Setup files
generalSetup: configs/GeneralSetup.yaml
planetSetup: configs/PlanetSetup.yaml

# Content files
building:
  filenames:
    - buildings/solar-enhanced.yaml
    - buildings/atmospheric-processors.yaml
    - buildings/terraforming-stations.yaml
  replace: false

resource:
  filenames:
    - resources/advanced-materials.yaml
    - resources/atmospheric-gases.yaml
  replace: false

technology:
  filenames:
    - tech/terraforming-engineering.yaml
    - tech/atmospheric-science.yaml
    - tech/advanced-energy.yaml
  replace: false

knowledge:
  filenames:
    - knowledge/terraforming-guide.yaml
  replace: false

buildingCategory:
  filenames:
    - categories/terraforming-categories.yaml
  replace: false

# Localization
languages:
  filenames:
    - language.yaml
  replace: false
```

---

## 🚨 Validation & Testing

### Pre-Launch Checklist

✅ **Syntax Validation**
- YAML syntax correct (2 spaces, no tabs)
- All referenced files exist
- File paths relative to mod root

✅ **Dependency Check**
- Required mods available
- Compatible game version installed
- No circular dependencies

✅ **Content Validation**
- Building references valid buildingCategories
- Resource references exist
- Technology dependencies correct

### Common Errors

**Missing Files**
```yaml
building:
  filenames:
    - nonexistent-file.yaml  # ❌ File not found
```

**Incorrect Paths**
```yaml
building:
  filenames:
    - /absolute/path/building.yaml  # ❌ Use relative paths
    - ../outside-mod/building.yaml  # ❌ Stay within mod folder
```

**Circular Dependencies**
```yaml
# Mod A requires Mod B, Mod B requires Mod A
requiredMods:
  - "ModB"  # ❌ Creates dependency loop
```

---

## 🔗 Related Documentation

- **[Console Commands](Console-Commands.md)** - Debug commands for testing
- **[Game Commands](Game-Commands.md)** - Programmatic commands
- **[Building Types](Building-Types.md)** - Valid building references
- **[YAML Syntax Guide](../tutorials/YAML-Syntax.md)** - YAML syntax basics

---

## ⚠️ Obsolescence Warning

**Known Issues with Official Documentation:**
- Some setup file structures may have changed
- Property names in type lists might be outdated
- Game version format may differ
- File path handling could be different

**Validation Strategy:**
1. Test with minimal manifest first
2. Add content incrementally
3. Check game logs for errors
4. Compare with working mod examples
5. Verify against current game datamodel files

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0  
**Status:** ⚠️ Requires validation against current game version  
**Next Steps:** Validate setup files and type list structures against current game files