# Building YAML Properties Reference

## 📚 Source

**Source authoritative** : `buildingParamlist.csv` — liste complète des champs C# de la classe `BuildingType` extraite par décompilation IL2CPP.

**Règle clé** : Per Aspera utilise un désérialiseur YAML custom par **réflexion sur le nom du champ**. Les champs `private`, `protected` et `public` sont **tous sérialisables** depuis YAML. Ne pas se fier à l'accessibilité C# pour décider si un attribut peut être défini en YAML.

> **YAML key = nom du champ C#** (sans underscore préfixe, sans `_003C..._003Ek__BackingField` pour les backing fields de propriétés auto).

---

## 📑 Table of Contents

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
- [Humidity](#humidity)

---

## Identity & Display

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `name` | `name` | `string` | Nom du bâtiment (clé de localisation ou texte brut) |
| `namePrefix` | `namePrefix` | `string` | Préfixe affiché avant le nom |
| `compactName` | `_003CcompactName_003Ek__BackingField` | `string` | Nom court pour l'UI compacte |
| `description` | `description` | `string` | Description (clé de localisation ou texte brut) |
| `notEnoughBuildings` | `notEnoughBuildings` | `string` | Message quand le nombre de bâtiments est insuffisant |

---

## Category & Unlock

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `categoryType` | `categoryType` | `!buildingCategory` | Catégorie UI du bâtiment |
| `knowledge` | `_003Cknowledge_003Ek__BackingField` | `!knowledge` | Connaissance requise pour débloquer |
| `availability` | `availability` | `Availability` | Condition de disponibilité |

---

## Resources & Production

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `inputResources` | `inputResources` | `Dictionary<!resource, CargoQuantity>` | Ressources consommées par cycle |
| `inputResourcesKeys` | `inputResourcesKeys` | `List<!resource>` | Clés des ressources en entrée (ordre d'affichage) |
| `extractionLevel` | `extractionLevel` | `int` | Niveau d'extraction (1, 2, 3…) |
| `requiredResourceVein` | `requiredResourceVein` | `!resource` | Type de gisement requis à l'emplacement |
| `scalesToResourceVein` | `_scalesToResourceVein` | `bool` | Production proportionnelle à la richesse du gisement |
| `displayInputs` | `displayInputs` | `List<!resource>` | Ressources affichées comme entrées dans l'UI (override) |
| `displayOutputs` | `displayOutputs` | `List<!resource>` | Ressources affichées comme sorties dans l'UI (override) |
| `outputResource` | `outputResource` | `!resource` | Ressource produite |
| `outputQuantity` | `outputQuantity` | `CargoQuantity` | Quantité produite par cycle |
| `progressPerDay` | `progressPerDay` | `float` | Avancement par jour (1.0 = 1 jour/cycle) |
| `producingLabel` | `producingLabel` | `string` | Label de production affiché dans l'UI |
| `clearOutQuantity` | `clearOutQuantity` | `CargoQuantity` | Quantité évacuée lors d'un clearout |
| `clearoutThreshold` | `clearoutThreshold` | `float` | Seuil de remplissage déclenchant le clearout |
| `requiredConstructionResources` | `requiredConstructionResources` | `Dictionary<!resource, CargoQuantity>` | Ressources nécessaires à la construction |
| `initialStock` | `initialStock` | `Dictionary<!resource, CargoQuantity>` | Stock initial au démarrage |

**Exemple :**
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

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `maxHealth` | `maxHealth` | `float` | Points de vie maximum |
| `healthLossPerDay` | `healthLossPerDay` | `float` | Dégradation par jour (0 = pas de dégradation) |

---

## Visuals & Placement

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `prefabName` | `prefabName` | `string` | Nom du prefab 3D Unity |
| `rubblePrefabName` | `rubblePrefabName` | `string` | Prefab des décombres après destruction |
| `iconName` | `iconName` | `Sprite` | Icône dans les menus de construction |
| `rivalIconName` | `rivalIconName` | `Sprite` | Icône variante rivale |
| `orbitalIconName` | `orbitalIconName` | `Sprite` | Icône vue orbitale |
| `emptyHubIconName` | `emptyHubIconName` | `Sprite` | Icône hub vide |
| `progressBarNames` | `progressBarNames` | `List<string>` | Noms des barres de progression |
| `textureOverrides` | `textureOverrides` | `List<TextureOverrideData>` | Overrides de texture du modèle |
| `categorySoundOverride` | `categorySoundOverride` | `string` | Son de catégorie personnalisé |
| `waySnapRadius` | `_waySnapRadius` | `float` | Rayon de snap sur les routes |
| `reservedRadius` | `_reservedRadius` | `float` | Zone de dégagement autour du bâtiment |
| `jumpRadius` | `jumpRadius` | `float` | Rayon de saut pour les drones |

---

## Workers

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `colonistCapacity` | `colonistCapacity` | `int` | Nombre de colons logés ou employés |
| `droneCapacity` | `droneCapacity` | `int` | Nombre de drones de travail |

---

## Power & Energy

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `maxSolarPowerProduction` | `maxSolarPowerProduction` | `float` | Production solaire maximale (kW) |
| `maxEolicPowerProduction` | `maxEolicPowerProduction` | `float` | Production éolienne maximale (kW) |
| `maxThermalPowerProduction` | `maxThermalPowerProduction` | `float` | Production thermique maximale (kW) |
| `maxFissionPowerProduction` | `maxFissionPowerProduction` | `float` | Production par fission (kW) |
| `maxFusionPowerProduction` | `maxFusionPowerProduction` | `float` | Production par fusion (kW) |
| `powerConsumption` | `powerConsumption` | `float` | Consommation électrique (kW) |
| `powerPriority` | `powerPriority` | `float` | Priorité dans l'allocation d'énergie |
| `energyStorageCapacity` | `energyStorageCapacity` | `float` | Capacité de stockage d'énergie (kWh) |
| `extendsPowerCluster` | `extendsPowerCluster` | `bool` | Étend le réseau électrique |
| `powerClusterRadius` | `powerClusterRadius` | `float` | Rayon d'extension du réseau électrique |
| `maxPowerTransfer` | `maxPowerTransfer` | `float` | Transfert électrique maximum |

---

## Military & Defense

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `militaryDroneCapacity` | `militaryDroneCapacity` | `int` | Nombre de drones militaires |
| `defensiveMissilesPerDay` | `defensiveMissilesPerDay` | `float` | Missiles défensifs lancés par jour |
| `missileRange` | `missileRange` | `float` | Portée des missiles |
| `missileDamage` | `missileDamage` | `float` | Dégâts par missile |
| `scannerScanRadius` | `scannerScanRadius` | `float` | Rayon de détection du scanner |
| `scannerScanTime` | `scannerScanTime` | `float` | Durée d'un scan |
| `scannerTimeFactor` | `scannerTimeFactor` | `float` | Facteur de vitesse du scanner |
| `spawnRevealRadius` | `spawnRevealRadius` | `float` | Rayon de révélation à la pose |

---

## Upgrades

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `isUpgradeTo` | `isUpgradeTo` | `List<!building>` | Bâtiments que celui-ci peut upgrader |

```yaml
building_water_mine_2:
  isUpgradeTo:
    - !building building_water_mine_1
```

---

## Maintenance

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `maintenancePriority` | `maintenancePriority` | `float` | Priorité de maintenance |
| `extendsMaintenanceCluster` | `extendsMaintenanceCluster` | `bool` | Étend le réseau de maintenance |
| `maintenanceClusterRadius` | `maintenanceClusterRadius` | `float` | Rayon d'extension du réseau de maintenance |

---

## Infrastructure & Storage

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `enablesHyperloopConnection` | `enablesHyperloopConnection` | `bool` | Permet la connexion hyperloop |
| `maxStorageCapacity` | `maxStorageCapacity` | `int` | Capacité de stockage interne |
| `hasSpacePort` | `hasSpacePort` | `bool` | Possède un port spatial |
| `nodeType` | `nodeType` | `NodeType` | Type de nœud réseau |

---

## Atmosphere & Environment

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `co2ConversionPerDay` | `co2ConversionPerDay` | `float` | CO2 converti par jour |
| `gasReleaseAmount` | `gasReleaseAmount` | `float` | Quantité de gaz relâchée |
| `canSpawnLichenAndPlants` | `canSpawnLichenAndPlants` | `bool` | Peut faire apparaître lichens et plantes |
| `canSpawnCyanobacteria` | `canSpawnCyanobacteria` | `bool` | Peut faire apparaître des cyanobactéries |
| `deforestationRadius` | `deforestationRadius` | `float` | Rayon de déforestation |
| `deforestationTime` | `deforestationTime` | `float` | Durée de déforestation |
| `humidityRadius` | `humidityRadius` | `float` | Rayon d'effet d'humidité |
| `humidityPercentege` | `humidityPercentege` | `float` | Pourcentage d'humidité généré |

---

## Terrain & Placement Constraints

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `needsEquatorialStrip` | `needsEquatorialStrip` | `bool` | Doit être placé sur la bande équatoriale |
| `needsPressure` | `needsPressure` | `bool` | Requiert une pression atmosphérique |
| `needsBreathableAtmosphere` | `needsBreathableAtmosphere` | `bool` | Requiert une atmosphère respirable |
| `orbitalPlacement` | `orbitalPlacement` | `bool` | Placement orbital (hors sol) |
| `needsEvenTerrain` | `needsEvenTerrain` | `bool` | Requiert un terrain plat |
| `isBaseStarter` | `isBaseStarter` | `bool` | Bâtiment de départ de base |

---

## Ships & Transport

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `shipCapacity` | `shipCapacity` | `int` | Capacité de vaisseaux |

---

## Coastal & Water

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `lookToCoast` | `lookToCoast` | `bool` | Doit faire face à la côte |
| `coastMaxDistance` | `coastMaxDistance` | `float` | Distance maximum à la côte |
| `waterSampleDistance` | `waterSampleDistance` | `float` | Distance d'échantillonnage de l'eau |
| `lookToCoastPrecision` | `lookToCoastPrecision` | `float` | Précision d'orientation côtière |
| `lookToCoastSegmentationPrecision` | `lookToCoastSegmentationPrecision` | `float` | Précision de segmentation côtière |
| `minimunWaterSamples` | `minimunWaterSamples` | `int` | Nombre minimum d'échantillons d'eau |
| `isAquaticVersionOf` | `isAquaticVersionOf` | `!building` | Version aquatique d'un autre bâtiment |
| `prefabZeppelinOverride` | `prefabZeppelinOverride` | `string` | Override de prefab zeppelin |
| `waterNeededFactor` | `waterNeededFactor` | `float` | Facteur de besoin en eau |
| `waterNeededRadius` | `waterNeededRadius` | `float` | Rayon de détection de l'eau |

---

## Farming

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `farmMinRequiredTerrain` | `farmMinRequiredTerrain` | `float` | Surface de terrain minimale requise |
| `farmSpawnRadius` | `farmSpawnRadius` | `float` | Rayon de spawn des cultures |

---

## Pipes / Water Network

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `pipesProduction` | `pipesProduction` | `float` | Production injectée dans le réseau de tuyaux |
| `pipesConsumption` | `pipesConsumption` | `float` | Consommation depuis le réseau de tuyaux |
| `extendsPipesCluster` | `extendsPipesCluster` | `bool` | Étend le réseau de tuyaux |
| `pipesClusterRadius` | `pipesClusterRadius` | `float` | Rayon d'extension du réseau de tuyaux |

---

## Districts & Hubs

| YAML key | C# field | Type | Description |
|----------|----------|------|-------------|
| `isWorkerHub` | `isWorkerHub` | `bool` | Est un hub de travailleurs |
| `isDistrictHub` | `isDistrictHub` | `bool` | Est un hub de district |
| `isAnimalSanctuary` | `isAnimalSanctuary` | `bool` | Est un sanctuaire pour animaux |

---

## Notes

### Backing fields (propriétés auto C#)
Les champs `_003CX_003Ek__BackingField` sont des backing fields de propriétés auto générés par le compilateur C#. Le nom YAML correspond au nom de la propriété sans le préfixe/suffixe :
- `_003CcompactName_003Ek__BackingField` → YAML key : `compactName`
- `_003Cknowledge_003Ek__BackingField` → YAML key : `knowledge`

### Champs avec underscore préfixe
- `_scalesToResourceVein` → YAML key : `scalesToResourceVein`
- `_waySnapRadius` → YAML key : `waySnapRadius`
- `_reservedRadius` → YAML key : `reservedRadius`
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