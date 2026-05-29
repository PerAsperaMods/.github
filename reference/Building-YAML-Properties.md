# Building YAML Properties Reference

Complete reference for all `building.yaml` properties available for modding, derived from the `BuildingType` decompiled class.

**Key rule**: Per Aspera uses a custom YAML deserializer that reads fields **by name via reflection**. Fields declared as `private`, `protected`, or `public` in C# are **all writable from YAML**. C# access modifiers do not determine whether a field can be set in YAML.

> **YAML key = C# field name** (without leading underscore, without compiler-generated `_003CX_003Ek__BackingField` suffix for auto-properties).

---

## Table of Contents

- [Identity & Display](#identity--display)
- [Category & Unlock](#category--unlock)
- [Resources & Production](#resources--production)
- [Health & Durability](#health--durability)
- [Visuals & Placement](#visuals--placement)
- [Workers](#workers)
- [Power & Energy](#power--energy)
- [Military & Defense](#military--defense)
- [Upgrades](#upgrades)
- [Maintenance](#maintenance)
- [Infrastructure & Storage](#infrastructure--storage)
- [Atmosphere & Environment](#atmosphere--environment)
- [Terrain & Placement Constraints](#terrain--placement-constraints)
- [Ships & Transport](#ships--transport)
- [Coastal & Water](#coastal--water)
- [Farming](#farming)
- [Pipes / Water Network](#pipes--water-network)
- [Districts & Hubs](#districts--hubs)
- [Notes on Field Name Conventions](#notes-on-field-name-conventions)

---

## Identity & Display

| YAML key | Type | Description |
|----------|------|-------------|
| `name` | `string` | Building name (localization key or plain text) |
| `namePrefix` | `string` | Prefix displayed before the name |
| `compactName` | `string` | Short name for compact UI display |
| `description` | `string` | Building description (localization key or plain text) |
| `notEnoughBuildings` | `string` | Message shown when not enough buildings are present |

---

## Category & Unlock

| YAML key | Type | Description |
|----------|------|-------------|
| `categoryType` | `!buildingCategory` | UI category this building belongs to |
| `knowledge` | `!knowledge` | Knowledge required to unlock this building |
| `availability` | `Availability` | Availability condition |

---

## Resources & Production

| YAML key | Type | Description |
|----------|------|-------------|
| `inputResources` | `Dictionary<!resource, CargoQuantity>` | Resources consumed per production cycle |
| `inputResourcesKeys` | `List<!resource>` | Input resource keys (controls display order) |
| `extractionLevel` | `int` | Extraction tier (1, 2, 3…) |
| `requiredResourceVein` | `!resource` | Vein type required at placement location |
| `scalesToResourceVein` | `bool` | Production scales with vein richness |
| `displayInputs` | `List<!resource>` | Resources shown as inputs in UI (overrides auto-detection) |
| `displayOutputs` | `List<!resource>` | Resources shown as outputs in UI (overrides auto-detection) |
| `outputResource` | `!resource` | Resource produced by this building |
| `outputQuantity` | `CargoQuantity` | Amount produced per cycle |
| `progressPerDay` | `float` | Cycle progress per day (1.0 = 1 day/cycle) |
| `producingLabel` | `string` | Label shown in the production UI |
| `clearOutQuantity` | `CargoQuantity` | Amount ejected during a clearout |
| `clearoutThreshold` | `float` | Storage fill level that triggers a clearout |
| `requiredConstructionResources` | `Dictionary<!resource, CargoQuantity>` | Resources needed to construct this building |
| `initialStock` | `Dictionary<!resource, CargoQuantity>` | Initial resource stock when the building is placed |

**Example:**
```yaml
building_my_processor:
  inputResources:
    !resource resource_iron_ore: 10
  outputResource: !resource resource_steel
  outputQuantity: 5
  progressPerDay: 1.0
  requiredConstructionResources:
    !resource resource_metal: 150
    !resource resource_concrete: 50
  displayInputs:
    - !resource resource_iron_ore
  displayOutputs:
    - !resource resource_steel
```

---

## Health & Durability

| YAML key | Type | Description |
|----------|------|-------------|
| `maxHealth` | `float` | Maximum health points |
| `healthLossPerDay` | `float` | Health lost per day (0 = no decay) |

---

## Visuals & Placement

| YAML key | Type | Description |
|----------|------|-------------|
| `prefabName` | `string` | Unity 3D prefab name |
| `rubblePrefabName` | `string` | Rubble prefab shown after destruction |
| `iconName` | `Sprite` | Icon in the construction menus |
| `rivalIconName` | `Sprite` | Alternate icon for rival factions |
| `orbitalIconName` | `Sprite` | Icon in orbital view |
| `emptyHubIconName` | `Sprite` | Icon when the hub is empty |
| `progressBarNames` | `List<string>` | Names of progress bar indicators |
| `textureOverrides` | `List<TextureOverrideData>` | Custom texture overrides for the model |
| `categorySoundOverride` | `string` | Custom category sound |
| `waySnapRadius` | `float` | Snap radius to roads/paths |
| `reservedRadius` | `float` | Clearance zone around the building |
| `jumpRadius` | `float` | Maximum drone jump/connection radius |

---

## Workers

| YAML key | Type | Description |
|----------|------|-------------|
| `colonistCapacity` | `int` | Number of colonists housed or employed |
| `droneCapacity` | `int` | Number of worker drones this building supports |

---

## Power & Energy

| YAML key | Type | Description |
|----------|------|-------------|
| `maxSolarPowerProduction` | `float` | Maximum solar power output (kW) |
| `maxEolicPowerProduction` | `float` | Maximum wind power output (kW) |
| `maxThermalPowerProduction` | `float` | Maximum thermal power output (kW) |
| `maxFissionPowerProduction` | `float` | Maximum nuclear fission output (kW) |
| `maxFusionPowerProduction` | `float` | Maximum nuclear fusion output (kW) |
| `powerConsumption` | `float` | Power consumed when operating (kW) |
| `powerPriority` | `float` | Priority in power allocation (higher = last to lose power) |
| `energyStorageCapacity` | `float` | Built-in energy storage capacity (kWh) |
| `extendsPowerCluster` | `bool` | Extends the power grid network |
| `powerClusterRadius` | `float` | Power grid extension radius |
| `maxPowerTransfer` | `float` | Maximum power transfer rate |

---

## Military & Defense

| YAML key | Type | Description |
|----------|------|-------------|
| `militaryDroneCapacity` | `int` | Number of military drones hosted |
| `defensiveMissilesPerDay` | `float` | Defensive missiles launched per day |
| `missileRange` | `float` | Missile engagement range |
| `missileDamage` | `float` | Damage per missile |
| `scannerScanRadius` | `float` | Scanner detection radius |
| `scannerScanTime` | `float` | Duration of one scan |
| `scannerTimeFactor` | `float` | Scanner speed multiplier |
| `spawnRevealRadius` | `float` | Fog-of-war reveal radius on placement |

---

## Upgrades

| YAML key | Type | Description |
|----------|------|-------------|
| `isUpgradeTo` | `List<!building>` | Buildings that this building can upgrade from |

```yaml
building_water_mine_2:
  isUpgradeTo:
    - !building building_water_mine_1
```

---

## Maintenance

| YAML key | Type | Description |
|----------|------|-------------|
| `maintenancePriority` | `float` | Maintenance priority level |
| `extendsMaintenanceCluster` | `bool` | Extends the maintenance network |
| `maintenanceClusterRadius` | `float` | Maintenance network extension radius |

---

## Infrastructure & Storage

| YAML key | Type | Description |
|----------|------|-------------|
| `enablesHyperloopConnection` | `bool` | Allows hyperloop connection |
| `maxStorageCapacity` | `int` | Internal resource storage capacity |
| `hasSpacePort` | `bool` | Has a space port for special projects |
| `nodeType` | `NodeType` | Network node type |

---

## Atmosphere & Environment

| YAML key | Type | Description |
|----------|------|-------------|
| `co2ConversionPerDay` | `float` | CO2 converted per day |
| `gasReleaseAmount` | `float` | Gas released into the atmosphere |
| `canSpawnLichenAndPlants` | `bool` | Can trigger lichen and plant spawning |
| `canSpawnCyanobacteria` | `bool` | Can trigger cyanobacteria spawning |
| `deforestationRadius` | `float` | Radius of deforestation effect |
| `deforestationTime` | `float` | Time to complete deforestation |
| `humidityRadius` | `float` | Radius of humidity effect |
| `humidityPercentege` | `float` | Humidity percentage generated |

---

## Terrain & Placement Constraints

| YAML key | Type | Description |
|----------|------|-------------|
| `needsEquatorialStrip` | `bool` | Must be placed on the equatorial strip |
| `needsPressure` | `bool` | Requires atmospheric pressure to operate |
| `needsBreathableAtmosphere` | `bool` | Requires a breathable atmosphere |
| `orbitalPlacement` | `bool` | Placed from orbit (not on the surface) |
| `needsEvenTerrain` | `bool` | Requires flat terrain |
| `isBaseStarter` | `bool` | Can serve as the starting building for a new base |

---

## Ships & Transport

| YAML key | Type | Description |
|----------|------|-------------|
| `shipCapacity` | `int` | Number of ships this building can host |

---

## Coastal & Water

| YAML key | Type | Description |
|----------|------|-------------|
| `lookToCoast` | `bool` | Must face the coastline |
| `coastMaxDistance` | `float` | Maximum allowed distance to the coast |
| `waterSampleDistance` | `float` | Distance used when sampling water presence |
| `lookToCoastPrecision` | `float` | Coastal orientation precision |
| `lookToCoastSegmentationPrecision` | `float` | Coastal segmentation precision |
| `minimunWaterSamples` | `int` | Minimum number of valid water samples required |
| `isAquaticVersionOf` | `!building` | Aquatic variant of another building type |
| `prefabZeppelinOverride` | `string` | Override prefab for zeppelin variant |
| `waterNeededFactor` | `float` | Water proximity requirement factor |
| `waterNeededRadius` | `float` | Radius used to detect nearby water |

---

## Farming

| YAML key | Type | Description |
|----------|------|-------------|
| `farmMinRequiredTerrain` | `float` | Minimum terrain surface area required |
| `farmSpawnRadius` | `float` | Radius in which crops are spawned |

---

## Pipes / Water Network

| YAML key | Type | Description |
|----------|------|-------------|
| `pipesProduction` | `float` | Amount injected into the pipe network per day |
| `pipesConsumption` | `float` | Amount consumed from the pipe network per day |
| `extendsPipesCluster` | `bool` | Extends the pipe network |
| `pipesClusterRadius` | `float` | Pipe network extension radius |

---

## Districts & Hubs

| YAML key | Type | Description |
|----------|------|-------------|
| `isWorkerHub` | `bool` | Acts as a worker drone hub |
| `isDistrictHub` | `bool` | Acts as a district hub |
| `isAnimalSanctuary` | `bool` | Acts as an animal sanctuary |

---

## Notes on Field Name Conventions

### Auto-property backing fields
Some C# auto-properties generate backing fields with mangled names. Use the clean property name in YAML:
- `_003CcompactName_003Ek__BackingField` → YAML key: `compactName`
- `_003Cknowledge_003Ek__BackingField` → YAML key: `knowledge`

### Underscore-prefixed fields
Fields with a leading `_` drop the underscore in YAML:
- `_scalesToResourceVein` → YAML key: `scalesToResourceVein`
- `_waySnapRadius` → YAML key: `waySnapRadius`
- `_reservedRadius` → YAML key: `reservedRadius`
