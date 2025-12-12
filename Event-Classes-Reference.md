# Per Aspera - Event Classes Technical Reference

This technical documentation details the C# classes behind each Per Aspera GameEventType event.

## 📋 Table of Contents

- [Event Class Architecture](#event-class-architecture)
- [Class Types](#class-types)
- [Event → Class Mapping](#event--class-mapping)
- [IL2CPP Structure](#il2cpp-structure)
- [Usage for Modding](#usage-for-modding)

---

## Event Class Architecture

### Basic Structure
All Per Aspera events follow this IL2CPP pattern:

```csharp
// Example: GevBuildingBuilt
using System;
using PerAspera.Events;

public static GameEventType GevBuildingBuilt
```

### Implementation Categories

#### 1. **Static Events** (Majority)
```csharp
public static GameEventType GevEventName
```
- Standard events used by the game system
- Accessible via YAML with `!gameEventType GevEventName`

#### 2. **Override Events** (Specialized)
```csharp
public override GameEventType GevOnClusteringChanged
```
- Inherited or specialized events
- Generally linked to specific systems (Pipes, Electricity)

---

## Class Types

### 🏗️ **Building Events Classes**
Located in decompiled building files.

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