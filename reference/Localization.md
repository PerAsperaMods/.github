# Localization System Documentation

## 📚 Introduction

This document covers the **Localization system** from the official ISA modding manual v2.0. The system allows translation of mods into multiple languages using CSV files.

**⚠️ Critical Warning:** This documentation is from the **official ISA manual but may contain obsolete information**. Localization file formats and loading mechanisms may have changed.

**Sources:**
- ISA Quantum AI Laboratory Modding Manual v2.0 (official but potentially obsolete)
- Annex III: Localization (pages 17-18 of manual)

---

## 🚨 Validation Status

**❌ UNVALIDATED:** All information comes from official manual but **requires testing against current game version**.

---

## 📂 Localization Structure (Official Manual)

According to the official manual, localization uses **CSV files** with specific structure:

### File Organization
```
your_mod/
├── manifest.yaml
├── localization/
│   ├── en.csv     # English (default)
│   ├── fr.csv     # French  
│   ├── de.csv     # German
│   ├── es.csv     # Spanish
│   ├── it.csv     # Italian
│   ├── pt.csv     # Portuguese
│   ├── ru.csv     # Russian
│   ├── ja.csv     # Japanese
│   ├── ko.csv     # Korean
│   └── zh.csv     # Chinese
```

**⚠️ Warning:** File structure and supported languages may be incorrect or obsolete.

---

## 📋 CSV Format (Official Manual)

### Basic Structure
Official manual shows this CSV format:

```csv
# ⚠️ WARNING: Unvalidated official format
Key,Text,Comment
building_solar_advanced,Advanced Solar Panel,High efficiency solar panel
building_solar_advanced_desc,Converts sunlight to electricity with 150% efficiency,Building description
resource_rare_metal,Rare Metals,Exotic materials for advanced technology  
tech_fusion_power,Fusion Power,Nuclear fusion technology
```

### ⚠️ Official Column Definitions

| Column | Purpose | Required | Status |
|--------|---------|----------|--------|
| `Key` | Unique identifier for text | Yes | ⚠️ Unvalidated |
| `Text` | Translated text | Yes | ⚠️ Unvalidated |
| `Comment` | Developer notes | No | ⚠️ Unvalidated |

**Critical Questions:**
1. Is CSV actually the format used?
2. Are these column names correct?
3. Does the game support these languages?

---

## 🔤 Key Naming Conventions (Official)

The manual suggests these key patterns:

### Building Localization
```csv
# ⚠️ WARNING: Unvalidated naming patterns
building_[name],Building Name,
building_[name]_desc,Building Description,
building_[name]_tooltip,Tooltip text,
```

### Resource Localization  
```csv
resource_[name],Resource Name,
resource_[name]_desc,Resource Description,
resource_[name]_unit,Unit (kg/L/etc),
```

### Technology Localization
```csv
tech_[name],Technology Name,
tech_[name]_desc,Technology Description,
tech_[name]_flavor,Flavor text,
```

### Knowledge Localization
```csv
knowledge_[name],Knowledge Name,
knowledge_[name]_desc,Knowledge Description,
```

**⚠️ Warning:** These naming patterns may not match current game expectations.

---

## 🌍 Supported Languages (Official)

According to manual, these language codes are supported:

| Code | Language | Native Name | Status |
|------|----------|-------------|--------|
| `en` | English | English | ⚠️ Assumed default |
| `fr` | French | Français | ⚠️ Unvalidated |
| `de` | German | Deutsch | ⚠️ Unvalidated |
| `es` | Spanish | Español | ⚠️ Unvalidated |
| `it` | Italian | Italiano | ⚠️ Unvalidated |
| `pt` | Portuguese | Português | ⚠️ Unvalidated |
| `ru` | Russian | Русский | ⚠️ Unvalidated |
| `ja` | Japanese | 日本語 | ⚠️ Unvalidated |
| `ko` | Korean | 한국어 | ⚠️ Unvalidated |
| `zh` | Chinese | 中文 | ⚠️ Unvalidated |

**Critical Validation:** Does current game actually support all these languages?

---

## 📝 YAML Integration (Official)

### Using Localization Keys
According to manual, reference localized text in YAML using key names:

```yaml
# ⚠️ WARNING: Unvalidated syntax
building_advanced_solar:
  name: "building_solar_advanced"           # Key instead of direct text
  description: "building_solar_advanced_desc"
  category: !buildingCategory energy
  cost:
    - !resource metal: 100
    - !resource electronics: 50
```

### Alternative Syntax
Manual also shows direct text option:
```yaml
building_simple:
  name: "Simple Solar Panel"               # Direct text
  name_localized: "building_simple"       # Localization key
  description: "Basic solar power"
  description_localized: "building_simple_desc"
```

**⚠️ Questions:**
1. Which syntax actually works?
2. Does `name_localized` property exist?
3. Is direct text or keys preferred?

---

## 🔄 Loading Mechanism (Official)

### Automatic Loading
Official manual claims:
- Game automatically loads `localization/[language].csv` based on user's language setting
- Falls back to English (`en.csv`) if translation missing
- Keys not found in CSV display as the key name

### Loading Order
1. Check user's language CSV file
2. If key not found, check English CSV
3. If still not found, display raw key

**⚠️ Validation Required:** Does this loading mechanism actually exist?

---

## 📄 Complete Example (Official)

### English Localization (`en.csv`)
```csv
Key,Text,Comment
mod_terraformer,Advanced Terraforming Mod,Mod name
building_atmosphere_processor,Atmospheric Processor,Large terraforming facility
building_atmosphere_processor_desc,Rapidly converts CO2 to oxygen for breathable atmosphere,Building description
resource_terraforming_catalyst,Terraforming Catalyst,Special resource for atmosphere processing
tech_advanced_terraforming,Advanced Terraforming,Technology to unlock atmospheric processors
knowledge_atmospheric_chemistry,Atmospheric Chemistry,Knowledge about gas conversion
```

### French Localization (`fr.csv`)  
```csv
Key,Text,Comment
mod_terraformer,Mod de Terraformation Avancée,Nom du mod
building_atmosphere_processor,Processeur Atmosphérique,Grande installation de terraformation
building_atmosphere_processor_desc,Convertit rapidement le CO2 en oxygène pour une atmosphère respirable,Description du bâtiment
resource_terraforming_catalyst,Catalyseur de Terraformation,Ressource spéciale pour le traitement atmosphérique
tech_advanced_terraforming,Terraformation Avancée,Technologie pour débloquer les processeurs atmosphériques
knowledge_atmospheric_chemistry,Chimie Atmosphérique,Connaissance de la conversion des gaz
```

**⚠️ Warning:** This example format is completely unvalidated.

---

## 🚨 Character Encoding (Official)

### Supported Encodings
Manual mentions:
- **UTF-8** for all CSV files (required for international characters)
- **BOM optional** but recommended for Windows compatibility
- **Line endings:** Windows (CRLF) or Unix (LF) both supported

### Special Characters
```csv
# Examples with special characters (unvalidated)
building_cañón,Cañón Station,Spanish building name
building_müller,Müller Laboratory,German building name  
resource_fußnote,Fußnote Crystal,German resource with special chars
tech_naïve,Naïve Technology,French technology with diacritic
```

---

## 🔍 Validation Tests Required

### File Format Validation
1. **Test CSV loading** - Does game actually parse CSV files?
2. **Check encoding support** - UTF-8, special characters, BOM handling
3. **Verify column structure** - Are Key,Text,Comment correct?
4. **Test language codes** - Which languages actually work?

### Integration Testing  
1. **YAML key referencing** - Does `name: "localization_key"` work?
2. **Fallback behavior** - Missing keys, missing files
3. **Language switching** - Runtime language changes

### Current Game Investigation
```bash
# Search for localization evidence
grep -r "localization" Internal_doc/
grep -r "\.csv" CleanedScriptAssemblyClass/
grep -r "language" Organization-Wiki/

# Check for language files in game installation
find "F:\SteamLibrary\steamapps\common\Per Aspera" -name "*.csv"
find "F:\SteamLibrary\steamapps\common\Per Aspera" -name "*local*"
```

---

## 🚨 Major Concerns

### System May Be Obsolete
**High probability that localization system is different:**
- No evidence of CSV files in current mod examples
- Complex system that may use different format
- Unity typically uses different localization methods

### Alternative Implementations
Current game may use:
- **Unity Localization Package** (standard Unity approach)
- **JSON files** instead of CSV
- **Embedded text** in YAML with language suffixes
- **No mod localization support** at all

---

## 🔬 Investigation Priorities

### High Priority Tests
1. **Check game installation** for localization files
2. **Search mod examples** for any localization usage
3. **Test minimal CSV file** with one key
4. **Verify YAML key referencing** works

### Test Mod Creation
```yaml
# manifest.yaml
name: "localization_test"

# buildings/test.yaml  
test_building:
  name: "test_building_name"    # Try key reference
  description: "test_building_desc"
  category: !buildingCategory energy
```

```csv
# localization/en.csv
Key,Text,Comment
test_building_name,Test Building,Test building name
test_building_desc,This is a test building for localization,Test description
```

---

## 🔗 Related Documentation

- **[Manifest YAML](Manifest-YAML.md)** - Check if localization is part of mod structure
- **[Building YAML Properties](Building-YAML-Properties.md)** - Text properties that need localization
- **[YAML Datamodel](../content/YAML-Datamodel.md)** - Overall data structure

---

## ⚡ Immediate Action Required

### Critical Validation Steps
1. **Search for existing localization** in current mods/game files
2. **Test CSV file loading** with minimal example
3. **Document actual localization method** if different
4. **Create working example** or mark as obsolete

### If System Is Obsolete
1. **Research current localization method** used by game
2. **Document modern approach** for mod developers
3. **Create migration guide** from official manual method
4. **Update with validated information**

---

## 📋 Validation Checklist

- [ ] **File Format:** Test if CSV files are actually loaded
- [ ] **Column Structure:** Verify Key,Text,Comment column names
- [ ] **Encoding:** Test UTF-8, special characters, BOM handling
- [ ] **Language Codes:** Confirm which language codes work
- [ ] **YAML Integration:** Test key referencing in YAML files
- [ ] **Fallback Behavior:** Test missing keys and files
- [ ] **Runtime Changes:** Test language switching during gameplay

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0 - Annex III  
**Status:** ❌ **COMPLETELY UNVALIDATED** - System may be obsolete  
**Priority:** HIGH - Critical for international mod development  
**Risk Level:** HIGH - May provide completely incorrect localization method