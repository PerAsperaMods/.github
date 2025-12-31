# Building YAML Properties Reference

## 📚 Introduction

This reference documents **Building YAML properties** based on the official ISA modding manual v2.0. These properties define building behavior, costs, production, and characteristics.

**⚠️ Critical Warning:** The official documentation contains **obsolete and incorrect** property names. Many properties listed in the official manual **do not exist** in the current game version. This document compares official documentation with validated current data.

**Sources:**
- ISA Quantum AI Laboratory Modding Manual v2.0 (official but potentially obsolete)
- Current game datamodel validation (Internal_doc/Yaml/)
- Working mod examples (Yaml-Mods/)

---

## 🚨 Validation Status

### ✅ **Validated Properties** (Confirmed in current game)
These properties have been verified against working mod examples:

```yaml
building_example:
  name: "Building Name"                    # ✅ Confirmed
  category: !buildingCategory energy      # ✅ Confirmed  
  cost:                                   # ✅ Confirmed
    - !resource metal: 100
    - !resource concrete: 50
  description: "Building description"      # ✅ Confirmed
```

### ⚠️ **Unvalidated Properties** (From official manual)
These properties are from the official manual but **require validation**:

```yaml
# ⚠️ WARNING: These may not work in current game version
building_questionable:
  maxSolarPowerProduction: 100    # ⚠️ May be obsolete
  colonistCapacity: 50            # ⚠️ May be obsolete
  militaryDroneCapacity: 10       # ⚠️ May be obsolete
  co2ConversionPerDay: 25         # ⚠️ May be obsolete
```

---

## 📋 Property Categories (Official Manual)

### 🏗️ Basic Building Properties

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `name` | String | Building name or localization key | ✅ Validated |
| `description` | String | Building description or localization key | ✅ Validated |
| `categoryType` | Reference | Building category for UI panels | ⚠️ May be `category` |
| `knowledge` | Reference | Knowledge unlocked when building unlocked | ⚠️ Unvalidated |

**Example:**
```yaml
building_solar_advanced:
  name: "Advanced Solar Panel"
  description: "High-efficiency solar panel"
  category: !buildingCategory energy     # Note: official says "categoryType"
  knowledge: !knowledge solar_technology
```

### 💰 Construction & Resources

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `requiredConstructionResources` | Array | Resources needed to build | ⚠️ May be `cost` |
| `inputResources` | Array | Resources consumed per cycle | ⚠️ Unvalidated |
| `outputResource` | Reference | Resource produced | ⚠️ May be different |
| `outputQuantity` | Number | Quantity produced per cycle | ⚠️ Unvalidated |
| `progressPerDay` | Number | Production cycle progress per day | ⚠️ Unvalidated |
| `initialStock` | Array | Starting resources in building | ⚠️ Unvalidated |

**Example (Official - May Not Work):**
```yaml
# ⚠️ WARNING: Official syntax may be incorrect
building_processor:
  requiredConstructionResources:    # May be "cost" instead
    - !resource metal: 150
    - !resource electronics: 75
  inputResources:                   # May not exist
    - !resource iron_ore: 10
  outputResource: !resource steel   # May be different syntax
  outputQuantity: 5
  progressPerDay: 1.0
```

### ⚡ Power & Energy

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `maxSolarPowerProduction` | Number | Solar power output | ⚠️ Likely obsolete |
| `maxEolicPowerProduction` | Number | Wind power output | ⚠️ Likely obsolete |
| `maxThermalPowerProduction` | Number | Thermal power output | ⚠️ Likely obsolete |
| `maxFissionPowerProduction` | Number | Nuclear fission output | ⚠️ Likely obsolete |
| `maxFusionPowerProduction` | Number | Nuclear fusion output | ⚠️ Likely obsolete |
| `powerConsumption` | Number | Power required to operate | ⚠️ Unvalidated |
| `powerPriority` | Number | Power allocation priority | ⚠️ Unvalidated |
| `energyStorageCapacity` | Number | Energy storage capacity | ⚠️ Unvalidated |
| `extendsPowerCluster` | Boolean | Extends power network | ⚠️ Unvalidated |
| `powerClusterRadius` | Number | Power network reach | ⚠️ Unvalidated |

**Critical Note:** Power system properties from official manual are likely completely obsolete. Current game may use entirely different property names.

### 🏭 Production & Mining

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `extractionLevel` | Number | Vein levels this building can extract | ⚠️ Unvalidated |
| `requiredResourceVein` | Reference | Required vein for placement | ⚠️ Unvalidated |
| `scalesToResourceVein` | Boolean | Building scales to vein size | ⚠️ Unvalidated |

### 🏥 Health & Maintenance

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `maxHealth` | Number | Maximum building health | ⚠️ Unvalidated |
| `healthLossPerDay` | Number | Health decay per day | ⚠️ Unvalidated |
| `maintenancePriority` | Number | Maintenance priority level | ⚠️ Unvalidated |
| `extendsMaintenanceCluster` | Boolean | Extends maintenance network | ⚠️ Unvalidated |
| `maintenanceClusterRadius` | Number | Maintenance network reach | ⚠️ Unvalidated |

### 🎨 Visual & UI

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `prefabName` | String | 3D model mesh in close view | ⚠️ Unvalidated |
| `rubblePrefabName` | String | Destroyed building mesh | ⚠️ Unvalidated |
| `iconName` | String | Icon for far view and UI | ⚠️ Unvalidated |
| `rivalIconName` | String | Icon for non-player factions | ⚠️ Unvalidated |
| `orbitalIconName` | String | Icon in orbital view | ⚠️ Unvalidated |
| `emptyHubIconName` | String | Icon when drone hub is empty | ⚠️ Unvalidated |
| `progressBarNames` | Array | Progress indicators when hovering | ⚠️ Unvalidated |

### 🔧 Advanced Features

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `jumpRadius` | Number | Max connection distance to other buildings | ⚠️ Unvalidated |
| `textureOverrides` | Array | Custom texture replacements | ⚠️ Complex, unvalidated |
| `hasStorage` | Boolean | Can store extra resources | ⚠️ Unvalidated |
| `hasSpacePort` | Boolean | Can launch special projects | ⚠️ Unvalidated |
| `isBaseStarter` | Boolean | Can start new base automatically | ⚠️ Unvalidated |
| `isWorkerHub` | Boolean | Can host worker drone | ⚠️ Unvalidated |
| `isDistrictHub` | Boolean | Creates and manages district | ⚠️ Unvalidated |
| `orbitalPlacement` | Boolean | Placed from orbit like landing site | ⚠️ Unvalidated |

### 🌱 Environmental Features

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `canSpawnLichenAndPlants` | Boolean | Can create vegetation when operative | ⚠️ Unvalidated |
| `canSpawnCyanobacteria` | Boolean | Can create cyanobacteria | ⚠️ Unvalidated |
| `co2ConversionPerDay` | Number | CO2 to O2 conversion rate | ⚠️ Unvalidated |
| `gasReleaseAmount` | Number | Gas release quantity | ⚠️ Unvalidated |

### 🌍 Placement Requirements

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `needsEquatorialStrip` | Boolean | Requires equatorial placement | ⚠️ Unvalidated |
| `needsPressure` | Boolean | Requires minimum atmospheric pressure | ⚠️ Unvalidated |
| `needsBreathableAtmosphere` | Boolean | Requires breathable atmosphere | ⚠️ Unvalidated |
| `waterNeededFactor` | Number | Water level needed nearby | ⚠️ Unvalidated |
| `waterNeededRadius` | Number | Radius for water requirement check | ⚠️ Unvalidated |

### 👥 Colony & Population

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `colonistCapacity` | Number | Max colonists in building | ⚠️ Likely obsolete |

### 🚁 Drones & Military

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `militaryDroneCapacity` | Number | Military drone capacity boost | ⚠️ Likely obsolete |
| `defensiveMissilesPerDay` | Number | Defensive missiles per day | ⚠️ Likely obsolete |
| `missileRange` | Number | Missile attack range | ⚠️ Likely obsolete |
| `missileDamage` | Number | Missile damage value | ⚠️ Likely obsolete |

### 📡 Scanner Properties

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `scannerScanTime` | Number | Days to reveal one scanner tile | ⚠️ Unvalidated |
| `scannerTimerFactor` | Number | Distance factor for scan time | ⚠️ Unvalidated |
| `spawnRevealRadius` | Number | Auto-reveal radius on placement | ⚠️ Unvalidated |

### 🔄 Upgrade System

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `isUpgradeTo` | Array | Buildings this upgrades from | ⚠️ Unvalidated |

### 🚄 Transportation

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `enablesHyperloopConnection` | Boolean | Supports hyperloop connections | ⚠️ Unvalidated |

---

## ✅ Validated Current Syntax

Based on working mod examples in the codebase:

```yaml
# Confirmed working syntax from Yaml-Mods/
building_example:
  name: "My Building Name"
  description: "Building description" 
  category: !buildingCategory energy
  cost:
    - !resource metal: 100
    - !resource concrete: 50
    - !resource electronics: 25
  
  # These are commonly used in working mods:
  unlock_condition: !knowledge basic_engineering
  production:
    - !resource electricity: 50
  
  # Building upgrade relationships
  upgrade_from: !building basic_solar_panel
  upgrade_to: !building advanced_solar_panel
```

---

## 🚨 Critical Warnings

### Property Name Mismatch
Many official property names are **incorrect**:
- Official says `categoryType` → Current uses `category`
- Official says `requiredConstructionResources` → Current uses `cost`
- Power properties likely completely different

### Obsolete Features
Several features from manual may not exist:
- Military/drone systems may be removed
- Colony population system may be different
- Power system completely redesigned

### Validation Required
**Never assume any property works** without testing:
1. Check against working mod examples
2. Validate with current game datamodel files  
3. Test in-game before documenting
4. Cross-reference with SDK/CleanedScriptAssemblyClass

---

## 🔍 Recommended Validation Process

### 1. Check Working Examples
```bash
# Search for actual property usage
grep -r "category:" Yaml-Mods/
grep -r "cost:" Yaml-Mods/  
grep -r "production:" Yaml-Mods/
```

### 2. Cross-Reference Current Data
```bash
# Check official game files
find Internal_doc/Yaml/ -name "building*.yaml" -exec grep "properties" {} \;
```

### 3. Test Incrementally
```yaml
# Start minimal, add one property at a time
building_test:
  name: "Test Building"
  category: !buildingCategory test
  # Add more properties one by one
```

---

## 🔗 Related Documentation

- **[Manifest YAML](Manifest-YAML.md)** - Mod structure and dependencies
- **[Console Commands](Console-Commands.md)** - Testing commands
- **[Game Commands](Game-Commands.md)** - Unlock buildings for testing
- **[SDK Components](../architecture/SDK-Components.md)** - Programmatic building access

---

## 📋 Action Items for Validation

1. **Create test mod** with minimal building properties
2. **Validate each property** from official list against current game
3. **Document working properties** with examples
4. **Mark obsolete properties** clearly
5. **Create corrected property reference** based on validation

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0 (official but potentially obsolete)  
**Status:** ⚠️ **REQUIRES VALIDATION** - Many properties may be incorrect  
**Priority:** HIGH - Critical for accurate mod development