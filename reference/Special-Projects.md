# Special Projects YAML Documentation

## 📚 Introduction

This document covers **Special Projects** system from the official ISA modding manual v2.0. Special Projects are large-scale planetary improvements launched from Spaceports.

**⚠️ Critical Warning:** This documentation is from the **official ISA manual but may contain obsolete information**. The Special Projects system may have changed significantly since manual creation.

**Sources:**
- ISA Quantum AI Laboratory Modding Manual v2.0 (official but potentially obsolete)
- Annex II: Special Projects (pages 15-16 of manual)

---

## 🚨 Validation Status

**❌ UNVALIDATED:** All information in this document comes from official manual but **requires testing against current game version**.

---

## 📋 Special Projects Structure (Official Manual)

According to the official manual, Special Projects are defined in YAML with these components:

### Basic Properties

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `name` | String | Project name or localization key | ⚠️ Unvalidated |
| `description` | String | Project description or localization key | ⚠️ Unvalidated |
| `cost` | Array | Resources required to launch | ⚠️ Unvalidated |
| `duration` | Number | Days to complete | ⚠️ Unvalidated |
| `effects` | Array | Project outcomes | ⚠️ Unvalidated |

### ⚠️ Official Example (May Not Work)

```yaml
# WARNING: This syntax is from official manual but unvalidated
special_project_terraform:
  name: "Atmospheric Processor"
  description: "Accelerates atmospheric development"
  cost:
    - !resource steel: 1000
    - !resource electronics: 500
    - !resource rare_metals: 200
  duration: 180  # Days
  effects:
    - type: "atmospheric_boost"
      value: 0.25
    - type: "temperature_adjust" 
      value: 5.0
```

---

## 🏭 Spaceport Requirements

### Official Manual Claims:
- Special Projects are launched from buildings with `hasSpacePort: true`
- Require specific resource stockpiles
- Launch time depends on project complexity

**⚠️ Validation Required:**
1. Does `hasSpacePort` property exist in current game?
2. Are Special Projects still a separate system?
3. What buildings can actually launch projects?

---

## 🚀 Project Categories (Official)

### Terraforming Projects
According to manual, these affect planetary environment:

```yaml
# ⚠️ WARNING: Unvalidated official examples
atmospheric_processor:
  name: "Atmospheric Processor"
  effects:
    - type: "co2_reduction"
      value: 1000
    - type: "oxygen_increase" 
      value: 500

temperature_regulator:
  name: "Global Temperature Control"
  effects:
    - type: "temperature_set"
      target: 15.0  # Celsius
```

### Infrastructure Projects
Large-scale infrastructure improvements:

```yaml
# ⚠️ WARNING: Unvalidated official examples  
orbital_platform:
  name: "Orbital Manufacturing Platform"
  effects:
    - type: "production_bonus"
      category: "electronics"
      multiplier: 1.5

planetary_shield:
  name: "Planetary Defense Grid"
  effects:
    - type: "disaster_protection"
      value: 0.8  # 80% protection
```

### Research Accelerators
Scientific advancement projects:

```yaml
# ⚠️ WARNING: Unvalidated official examples
quantum_computer:
  name: "Quantum Research Computer" 
  effects:
    - type: "research_speed"
      multiplier: 2.0
    - type: "unlock_technology"
      target: "quantum_physics"
```

---

## 🔄 Effect Types (Official Manual)

The manual lists these effect types:

| Effect Type | Description | Status |
|-------------|-------------|--------|
| `atmospheric_boost` | Accelerates atmosphere development | ⚠️ Unvalidated |
| `temperature_adjust` | Modifies planet temperature | ⚠️ Unvalidated |
| `co2_reduction` | Reduces CO2 levels | ⚠️ Unvalidated |
| `oxygen_increase` | Increases O2 levels | ⚠️ Unvalidated |
| `production_bonus` | Boosts building production | ⚠️ Unvalidated |
| `research_speed` | Accelerates research | ⚠️ Unvalidated |
| `unlock_technology` | Grants technologies | ⚠️ Unvalidated |
| `disaster_protection` | Reduces disaster damage | ⚠️ Unvalidated |

**Critical Question:** Do any of these effect types actually exist in current game?

---

## 🎯 Launch Mechanics (Official)

### Resource Requirements
Official manual describes launch process:
1. Build Spaceport with `hasSpacePort: true`
2. Stockpile required resources
3. Select Special Project from spaceport menu
4. Wait for completion duration

### Progress Tracking
- Projects show progress in spaceport UI
- Cannot be cancelled once started
- Effects apply immediately on completion

**⚠️ Validation Questions:**
1. Is there actually a spaceport UI for Special Projects?
2. Can projects be cancelled or modified?
3. Are resources consumed immediately or over time?

---

## 📂 File Structure (Official)

According to manual, Special Projects should be in:
```
your_mod/
├── manifest.yaml
├── special_projects/
│   ├── terraforming.yaml
│   ├── infrastructure.yaml  
│   └── research.yaml
```

**⚠️ Warning:** File structure may be incorrect or obsolete.

---

## 🔍 Validation Required

### Critical Tests Needed
1. **Check if Special Projects exist** in current game
2. **Find working spaceport buildings** with project capabilities  
3. **Test project YAML syntax** against current parser
4. **Verify effect types** and their parameters
5. **Confirm file structure** requirements

### Recommended Investigation
```bash
# Search for Special Projects in current game files
grep -r "special.project" Internal_doc/
grep -r "hasSpacePort" CleanedScriptAssemblyClass/
grep -r "spaceport" Organization-Wiki/

# Check for project-related commands
grep -r "project" reference/Game-Commands.md
grep -r "launch" reference/Console-Commands.md
```

### Test Mod Creation
Create minimal test mod:
```yaml
# test_project.yaml
simple_test:
  name: "Test Project"
  description: "Testing if system works"
  cost:
    - !resource metal: 10
  duration: 1
  effects:
    - type: "test_effect"
      value: 1
```

---

## 🚨 Major Concerns

### System May Not Exist
**High probability that Special Projects system is obsolete:**
- No evidence in current mod examples
- No references in validated game commands
- Complex system that may have been removed

### Alternative Implementations
Current game may handle "special projects" as:
- Regular buildings with special properties
- Technology unlocks
- Event system triggers
- Direct scripting through BepInX mods

---

## 🔗 Related Documentation

- **[Manifest YAML](Manifest-YAML.md)** - Check if Special Projects listed in mod structure
- **[Building YAML Properties](Building-YAML-Properties.md)** - Validate `hasSpacePort` property
- **[Game Commands](Game-Commands.md)** - Look for project-related commands
- **[Console Commands](Console-Commands.md)** - Test project launching

---

## ⚡ Immediate Action Required

### High Priority Validation
1. **Search codebase** for any Special Projects references
2. **Test spaceport functionality** in current game
3. **Create minimal test mod** to verify syntax
4. **Document findings** and update this page

### If System Is Obsolete
1. **Mark as deprecated** in documentation
2. **Document modern alternatives** (buildings, events, scripting)
3. **Create migration guide** for mod developers
4. **Update official documentation** with current methods

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0 - Annex II  
**Status:** ❌ **COMPLETELY UNVALIDATED** - System may not exist  
**Priority:** CRITICAL - Requires immediate validation testing  
**Risk Level:** HIGH - May mislead developers with obsolete information