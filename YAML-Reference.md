# YAML Reference — Per Aspera Modding

Complete reference for all YAML properties used in Per Aspera mods. Based on decompiled IL2CPP classes and validated against the official datamodel.

---

## ⚠️ Critical Rules

> **NEVER modify the `index` field** of an existing entry — it corrupts saves.  
> **NEVER reuse an existing `index`** — always use values > 1000 for new mod entries to avoid conflicts.  
> **`index` is optional for new entries** — the game assigns one internally; only add it if you need stable references.

---

## 📑 Table of Contents

- [manifest.yaml — Mod entry point](#manifestyaml--mod-entry-point)
- [building.yaml — Buildings](#buildingyaml--buildings)
- [buildingCategory.yaml — Building categories](#buildingcategoryyaml--building-categories)
- [resource.yaml — Resources](#resourceyaml--resources)
- [technology.yaml — Technologies](#technologyyaml--technologies)
- [knowledge.yaml — AMI knowledge base](#knowledgeyaml--ami-knowledge-base)
- [YAML Tag Reference](#yaml-tag-reference)

---

## manifest.yaml — Mod entry point

Every mod needs a `manifest.yaml` that declares its content files.

```yaml
modId: "MyUniqueMod"
compatibleGameVersions:
  - "1.8.x"
requiredMods: []

building:
  filenames:
    - building.yaml
  replace: false

resource:
  filenames:
    - resource.yaml
  replace: false

technology:
  filenames:
    - technology.yaml
  replace: false

knowledge:
  filenames:
    - knowledge.yaml
  replace: false
```

### Core manifest properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `modId` | string | ✅ | Unique identifier for your mod. No spaces. |
| `compatibleGameVersions` | string[] | recommended | Game versions this mod supports. |
| `requiredMods` | string[] | no | Other mod IDs that must be loaded first. |
| `building.filenames` | string[] | no | YAML files containing buildings. |
| `building.replace` | bool | no | `true` replaces vanilla buildings; `false` appends. |
| `resource.filenames` | string[] | no | YAML files containing resources. |
| `technology.filenames` | string[] | no | YAML files containing technologies. |
| `knowledge.filenames` | string[] | no | YAML files containing knowledge entries. |
| `buildingCategory.filenames` | string[] | no | YAML files containing building categories. |

---

## building.yaml — Buildings

Defines all building types. The entry key (e.g. `building_water_mine`) is the building's internal ID.

### Minimal example (from official game data)

```yaml
building_water_mine:
  categoryType: !buildingCategory category_mines
  compactName: WateEx1
  description: BE_building_water_mine_description
  droneCapacity: 1
  healthLossPerDay: 0.05
  inputResources: {}
  knowledge: !knowledge knowledge_water_mine
  maxHealth: 100.0
  name: BE_building_water_mine_name
  outputQuantity: 1
  outputResource: !resource resource_water
  powerConsumption: 10.0
  powerPriority: 3.0
  prefabName: WaterMine_1
  rubblePrefabName: RubblePile_M
  progressPerDay: 0.1
  requiredConstructionResources:
    !resource resource_aluminum: 2
    !resource resource_steel: 2
    !resource resource_glass: 2
  requiredResourceVein: !resource resource_water
  reservedRadius: 30.0
  waySnapRadius: 10.0
  iconName: BuildIcons/Icon_WaterPlant_1
  rivalIconName: BuildIcons_Rival/Icon_WaterPlant_1
  progressBarNames:
    - Progress_Torus
  extractionLevel: 1
```

### Identification

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | ✅ | Localization key for the displayed name (e.g. `BE_building_water_mine_name`). |
| `compactName` | string | no | Short name for compact UI displays (max ~8 chars). |
| `description` | string | ✅ | Localization key for the building description. |
| `namePrefix` | string | no | Prefix shown before the building name. |

### Category & Classification

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `categoryType` | `!buildingCategory` | ✅ | Reference to a building category (e.g. `!buildingCategory category_mines`). |
| `categorySoundOverride` | string | no | Overrides the category sound for this specific building. |
| `knowledge` | `!knowledge` | no | Knowledge entry unlocked when this building is unlocked. |

### Health & Durability

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `maxHealth` | float | — | Maximum HP. Determines resistance to damage and breakdowns. |
| `healthLossPerDay` | float | `0.0` | Daily HP degradation. Forces maintenance. |

### Visuals & Assets

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `prefabName` | string | ✅ | Unity prefab name for the 3D model. Must exist in game assets. |
| `rubblePrefabName` | string | no | Prefab shown when building is destroyed (e.g. `RubblePile_M`). |
| `iconName` | string | ✅ | Sprite path for the main UI icon (e.g. `BuildIcons/Icon_WaterPlant_1`). |
| `rivalIconName` | string | no | Icon variant used for rival/enemy faction buildings. |
| `orbitalIconName` | string | no | Icon shown in orbital/satellite view. |
| `emptyHubIconName` | string | no | Icon shown when the building is idle/empty. |
| `progressBarNames` | string[] | no | Unity animation progress bar names. |

### Production & Extraction

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `outputResource` | `!resource` | `null` | Resource this building produces. |
| `outputQuantity` | int | `0` | Units produced per production cycle. |
| `progressPerDay` | float | — | How much production progress is made per in-game day. `1.0` = one cycle per day. |
| `extractionLevel` | int | no | Tier level for extraction buildings (1, 2, 3…). |
| `inputResources` | map | `{}` | Resources consumed per cycle: `!resource name: quantity`. |
| `requiredResourceVein` | `!resource` | `null` | Resource vein required at this location (e.g. `!resource resource_water`). |

### Construction Cost

| Property | Type | Description |
|----------|------|-------------|
| `requiredConstructionResources` | map | Resources required to build: `!resource name: quantity`. |

```yaml
requiredConstructionResources:
  !resource resource_aluminum: 2
  !resource resource_steel: 2
  !resource resource_glass: 2
```

### Capacities & Storage

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `colonistCapacity` | int | `0` | Number of colonists the building can house or employ. |
| `droneCapacity` | int | `0` | Number of worker drones the building can store. |
| `militaryDroneCapacity` | int | `0` | Number of military drones it can store. |
| `shipCapacity` | int | `0` | Number of ships it can store (spaceports, hangars). |
| `maxStorageCapacity` | int | `0` | Internal resource storage capacity. |

### Energy System

#### Production

| Property | Type | Default | Unit | Description |
|----------|------|---------|------|-------------|
| `maxSolarPowerProduction` | float | `0.0` | kW | Maximum solar power output (varies with sun exposure). |
| `maxEolicPowerProduction` | float | `0.0` | kW | Maximum wind power output. |
| `maxThermalPowerProduction` | float | `0.0` | kW | Maximum geothermal power output. |
| `maxFissionPowerProduction` | float | `0.0` | kW | Maximum nuclear fission output. |
| `maxFusionPowerProduction` | float | `0.0` | kW | Maximum nuclear fusion output. |

#### Consumption & Management

| Property | Type | Default | Unit | Description |
|----------|------|---------|------|-------------|
| `powerConsumption` | float | `0.0` | kW | Energy consumed by this building. |
| `powerPriority` | float | `1.0` | — | Shutdown priority during shortage. Higher = cut last. |
| `energyStorageCapacity` | float | `0.0` | kWh | Built-in battery capacity. |

#### Grid Infrastructure

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `extendsPowerCluster` | bool | `false` | Whether this building extends the power grid. |
| `powerClusterRadius` | float | `0.0` | Radius of power grid extension (game units). |
| `maxPowerTransfer` | float | `0.0` | Maximum power transmission in kW. |

### Placement & Geometry

| Property | Type | Description |
|----------|------|-------------|
| `reservedRadius` | float | Minimum clear area around the building (game units). |
| `waySnapRadius` | float | Snap radius to roads/ways. |
| `enablesHyperloopConnection` | bool | Whether this building can connect to the Hyperloop network. |

### Upgrades

| Property | Type | Description |
|----------|------|-------------|
| `isUpgradeTo` | `!building[]` | List of buildings this is an upgrade of. |

```yaml
building_water_mine_2:
  isUpgradeTo:
    - !building building_water_mine
  # ... rest of properties
```

---

## buildingCategory.yaml — Building categories

Groups buildings in the construction UI.

### Example

```yaml
buildingCategory:
  - !buildingCategory
    index: 10
    name: extraction
    iconName: "icon_category_extraction"
    categorySound: "drilling_machinery"
    factionCountStatId: "faction_extraction_count"
    hidden: false
```

### Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `index` | int | ✅ | Unique ID. **Never modify existing ones.** |
| `name` | string | ✅ | Internal ID used in `!buildingCategory` references. `snake_case`. |
| `iconName` | string | ✅ | Sprite for this category in the build menu. |
| `hidden` | bool | `false` | Hide this category from the UI (useful for internal categories). |
| `categorySound` | string | no | Audio event played when interacting with buildings in this category. |
| `factionCountStatId` | string | no | Stat tracking how many buildings of this category each faction has. |

### Vanilla categories

| Name | Description |
|------|-------------|
| `category_core` | Core / HQ buildings |
| `category_mines` | Resource extraction |
| `category_production` | Manufacturing |
| `category_power` | Energy production |
| `category_storage` | Storage & logistics |
| `category_habitat` | Housing & population |
| `category_research` | Research labs |
| `category_defense` | Military structures |

---

## resource.yaml — Resources

Defines all resource types (mined, manufactured, atmospheric).

### Example (from official game data)

```yaml
resource_aluminum:
  color: A8B3B9
  index: 5
  knowledge: !knowledge knowledge_resource_aluminum
  materialType: Mined
  name: BE_resource_aluminum_name
  prefabName: Aluminum
  veinIconsName:
    - Resource Icons/Veins/Aluminum_Vein
    - Resource Icons/Veins/Aluminum_Vein2
    - Resource Icons/Veins/Aluminum_Vein3
  iconName: Resource Icons/Aluminum
  cubeMaterial: ResourceCube
```

### Properties

#### Identification

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `index` | int | ✅ | Unique ID. **Never modify.** |
| `name` | string | ✅ | Localization key for displayed name. |

#### Visuals

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `color` | string (hex) | ✅ | Hex color used in graphs and indicators (e.g. `A8B3B9`). |
| `iconName` | string | ✅ | Main icon sprite path. |
| `altIconName` | string | no | Alternate icon for special contexts. |
| `orbitalIconName` | string | no | Icon in orbital/satellite view. |
| `altOrbitalIconName` | string | no | Alternate orbital icon. |
| `prefabName` | string | ✅ | Unity prefab for 3D world representation. |
| `cubeMaterial` | string | no | Unity material name for cube display (e.g. `ResourceCube`). |
| `veinIconsName` | string[] | no | Vein icons per extraction level (index 0 = lowest). |

#### Classification

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `materialType` | enum | ✅ | `Mined`, `Manufactured`, `Released`, `Placement`, `Placeholder`. |
| `knowledge` | `!knowledge` | no | Knowledge entry required to unlock this resource. |

#### Gameplay

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `showInScannerLens` | bool | `false` | Visible in the orbital scanner lens. |
| `radius` | float | `0.0` | Detection/influence radius. |

#### Statistics

| Property | Type | Description |
|----------|------|-------------|
| `quantityStatName` | string | Stat name for total stored quantity. |
| `potentialDemandStatName` | string | Stat name for potential demand. |
| `currentDemandStatName` | string | Stat name for current demand. |
| `potentialProductionStatName` | string | Stat name for potential production. |
| `currentProductionStatName` | string | Stat name for current production. |

### `materialType` values

| Value | Description |
|-------|-------------|
| `Mined` | Extracted from deposits (iron, water, silicon…) |
| `Manufactured` | Produced by factories (steel, electronics…) |
| `Released` | Atmospheric byproduct (CO₂, O₂…) |
| `Placement` | Placement objects (drones, ships…) |
| `Placeholder` | Development placeholder |

---

## technology.yaml — Technologies

Defines the research tree.

### Example

```yaml
technology:
  - !technology
    index: 1001
    name: improved_mining_efficiency
    shortName: "Imp. Mining"
    description: technology.improved_mining.description
    categoryName: "engineering"
    position: 5
    IsLane: false
    requiredResearchPoints: 100.0
    requirements:
      - !technology basic_extraction
    unlockAutomatically: false
    isRepeatable: false
    iconName: "icon_improved_mining"
    popupImageName: "popup_improved_mining"
    actions:
      - increase_mining_efficiency: 1.25
      - unlock_building: improved_water_mine
```

### Properties

#### Identification & Metadata

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `index` | int | ✅ | Unique ID. **Never modify.** |
| `name` | string | ✅ | Internal ID. `snake_case`. Used in `!technology` references. |
| `shortName` | string | no | Short display name for compact UI. |
| `description` | string | ✅ | Localization key for detailed description. |

#### Tree Organization

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `categoryName` | string | ✅ | Branch: `engineering`, `biology`, `space`. |
| `position` | int | ✅ | Display position within the branch. |
| `IsLane` | bool | `false` | `true` makes this a main branch node. |
| `laneColor` | Color RGBA | no | Color of the lane (only if `IsLane: true`). |

#### Cost & Prerequisites

| Property | Type | Description |
|----------|------|-------------|
| `requiredResearchPoints` | float | Research points needed to unlock. |
| `requirements` | `!technology[]` | List of prerequisite technologies. |
| `unlockAutomatically` | bool | Auto-unlocks when prerequisites are met. |
| `isRepeatable` | bool | Can be researched multiple times (cumulative bonuses). |

#### Visuals

| Property | Type | Description |
|----------|------|-------------|
| `iconName` | string | Icon in the tech tree. |
| `popupImageName` | string | Main image in the research popup. |
| `popupSecondaryImageName` | string | Secondary image (diagram, etc.). |
| `categoryIconName` | string | Category-specific icon override. |
| `popupOverride` | — | Custom popup configuration. |

#### Effects

| Property | Type | Description |
|----------|------|-------------|
| `actions` | list | Effects triggered when researched. See actions below. |

**Common action types:**
```yaml
actions:
  - unlock_building: building_name       # Unlock a building
  - unlock_resource: resource_name       # Unlock a resource
  - grant_knowledge: knowledge_name      # Grant a knowledge entry
  - modify_efficiency: building, 1.25    # +25% efficiency
  - modify_production_speed: type, 2.0   # Double production speed
```

---

## knowledge.yaml — AMI knowledge base

Defines encyclopedic entries accessible through AMI's knowledge interface.

### Example

```yaml
knowledge:
  - !knowledge
    index: 1001
    name: atmospheric_chemistry_basics
    path: "Sciences/Chemistry/Atmospheric"
    localizedPath: "Sciences/Chimie/Atmosphérique"
    title: "knowledge.atmospheric_chemistry.title"
    content: "knowledge.atmospheric_chemistry.content"
    contentAMI: "knowledge.atmospheric_chemistry.ami"
    image: "knowledge_atmospheric_chemistry"
    iconName: "icon_chemistry_knowledge"
    contentTable:
      - field: "CO₂ Concentration"
        text: "95.32% of atmosphere"
      - field: "Pressure at Surface"
        text: "0.636 kPa"
```

### Properties

#### Identification & Navigation

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `index` | int | ✅ | Unique ID. **Never modify.** |
| `name` | string | ✅ | Internal ID. Used in `!knowledge` references. |
| `path` | string | ✅ | Hierarchy path in AMI browser (e.g. `Technologies/Engineering/Materials`). |
| `localizedPath` | string | no | Localized version of path for display. |

#### Content

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `title` | string | ✅ | Localization key for the article title. |
| `content` | string | ✅ | Localization key for the main content (supports rich text). |
| `contentAMI` | string | no | AMI-specific version of the content (different tone). |

#### Visuals

| Property | Type | Description |
|----------|------|-------------|
| `image` | string | Main illustration sprite. |
| `iconName` | string | Small icon for navigation lists. |

#### Structured Data

| Property | Type | Description |
|----------|------|-------------|
| `contentTable` | `{field, text}[]` | Technical data table with field/value rows. |
| `contentBuilding` | `!building` | Links this knowledge to a specific building. |
| `contentProject` | `!specialProject` | Links this knowledge to a special project. |

```yaml
# Data table example
contentTable:
  - field: "Melting Point"
    text: "1,538°C"
  - field: "Density"
    text: "7.87 g/cm³"

# Building link example
contentBuilding: !building building_steel_mill
```

---

## YAML Tag Reference

These YAML tags are used throughout all mod files to create cross-references between game objects.

| Tag | References | Example |
|-----|-----------|---------|
| `!building` | A building type | `!building building_water_mine` |
| `!buildingCategory` | A building category | `!buildingCategory category_mines` |
| `!resource` | A resource type | `!resource resource_water` |
| `!technology` | A technology | `!technology basic_extraction` |
| `!knowledge` | A knowledge entry | `!knowledge knowledge_water_mine` |
| `!specialProject` | A special project | `!specialProject project_terraforming` |
| `!quest` | A quest | `!quest quest_first_water` |

---

## Full Mod Example

A complete minimal mod adding one new building:

**`manifest.yaml`**
```yaml
modId: "MyFirstMod"
compatibleGameVersions:
  - "1.8.x"
building:
  filenames:
    - building.yaml
  replace: false
```

**`building.yaml`**
```yaml
building_my_advanced_mine:
  categoryType: !buildingCategory category_mines
  name: "My Advanced Mine"
  description: "An improved extraction building."
  compactName: AdvMine
  maxHealth: 150.0
  healthLossPerDay: 0.3
  prefabName: WaterMine_2
  iconName: BuildIcons/Icon_WaterPlant_1
  rubblePrefabName: RubblePile_M
  outputResource: !resource resource_water
  outputQuantity: 1
  progressPerDay: 0.15
  requiredResourceVein: !resource resource_water
  powerConsumption: 15.0
  powerPriority: 3.0
  droneCapacity: 2
  requiredConstructionResources:
    !resource resource_aluminum: 5
    !resource resource_steel: 3
  reservedRadius: 30.0
  waySnapRadius: 10.0
```

---

*Reference built from decompiled IL2CPP classes and validated against `Per Aspera/datamodel/`. See also: [Reference-Building-YAML-Properties](Reference-Building-YAML-Properties), [Reference-Manifest-YAML](Reference-Manifest-YAML).*
