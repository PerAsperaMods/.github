# Event Classes Reference

🔧 **Technical reference for Per Aspera event classes organized by source system.**

## 🎯 Table of Contents

- [Building Events](#building-events) - Construction and infrastructure events
- [Faction Events](#faction-events) - Faction management and interactions  
- [Planet Events](#planet-events) - Planetary systems and climate events
- [Universe Events](#universe-events) - Universal game state events
- [BaseGame Events](#basegame-events) - Core game system events

---

## Building Events

### 🏗️ Construction Events

#### `GevBuildingBuilt`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when building construction completes.

```csharp
public static GameEventType GevBuildingBuilt
```

#### `GevBuildingDestroyed`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when a building is destroyed.

```csharp
public static GameEventType GevBuildingDestroyed
```

#### `GevBuildingStarted`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when building construction begins.

```csharp
public static GameEventType GevBuildingStarted
```

### ⚡ Power System Events

#### `GevPowerGridChanged`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when power grid status changes.

```csharp
public static GameEventType GevPowerGridChanged
```

#### `GevPowerOutage`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when power outages occur.

```csharp
public static GameEventType GevPowerOutage
```

### 🏭 Production Events

#### `GevProductionCycleCompleted`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when production cycles complete.

```csharp
public static GameEventType GevProductionCycleCompleted
```

#### `GevBuildingEfficiencyChanged`
**Source Class**: `Building`
**Type**: `static GameEventType`

Triggered when building efficiency changes.

```csharp
public static GameEventType GevBuildingEfficiencyChanged
```

---

## Faction Events

### 👥 Faction Management

#### `GevFactionDetected`
**Source Class**: `Faction`
**Type**: `static GameEventType`

Triggered when new factions are detected.

```csharp
public static GameEventType GevFactionDetected
```

#### `GevFactionRelationshipChanged`
**Source Class**: `Faction`
**Type**: `static GameEventType`

Triggered when faction relationships change.

```csharp
public static GameEventType GevFactionRelationshipChanged
```

#### `GevFactionConflictStarted`
**Source Class**: `Faction`
**Type**: `static GameEventType`

Triggered when faction conflicts begin.

```csharp
public static GameEventType GevFactionConflictStarted
```

#### `GevFactionConflictEnded`
**Source Class**: `Faction`
**Type**: `static GameEventType`

Triggered when faction conflicts end.

```csharp
public static GameEventType GevFactionConflictEnded
```

---

## Planet Events

### 🌡️ Climate Events

#### `GevPlanetTemperatureChanged`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when planetary temperature changes.

```csharp
public static GameEventType GevPlanetTemperatureChanged
```

#### `GevAtmosphericPressureChanged`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when atmospheric pressure changes.

```csharp
public static GameEventType GevAtmosphericPressureChanged
```

#### `GevAtmosphereCompositionChanged`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when atmospheric composition changes.

```csharp
public static GameEventType GevAtmosphereCompositionChanged
```

### 💧 Water System Events

#### `GevWaterStockChanged`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when water reserves change.

```csharp
public static GameEventType GevWaterStockChanged
```

#### `GevWaterSourceDiscovered`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when new water sources are found.

```csharp
public static GameEventType GevWaterSourceDiscovered
```

### 🌌 Astronomical Events

#### `GevAsteroidImpactIncoming`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when asteroids are approaching.

```csharp
public static GameEventType GevAsteroidImpactIncoming
```

#### `GevAsteroidImpactOccurred`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when asteroid impacts occur.

```csharp
public static GameEventType GevAsteroidImpactOccurred
```

### 🌪️ Weather Events

#### `GevDustStormStarted`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when dust storms begin.

```csharp
public static GameEventType GevDustStormStarted
```

#### `GevDustStormEnded`
**Source Class**: `Planet`
**Type**: `static GameEventType`

Triggered when dust storms end.

```csharp
public static GameEventType GevDustStormEnded
```

---

## Universe Events

### 🎮 Game State Events

#### `GevUniverseInitialized`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when universe systems initialize.

```csharp
public static GameEventType GevUniverseInitialized
```

#### `GevSolChanged`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when Martian sol changes.

```csharp
public static GameEventType GevSolChanged
```

#### `GevGameSpeedChanged`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when game speed changes.

```csharp
public static GameEventType GevGameSpeedChanged
```

### 🔬 Research Events

#### `GevTechnologyUnlocked`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when technologies are unlocked.

```csharp
public static GameEventType GevTechnologyUnlocked
```

#### `GevResearchCompleted`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when research projects complete.

```csharp
public static GameEventType GevResearchCompleted
```

### 📦 Resource Events

#### `GevResourceProductionChanged`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when resource production changes.

```csharp
public static GameEventType GevResourceProductionChanged
```

#### `GevResourceShortageDetected`
**Source Class**: `Universe`
**Type**: `static GameEventType`

Triggered when resource shortages occur.

```csharp
public static GameEventType GevResourceShortageDetected
```

---

## BaseGame Events

### 🎮 Core System Events

#### `GevGameFullyLoaded`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when game finishes loading.

```csharp
public static GameEventType GevGameFullyLoaded
```

#### `GevGamePaused`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when game is paused.

```csharp
public static GameEventType GevGamePaused
```

#### `GevGameResumed`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when game is resumed.

```csharp
public static GameEventType GevGameResumed
```

### 💾 Save/Load Events

#### `GevGameSaved`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when game is saved.

```csharp
public static GameEventType GevGameSaved
```

#### `GevGameLoaded`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when save is loaded.

```csharp
public static GameEventType GevGameLoaded
```

### ⚙️ System Events

#### `GevSystemInitialized`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when core systems initialize.

```csharp
public static GameEventType GevSystemInitialized
```

#### `GevErrorOccurred`
**Source Class**: `BaseGame`
**Type**: `static GameEventType`

Triggered when system errors occur.

```csharp
public static GameEventType GevErrorOccurred
```

---

## 🔧 Usage in Modding

### Event Subscription Pattern
```csharp
// Subscribe to building events
ModSDK.Events.Subscribe("GevBuildingBuilt", OnBuildingBuilt);

// Subscribe to climate events  
ModSDK.Events.Subscribe("GevPlanetTemperatureChanged", OnTemperatureChanged);

// Subscribe to universe events
ModSDK.Events.Subscribe("GevSolChanged", OnSolChanged);
```

### YAML Event Reference
```yaml
# Reference events in YAML files
eventType: !gameEventType GevBuildingBuilt
triggerEvent: !gameEventType GevPlanetTemperatureChanged
```

### Event Handler Implementation
```csharp
private void OnBuildingBuilt(object eventData)
{
    // Handle building construction completion
    Log.LogInfo("Building construction completed");
}

private void OnTemperatureChanged(object eventData)
{
    // Handle temperature changes
    Log.LogInfo("Planetary temperature changed");
}
```

---

## 🚀 Next Steps

### 📚 Related Documentation

1. **[Per Aspera Events Reference](Per-Aspera-Events-Reference)** - Complete event usage guide
2. **[SDK API Reference](SDK-API-Reference)** - Full SDK documentation
3. **[Quick Reference](Quick-Reference)** - Essential patterns and examples

### 💡 Community Resources

- **[Discord Community](https://discord.gg/peraspera-modding)** - Real-time development help
- **[GitHub Discussions](https://github.com/PerAsperaMods/PerAspera-SDK/discussions)** - Technical Q&A
- **[Code Examples](Code-Examples)** - Working implementation examples

---

**Master Per Aspera event classes to build sophisticated mods that interact deeply with the game's systems! 🔴➡️🌍**

| Source Class | Event | Type |
|--------------|-------|---------|
| `GevBuilding.cs` | `GevBuildingAfterChangeBuildingType` | static |
| `GevBuilding.cs` | `GevBuildingAttacked` | static |
| `GevBuilding.cs` | `GevBuildingBuilt` | static |
| `GevBuilding.cs` | `GevBuildingUpgradedTo` | static |
| `GevBuilding.cs` | `GevBuildingFinishedScrapping` | static |

### 👥 **Faction Events Classes**
Faction management and progression.

| Source Class | Event | Type |
|--------------|-------|---------|
| `Faction.cs` | `GevFactionAIWaveEnded` | static |
| `Faction.cs` | `GevFactionTechnologyResearchFinished` | static |
| `Faction.cs` | `GevFactionKnowledgeUnlocked` | static |
| `Faction.cs` | `GevFactionSpecialProjectCompleted` | static |

### 🌍 **Universe Events Classes**
Global game system events.

| Source Class | Event | Type |
|--------------|-------|---------|
| `Universe.cs` | `GevUniverseNewGameStarted` | static |
| `Universe.cs` | `GevUniverseDayPassed` | static |
| `Universe.cs` | `GevUniverseGameOver` | static |
| `GevUniverseSwapFaction.cs` | `GevUniverseSwapFaction` | static |

### 🤖 **Drone Events Classes**
Work and maintenance drone management.

| Source Class | Event | Type |
|--------------|-------|---------|
| `drone.cs` | `GevDroneDespawned` | static |
| `drone.cs` | `GevDroneSpawned` | static |
| `drone.cs` | `GevDroneStartWorking` | static |
| `GevMaintenanceDroneStopWorking.cs` | `GevMaintenanceDroneStopWorking` | static |

### 🛣️ **Infrastructure Classes**
Roads, pipes, electricity.
  
| Source Class | Event | Type |
|--------------|-------|---------|
| `GevWayChanged.cs` | `GevWayChanged` | static |
| `aGevWayChanged.cs` | `GevWayChanged` | static |
| `Electricicy.cs` | `GevOnClusteringChanged` | **override** |
| `PipeGevOnClusteringChanged.cs` | `GevOnClusteringChanged` | **override** |

### 🌡️ **Environment Classes**
Planet, climate, terraforming.

| Source Class | Event | Type |
|--------------|-------|---------|
| `GevPlanetTemperatureChanged.cs` | `GevPlanetTemperatureChanged` | static |
| `Planet.cs` | `GevPlanetPressureChanged` | static |
| `Planet.cs` | `GevPlanetO2PressureChanged` | static |

### 🚂 **Transport Classes**
Specialized transport systems.

| Source Class | Event | Type |
|--------------|-------|---------|
| `GevRailedCarrierDespawned.cs` | `GevRailedCarrierDespawned` | static |
| `Transport.cs` | `GevRailedCarrierSpawned` | static |
| `GevZeppelinSpawned.cs` | `GevZeppelinSpawned` | static |

### ⚔️ **Combat Classes**
Swarms, combat, damage.

| Source Class | Event | Type |
|--------------|-------|---------|
| `GevSwarmDespawned.cs` | `GevSwarmDespawned` | static |
| `Combat.cs` | `GevSwarmSpawned` | static |
| `GevMilitaryInflictDamage.cs` | `GevMilitaryInflictDamage` | static |

---

## IL2CPP Structure

### Technical Characteristics

#### Standard Imports
```csharp
using System;
using PerAspera.Events;          // Core event system
using Il2CppInterop.Common.Attributes;  // For some overrides
```

#### NotImplementedException Pattern
```csharp
get
{
    throw new NotImplementedException();
}
```
- **All** events use this pattern
- Real implementations are in native IL2CPP code
- Decompiled classes are **stubs** for interoperability

#### Advanced IL2CPP Attributes
```csharp
[CallerCount(0)]
[CachedScanResults(RefRangeStart = 0, RefRangeEnd = 0, 
                   XrefRangeStart = 664787, XrefRangeEnd = 664791, 
                   MetadataInitTokenRva = 0L, MetadataInitFlagRva = 0L)]
```
- IL2CPP decompilation metadata
- Indicate references and optimizations

---

## Usage for Modding

### 1. **YAML Referencing**
```yaml
# In rule-patch.yaml
rules:
  - eventType: !gameEventType GevBuildingBuilt  # ← Exact class name
    domain: !domain Player
    effects:
      - !effects-set-blackboard-number
        name: "buildings_count"
        value: "increment"
```

### 2. **Event Validation**
Verify the event exists by checking:
- Source file in `gev/`
- Type `static` vs `override`
- Appropriate category

### 3. **BepInEx C# Hooking**
```csharp
// To hook an event in C#
[HarmonyPatch(typeof(GameEventBus), "TriggerEvent")]
public static void Prefix(GameEventType eventType, ref GameEventData data)
{
    if (eventType == GevBuildingBuilt)
    {
        // Your logic here
    }
}
```

### 4. **Event Debugging**
```csharp
// Log all events
public static void LogAllEvents(GameEventType eventType, GameEventData data)
{
    Debug.Log($"Event: {eventType.GetType().Name} | Data: {data}");
}
```

---

## 🔍 Mapping Complet Événement → Classe

### Pattern de Nommage
- **Fichier** : `GevEventName.cs` ou catégorie générale (`Building.cs`, `Faction.cs`)
- **Classe** : `public static GameEventType GevEventName`
- **Usage YAML** : `!gameEventType GevEventName`

### Événements avec Classes Dédiées
Ces événements ont leur propre fichier C# :

| Événement | Fichier Source |
|-----------|----------------|
| `GevMaintenanceDroneStopWorking` | `GevMaintenanceDroneStopWorking.cs` |
| `GevMilitaryInflictDamage` | `GevMilitaryInflictDamage.cs` |
| `GevPlanetTemperatureChanged` | `GevPlanetTemperatureChanged.cs` |
| `GevRailedCarrierDespawned` | `GevRailedCarrierDespawned.cs` |
| `GevSwarmDespawned` | `GevSwarmDespawned.cs` |
| `GevUniverseSwapFaction` | `GevUniverseSwapFaction.cs` |
| `GevWayChanged` | `GevWayChanged.cs` + `aGevWayChanged.cs` |
| `GevZeppelinSpawned` | `GevZeppelinSpawned.cs` |

### Événements Multiples (Override)
Ces événements ont des implémentations spécialisées :

| Événement | Classes Sources |
|-----------|-----------------|
| `GevOnClusteringChanged` | `Electricicy.cs`, `PipeGevOnClusteringChanged.cs`, `GevOnClusteringChanged.cs` |

---

## 💡 Tips for Modders

### ✅ **Best Practices**
1. **Use exact names** of classes for `!gameEventType`
2. **Prefer static events** over overrides
3. **Test first** with simple events like `GevUniverseDayPassed`
4. **Check BepInEx logs** to validate triggers

### ❌ **Avoid**
1. `Internal*` events (internal system)
2. Complex overrides without understanding context
3. Events without clear documentation

### 🔧 **Debug & Validation**
```yaml
# Basic test in rule-patch.yaml
rules:
  - eventType: !gameEventType GevUniverseDayPassed
    domain: !domain Player
    effects:
      - !effects-debug-log
        message: "Event GevUniverseDayPassed triggered successfully!"
```

This technical reference allows you to understand the underlying architecture of Per Aspera events and optimize your YAML and C# mods.

---

## 📚 Additional Resources

- [Events Reference](Per-Aspera-Events-Reference.md) - User guide for events
- [YAML Modding Guide](YAML-Modding-Guide.md) - YAML syntax and examples
- [BepInEx Guide](BepInEx-Modding-Guide.md) - Advanced C# modding
- Source files: `f:\ModPeraspera\Documentation\Hardpoint\class\Event\gev\`