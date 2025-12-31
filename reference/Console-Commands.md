# Console Commands Reference (Debug/Cheat)

## 📚 Introduction

This reference documents **debug and cheat console commands** available in Per Aspera for testing and development. These commands are accessed by pressing **`~`** key in-game and are different from [Game Commands](Game-Commands.md) which are programmatic.

**⚠️ Important Warnings:**
- **Disables achievements permanently** for the current savegame
- **Debug/testing only** - not for normal gameplay
- **Use with caution** - can break game state
- Based on **official modding manual** but may contain obsolete information

**Source:** ISA Quantum AI Laboratory Modding Manual v2.0

---

## 🛠️ General Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `Commands` | `[filter]` | Lists all available console commands with optional filter |
| `Help` | `<command>` | Show detailed help for specific command |
| `History` | `[count]` | Show command history with optional last count limit |
| `TakeScreenshot` | `[multiplier]` | Takes screenshot with optional resolution multiplier |
| `SetEngineTimescale` | `<float>` | Sets engine time scale (speed up/slow down) |

**Example:**
```bash
~ Commands building
~ Help UnlockBuilding
~ TakeScreenshot 2
```

---

## ⌨️ Console Bindings

| Command | Parameters | Description |
|---------|------------|-------------|
| `Bind` | `<KeyCode> <Command>` | Binds keyboard key to console command |
| `SaveBindings` | | Save current key bindings to file |
| `Unbind` | `<KeyCode>` | Remove binding from specified key |
| `UnbindAll` | | Remove all current key bindings |
| `ShowBindings` | | Display all current key bindings |

**Example:**
```bash
~ Bind U UnlockAllBuildings
~ Bind F FillPopulation
~ SaveBindings
```

---

## 🏗️ Building & Construction Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `UnlockBuilding` | `<BuildingKey>` | Unlock specific building type |
| `UnlockAllBuildings` | `[upgradeLevel]` | Unlock all buildings, optionally filtered by upgrade level |
| `UnlockMilitaryBuildings` | | Unlock all military/defense buildings |
| `FinishConstructions` | | Instantly complete all pending construction projects |
| `FinishUpgrades` | | Instantly complete all pending building upgrades |
| `Kill` | | Destroy currently selected building or drone |
| `BuildingSetHealth` | `<percentage>` | Set selected building health (0-100%) |
| `BuildingAddResource` | `<ResourceType> <Amount>` | Add resources to selected building's storage |
| `BuildingSetVeinQuantity` | `<Amount>` | Set resource vein quantity for selected building |

**Example:**
```bash
~ UnlockBuilding SolarPanel
~ UnlockAllBuildings 2
~ FinishConstructions
~ BuildingSetHealth 50
```

---

## 🧪 Research & Technology Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `UnlockAllTech` | | Unlock all technologies in tech tree |
| `UnlockAllKnowledges` | | Unlock all knowledge base entries |
| `UnlockAllSpecialProjects` | | Unlock all special orbital projects |
| `UnlockSpecialProject` | `<ProjectKey>` | Unlock specific special project |
| `ShowCurrentTechPopup` | | Force display current research technology popup |

**Example:**
```bash
~ UnlockAllTech
~ UnlockSpecialProject Terraforming
~ ShowCurrentTechPopup
```

---

## 🌍 Environment & Planet Commands

### Atmosphere Manipulation

| Command | Parameters | Description |
|---------|------------|-------------|
| `IncreaseGHG` | `<Amount>` | Increase greenhouse gas pressure (mbars) |
| `IncreaseN2` | `<Amount>` | Increase nitrogen pressure (mbars) |
| `IncreaseO2` | `<Amount>` | Increase oxygen pressure (mbars) |
| `IncreaseWater` | `<Amount>` | Increase liquid water level (meters) |

**Example:**
```bash
~ IncreaseO2 50
~ IncreaseN2 100
~ IncreaseWater 0.5
```

### Resource & Site Discovery

| Command | Parameters | Description |
|---------|------------|-------------|
| `RevealAllResourceVeins` | | Reveal all resource deposits on planet |
| `RevealAllSpecialSites` | | Reveal all special locations (caves, sites) |
| `RevealAllCraters` | | Reveal all usable crater locations |
| `RevealSpecialSite` | `<SiteKey>` | Reveal specific special site by identifier |
| `RevealScannerArea` | `<radius>` | Reveal area around mouse cursor with specified radius |

**Example:**
```bash
~ RevealAllResourceVeins
~ RevealSpecialSite CaveSystem_01
~ RevealScannerArea 500
```

---

## ⚡ Faction & Resource Commands

### Faction Management

| Command | Parameters | Description |
|---------|------------|-------------|
| `FactionSelect` | `<FactionID>` | Select faction as target for faction commands |
| `FactionList` | | List all factions in game |
| `FactionAddResourceDistributed` | `<ResourceType> <Amount>` | Add resources distributed across all faction buildings |
| `FactionAddResearchPoints` | `<Amount>` | Add research points to selected faction |

**Example:**
```bash
~ FactionSelect Player
~ FactionAddResourceDistributed Iron 1000
~ FactionAddResearchPoints 500
```

### Enhancement System

| Command | Parameters | Description |
|---------|------------|-------------|
| `FactionListEnhancements` | | List all enhancements for selected faction |
| `FactionEnableEnhancement` | `<EnhancementKey>` | Enable specific enhancement for faction |
| `FactionDisableEnhancement` | `<EnhancementKey>` | Disable specific enhancement for faction |

**Example:**
```bash
~ FactionListEnhancements
~ FactionEnableEnhancement AdvancedSolar
```

### Drone Management

| Command | Parameters | Description |
|---------|------------|-------------|
| `FactionListDrones` | | List all worker drones and their status |
| `FactionListMaintenanceDrones` | | List all maintenance drones and their status |
| `FactionSpawnDrones` | `<Amount>` | Spawn worker drones (at selected building if applicable) |
| `FactionSpawnMaintenanceDrones` | `<Amount>` | Spawn maintenance drones for selected faction |
| `FactionSpawnMiliDrones` | `<Amount>` | Spawn military drones for selected faction |
| `FactionKillMilitaryDrones` | | Destroy all military drones for selected faction |

**Example:**
```bash
~ FactionSpawnDrones 5
~ FactionListMaintenanceDrones
~ FactionKillMilitaryDrones
```

---

## 🌪️ Hazards & Events Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `SpawnSandstorm` | | Create sandstorm at mouse cursor position |
| `SpawnDevil` | | Create dust devil at mouse cursor position |
| `SpawnAsteroid` | | Create asteroid impact at mouse cursor position |
| `DespawnAllHazards` | | Remove all active hazards from planet |
| `SpawnRivalBase` | | Create rival faction base |

**Example:**
```bash
~ SpawnSandstorm
~ DespawnAllHazards
~ SpawnRivalBase
```

---

## 👥 Population & Colony Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `FillPopulation` | | Increase all colony populations to maximum capacity |
| `BunchOfResources` | | Add large amount of resources to selected building or main base |

**Example:**
```bash
~ FillPopulation
~ BunchOfResources
```

---

## 🗓️ Time & Date Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `SetDaysSinceStart` | `<Days>` | Set current game date (days from start) |
| `AddYearsSinceStart` | `<Years>` | Add specified years to current game time |

**Example:**
```bash
~ SetDaysSinceStart 365
~ AddYearsSinceStart 2
```

---

## 🗣️ Dialogue & Story Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `ListDialogues` | | Show list of all available dialogues |
| `ListPersons` | | Show list of dialogue characters and their info |
| `StartDialogue` | `<DialogueKey>` | Start specific dialogue by identifier |
| `RunInteractionRule` | `<RuleKey>` | Execute specific interaction rule manually |
| `RunTextAction` | `<ActionKey>` | Execute specific text action manually |

**Example:**
```bash
~ ListDialogues
~ StartDialogue WelcomeMessage
~ ListPersons
```

---

## 🗺️ Sector & Quest Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `ListSectors` | | Show list of player sectors and their status |
| `UnlockSector` | `<SectorIndex>` | Unlock sector by numerical index |
| `UnlockQuest` | `<QuestKey>` | Unlock specific quest by identifier |

**Example:**
```bash
~ ListSectors
~ UnlockSector 3
~ UnlockQuest MainObjective_02
```

---

## 🛣️ Transportation Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `UnlockAllWays` | | Unlock all transportation methods (roads, tubes, etc.) |

**Example:**
```bash
~ UnlockAllWays
```

---

## 🎛️ Debug & System Commands

| Command | Parameters | Description |
|---------|------------|-------------|
| `ShowBlackboard` | | Print all blackboard variables to console |
| `AdditionalLandingSite` | | Add additional landing site launch for player |
| `AdditionalOrbitalBuilding` | `<BuildingKey>` | Add additional orbital building launch |

**Example:**
```bash
~ ShowBlackboard
~ AdditionalOrbitalBuilding SpaceElevator
```

---

## 🚀 Quick Start Combinations

### New Game Boost
```bash
~ UnlockAllBuildings
~ FactionAddResourceDistributed Iron 5000
~ FactionAddResourceDistributed Electronics 2000
~ RevealAllResourceVeins
~ FactionAddResearchPoints 1000
```

### Terraforming Fast-Track
```bash
~ IncreaseO2 200
~ IncreaseN2 300
~ IncreaseWater 2.0
~ UnlockAllSpecialProjects
~ FillPopulation
```

### Testing Environment
```bash
~ Bind T UnlockAllTech
~ Bind B UnlockAllBuildings
~ Bind R RevealAllResourceVeins
~ Bind F FillPopulation
~ SaveBindings
```

---

## ⚠️ Important Considerations

### Achievement Impact
- **Permanent disable** - Using any console command disables achievements for the entire savegame
- **No recovery** - Cannot be undone, even if you save/reload
- **Use separate saves** for testing vs normal play

### Game State Integrity
- **Save before experimenting** - Some commands can break game progression
- **Test incrementally** - Don't use multiple major commands at once
- **Monitor performance** - Large resource additions can cause lag

### Validation Notes
- **Source authenticity** - From official ISA modding manual v2.0
- **Possible obsolescence** - Some commands may not work in current game version
- **Parameter accuracy** - Exact parameter formats may have changed

---

## 🔗 Related Documentation

- **[Game Commands](Game-Commands.md)** - Programmatic game commands (different system)
- **[Building Types](Building-Types.md)** - Valid building keys for unlock commands
- **[Resource Types](Resource-Types.md)** - Valid resource types for addition commands
- **[SDK Overview](../sdk/Overview.md)** - For programmatic alternatives

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0  
**Status:** ⚠️ Partially validated - may contain obsolete information  
**Command Count:** 50+ debug/cheat commands documented