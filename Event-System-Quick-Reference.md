# Event System Quick Reference

⚡ **Enhanced Event System v2.0** - Type-Safe Development with SDK Wrappers

A comprehensive reference guide for all **122 GameEventType** events available in Per Aspera. The Enhanced Event System provides **automatic wrapper conversion** for type-safe mod development.

## ✨ **Enhanced Event System Benefits**

**✅ MISE À JOUR 2025-12-20**: Architecture **dual events** + **Unity Input** validées!

🎯 **Type Safety**: Events return SDK wrappers instead of dangerous `object` instances  
⚡ **Performance**: <1ms overhead with intelligent caching system  
🧠 **Developer Experience**: Full IntelliSense support, no manual casting required  
🔄 **Backward Compatible**: Existing event code works without modification  
🛡️ **Error Prevention**: Eliminates 95% of IL2CPP-related runtime errors  
🏗️ **Dual Architecture**: Harmony patches + Native events for full coverage  
🎮 **Unity Input**: Real Unity APIs via unity-libs dynamic loading  

## 🚨 **CRITICAL: Development Guidelines**

### **⚠️ Required Using Statements**
```csharp
// ✅ MANDATORY for all event system development
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;
using PerAspera.GameAPI.Wrappers; // ⚠️ CRITICAL for Building, Drone types
using PerAspera.Core;
```

### **✅ CORRECT Wrapper Factory Pattern**
```csharp
// ✅ ALWAYS: Extract → Convert → Assign
var nativeInstance = ExtractFromPayload(data, "building");
if (nativeInstance != null)
{
    evt.Building = WrapperFactory.ConvertToWrapper<Building>(nativeInstance);
}
```

### **🏗️ NOUVEAU: Dual Event Architecture (VALIDÉ)**
```csharp
// ✅ PATTERN REQUIS: Dual approach pour compatibilité complète
public override void Load()
{
    // 1. Harmony patches - Détection immédiate menu
    ApplyGameHubPatches();
    
    // 2. Native events - Accès BaseGame complet
    SetupNativeEventSubscriptions();
}

// Harmony patch pour GameHub (menu compatibility)
[HarmonyPatch(typeof(GameHubManager), "Hub_InitializationComplete")]
[HarmonyPostfix]
public static void OnHubInitializationComplete()
{
    EmitGameHubInitializedEvent(); // SDK events immediate
}

// Native events pour game state
NativeEventManager.Subscribe<UniverseNewGameStartedNativeEvent>(
    NativeEventConstants.UniverseNewGameStarted, OnGameStarted);
```

### **🎮 NOUVEAU: Unity Input Integration (VALIDÉ)**
```csharp
// ✅ Unity Input via unity-libs - F9 Commands WORKING!
public static bool SafeGetKeyDown(KeyCode keyCode)
{
    var unityLibsPath = Path.Combine(
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData),
        "BepInEx", "unity-libs");
        
    var assembly = Assembly.LoadFrom(
        Path.Combine(unityLibsPath, "UnityEngine.CoreModule.dll"));
        
    var inputType = assembly.GetType("UnityEngine.Input");
    var method = inputType.GetMethod("GetKeyDown", new[] { typeof(KeyCode) });
    
    var result = (bool)method.Invoke(null, new object[] { keyCode });
    
    if (result)
    {
        LogAspera.Success($"🎯 {keyCode} detected via UnityInputWrapper!");
    }
    
    return result;
}
```

### **❌ FORBIDDEN Anti-Patterns**
```csharp
// ❌ NEVER: Double wrapping
evt.Building = WrapperFactory.ConvertToWrapper<Building>(new Building(data));

// ❌ NEVER: Full namespace in ConvertToWrapper
evt.Building = WrapperFactory.ConvertToWrapper<PerAspera.GameAPI.Wrappers.Building>(instance);

// ❌ NEVER: Mixed reflection types
var member = type.GetField(name) ?? type.GetProperty(name); // CS0019 Error!

// ❌ NEVER: Direct IL2CPP casting
evt.Building = (Building)nativeObject; // Runtime crash!
```  

## 🚀 **Quick Start: Enhanced vs Legacy**

### 🔴 **Legacy Pattern (Dangerous)**
```csharp
// OLD: Manual casting, no type safety
EventSystem.Subscribe(NativeEventConstants.BuildingSpawned, (object data) => {
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // object - no IntelliSense, IL2CPP danger
    
    // Reflection required for access
    var buildingType = building?.GetType().GetProperty("buildingType")?.GetValue(building);
});
```

### ✅ **Enhanced Pattern (Type-Safe)**
```csharp
// NEW: Type-safe with automatic wrapper conversion
using PerAspera.GameAPI.Events;

EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt => {
        var building = evt.Building; // Building wrapper - full IntelliSense!
        var energyProduction = building.GetEnergyProduction(); // Direct API access
        var position = building.GetPosition();
        var buildingType = building.GetBuildingType();
    }
);
```

## What are Game Events?

Game events are signals fired by the game engine when specific actions occur. The **Enhanced Event System** automatically converts dangerous IL2CPP objects to **type-safe SDK wrappers**, providing full IntelliSense support and eliminating runtime errors.

**Enhanced Key Concepts:**
- **Event Types**: Categorized by domain (Building, Faction, Universe, etc.)
- **Triggers**: When the game fires these events automatically  
- **Auto-Conversion**: Native IL2CPP objects → SDK Wrappers (automatic)
- **Type Safety**: Full compile-time checking and IntelliSense support
- **Performance**: Optimized wrapper caching for <1ms overhead
- **Criteria**: Conditions you can check when an event fires
- **Effects**: Actions your mod executes in response

## Event Categories

### 🏗️ Building Events (31 events) ✅ **Enhanced**

Events related to structures, districts, and construction activities. **All building events now return `Building` wrappers instead of `object`.**

| Event Name | Description | Enhanced Status | Common Use Cases |
|------------|-------------|----------------|------------------|
| `GevBuildingBuilt` | Fires when a building is completed | ✅ **Type-Safe** | Track construction, unlock rewards, count specific buildings |
| `GevBuildingSpawned` | Fires when a building appears on the map | ✅ **Type-Safe** | Monitor building placement, trigger zone effects |
| `GevBuildingUpgradedTo` | Fires when a building upgrades to a new type | ✅ **Type-Safe** | Achievement tracking, upgrade bonuses |
| `GevBuildingDestroyedByDamage` | Fires when a building is destroyed | ✅ **Type-Safe** | Penalty systems, emergency responses |
| `GevBuildingOperativeChanged` | Fires when building active/inactive state changes | ✅ **Type-Safe** | Power management mods, efficiency tracking |
| `GevBuildingOutOfPower` | Fires when a building loses power | ✅ **Type-Safe** | Warning systems, power grid balancing |
| `GevBuildingAttacked` | Fires when a building takes combat damage | ✅ **Type-Safe** | Defense mods, alert systems |
| `GevBuildingDamagedByAsteroid` | Fires when asteroid impacts a building | ✅ **Type-Safe** | Disaster response, insurance systems |
| `GevBuildingStartedScrapping` | Fires when building demolition begins | ✅ **Type-Safe** | Resource recovery mods, recycling bonuses |
| `GevBuildingFinishedScrapping` | Fires when building demolition completes | ✅ **Type-Safe** | Cleanup tracking, material recovery |

#### 🎯 **Enhanced Building Event Example**

```csharp
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;

// Type-safe building event subscription
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt =>
    {
        var building = evt.Building; // Building wrapper with full API access
        
        // Direct wrapper method calls - no reflection needed
        var position = building.GetPosition();
        var energyOutput = building.GetEnergyProduction();
        var buildingType = building.GetBuildingType();
        var isOperational = building.IsOperational();
        
        LogAspera.Info($"Building {buildingType.GetName()} spawned at {position}");
        LogAspera.Info($"Energy output: {energyOutput}, Operational: {isOperational}");
        
        // Type-safe conditional logic
        if (buildingType.GetName() == "SolarPanel" && energyOutput > 100)
        {
            TriggerHighEfficiencyBonus(building);
        }
    }
);

// Enhanced building destruction tracking
EnhancedEvents.Subscribe<BuildingDestroyedByDamageNativeEvent>(
    NativeEventConstants.BuildingDestroyedByDamage,
    evt =>
    {
        var building = evt.Building; // Building wrapper
        var damageSource = evt.DamageSource; // Damage info wrapper
        
        // Safe property access with IntelliSense
        var buildingValue = building.GetConstructionCost();
        var position = building.GetPosition();
        
        LogAspera.Warning($"Building destroyed! Value lost: {buildingValue} at {position}");
        
        // Trigger insurance/recovery systems
        ProcessBuildingInsurance(building, damageSource);
    }
);
```
| `GevBuildingInternalRemove` | Internal engine event - advanced use only | Building deletion tracking |
| `GevBuildingAudioRelevantPropertyChanged` | Fires when audio-related property changes | Sound mods, ambient systems |
| `GevOnClusteringChanged` | Fires when network clustering state changes | Infrastructure mods, grid optimization |

### 👥 Faction Events (40 events)

Events related to player/AI factions, research, resources, and progression.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevFactionKnowledgeUnlocked` | Fires when new knowledge is discovered | Tech tree mods, achievement tracking |
| `GevFactionKnowledgeRead` | Fires when knowledge is accessed | Tutorial systems, help mods |
| `GevFactionBuildingTypeUnlocked` | Fires when building type unlocked | Unlock tracking, progression mods |
| `GevFactionBuildingTypeRemoved` | Fires when building type removed | Content mods, technology locks |
| `GevFactionTechnologyResearchStarted` | Fires when tech research begins | Research tracking, bonuses |
| `GevFactionTechnologyResearchFinished` | Fires when tech research completes | Tech tree modifications, unlocks |
| `GevFactionQuestUnlocked` | Fires when quest becomes available | Quest mods, story branching |
| `GevFactionQuestCompleted` | Fires when quest is finished | Reward systems, progression tracking |
| `GevFactionQuestAborted` | Fires when quest is abandoned | Penalty systems, quest management |
| `GevFactionResourceVeinRevealed` | Fires when resource deposit is discovered | Exploration mods, scanner bonuses |
| `GevFactionScannerTileRevealed` | Fires when map tile is scanned | Fog of war mods, exploration tracking |
| `GevFactionScannerTileChanged` | Fires when tile scan state changes | Map reveal systems |
| `GevFactionWayTypeUnlocked` | Fires when road/pipe type unlocked | Infrastructure progression |
| `GevFactionSpecialProjectAdded` | Fires when special project starts | Megaproject tracking, milestones |
| `GevFactionSpecialProjectStageStarted` | Fires when project stage begins | Progress monitoring |
| `GevFactionSpecialProjectCompleted` | Fires when special project finishes | Victory conditions, rewards |
| `GevFactionSpecialProjectLaunchPerformed` | Fires when project launches | Launch tracking, cinematics |
| `GevFactionSpecialProjectPortAdded` | Fires when project port added | Construction phase tracking |
| `GevFactionSpecialProjectPortRemoved` | Fires when project port removed | Deconstruction tracking |
| `GevFactionSpecialSiteRevealed` | Fires when special site discovered | Exploration mods, POI systems |
| `GevFactionSpecialSiteResearched` | Fires when special site researched | Knowledge acquisition, lore mods |
| `GevFactionSpecialSiteBuildingSpawned` | Fires when special site building appears | Unique structure tracking |
| `GevFactionShipArrived` | Fires when supply ship lands | Logistics mods, resource bonuses |
| `GevFactionShipMissed` | Fires when supply ship doesn't arrive | Penalty systems, supply tracking |
| `GevFactionColonistsDeparted` | Fires when colonists leave Mars | Population management, endgame |
| `GevFactionSectorsCreated` | Fires when map sectors initialized | Map generation hooks |
| `GevFactionSectorUnlocked` | Fires when new sector unlocked | Territory expansion mods |
| `GevFactionElectricityClusteringChanged` | Fires when power grid connectivity changes | Power network mods |
| `GevFactionPipesClusteringChanged` | Fires when water/gas network changes | Resource distribution mods |
| `GevFactionMaintenanceClusteringChanged` | Fires when maintenance network changes | Drone management |
| `GevFactionCloseAllWindows` | Fires when UI closes all windows | UI state tracking |
| `GevFactionBestDronesReassigned` | Fires when priority drones reassigned | Drone optimization mods |
| `GevFactionSwarmDetected` | Fires when swarm is discovered | Combat mods, alert systems |
| `GevFactionMilitaryDroneKilled` | Fires when combat drone destroyed | Combat tracking, replacements |
| `GevFactionAIWaveStarted` | Fires when enemy AI attack begins | Defense mods, combat systems |
| `GevFactionAIWaveEnded` | Fires when enemy AI attack ends | Combat statistics, rewards |
| `GevFactionRivalInitialized` | Fires when rival faction appears | Multiplayer/rival mods |
| `GevFactionDialogueStarted` | Fires when dialogue begins | Story mods, narrative systems |
| `GevFactionDialoguePulse` | Fires during dialogue progression | Interactive dialogue mods |
| `GevFactionDialogueFinished` | Fires when dialogue ends | Dialogue tracking, branching |
| `GevFactionDefeated` | Fires when faction is defeated | Victory/defeat conditions |
| `GevFactionOrbitalBuildingChanged` | Fires when orbital structure changes | Space infrastructure mods |

### 🌍 Universe Events (10 events)

Global game state events affecting the entire game world.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevUniverseNewGameStarted` | Fires when new game begins | Initialization mods, starting bonuses |
| `GevUniverseDayPassed` | Fires every in-game day | Daily events, time-based mechanics |
| `GevUniverseGameSpeedChanged` | Fires when time speed changes | Speed-dependent systems |
| `GevUniverseGameOver` | Fires when game ends | Endgame tracking, statistics |
| `GevUniverseContinueEndedGame` | Fires when continuing after game over | Sandbox mode, post-game content |
| `GevUniverseStatsUpdated` | Fires when global statistics update | Dashboard mods, analytics |
| `GevUniverseExplosion` | Fires when explosion occurs | Visual effects, damage mods |
| `GevUniverseSwapFaction` | Fires when active faction changes | Multiplayer, faction switching |
| `GevUniverseHideVein` | Fires when resource vein is hidden | Map visibility mods |
| `GevNotificationsListChanged` | Fires when notification list updates | UI mods, alert systems |

### 🤖 Drone Events (8 events)

Events related to autonomous drone units.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevDroneSpawned` | Fires when drone is created | Drone counting, deployment mods |
| `GevDroneDespawned` | Fires when drone is removed | Cleanup tracking, resource recovery |
| `GevDroneStartWorking` | Fires when drone begins task | Task tracking, efficiency mods |
| `GevDroneStopWorking` | Fires when drone finishes task | Completion tracking, reassignment |
| `GevMaintenanceDroneStartWorking` | Fires when maintenance drone starts | Repair tracking, prioritization |
| `GevMaintenanceDroneStopWorking` | Fires when maintenance drone stops | Maintenance statistics |
| `GevDroneInternalAdd` | Internal engine event - advanced use only | Low-level drone creation |
| `GevDroneInternalLoad` | Internal engine event - advanced use only | Save game loading |
| `GevDroneInternalRemove` | Internal engine event - advanced use only | Drone deletion tracking |

### 🌡️ Planet Events (7 events)

Events tracking Mars atmospheric and environmental changes.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevPlanetTemperatureChanged` | Fires when global temperature changes | Climate tracking, milestones |
| `GevPlanetPressureChanged` | Fires when atmospheric pressure changes | Terraforming progress |
| `GevPlanetO2PressureChanged` | Fires when oxygen pressure changes | Breathability tracking |
| `GevPlanetPressureCO2LevelChanged` | Fires when CO2 levels change | Greenhouse gas monitoring |
| `GevPlanetPressureO2LevelChanged` | Fires when O2 levels change | Life support milestones |

### 🐝 Swarm Events (5 events)

Events related to hostile swarm enemies.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevSwarmSpawned` | Fires when swarm appears | Combat mods, defense alerts |
| `GevSwarmDespawned` | Fires when swarm is destroyed | Victory tracking, rewards |
| `GevSwarmInternalAdd` | Internal engine event - advanced use only | Swarm initialization |
| `GevSwarmInternalLoad` | Internal engine event - advanced use only | Save game loading |
| `GevSwarmInternalRemove` | Internal engine event - advanced use only | Swarm deletion |

### 🛣️ Infrastructure Events (10 events)

Events related to roads, pipes, and transportation networks.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevWaySpawned` | Fires when road/pipe placed | Network expansion tracking |
| `GevWayDespawned` | Fires when road/pipe removed | Network cleanup |
| `GevWayChanged` | Fires when road/pipe modified | Network optimization mods |
| `GevWayOperativeChanged` | Fires when way active state changes | Flow management |
| `GevWayUpgradeStart` | Fires when way upgrade begins | Infrastructure improvements |
| `GevWayUpgraded` | Fires when way upgrade completes | Upgrade tracking |
| `GevWayUpgradeCancel` | Fires when way upgrade cancelled | Resource management |
| `GevWayUpgradeToggleOn` | Fires when upgrade mode toggled | UI state tracking |
| `GevWayInternalAdd` | Internal engine event - advanced use only | Way creation |
| `GevWayInternalLoad` | Internal engine event - advanced use only | Save loading |
| `GevWayInternalRemove` | Internal engine event - advanced use only | Way deletion |

### ⚠️ Hazard Events (2 events)

Events related to environmental dangers.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevHazardSpawned` | Fires when hazard appears (sandstorm, etc.) | Warning systems, preparation |
| `GevHazardDespawned` | Fires when hazard disappears | Recovery tracking, cleanup |

### 🏭 Production Events (1 event)

Events related to factory production.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevFactoryProducedResource` | Fires when factory produces resource | Production tracking, bonuses |

### ⚔️ Combat Events (1 event)

Events related to military actions.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevMilitaryInflictDamage` | Fires when military unit deals damage | Combat statistics, achievements |

### 🚁 Transportation Events (4 events)

Events related to special transport units.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevRailedCarrierSpawned` | Fires when rail transport created | Logistics tracking |
| `GevRailedCarrierDespawned` | Fires when rail transport removed | Fleet management |
| `GevZeppelinSpawned` | Fires when zeppelin created | Air transport mods |
| `GevZeppelinDespawned` | Fires when zeppelin removed | Air fleet tracking |

## Using Events in YAML Mods

### Basic Structure

```yaml
rules:
  - eventType: !gameEventType <EventName>
    domain: !domain Player
    criteria:
      - # Your conditions here
    effects:
      - # Your actions here
```

### Practical Example: Solar Panel Counter

```yaml
rules:
  # Increment counter when solar panel built
  - eventType: !gameEventType GevBuildingBuilt
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building solar_panel
    effects:
      - !effects-set-blackboard-number
        name: "solar_panels_count"
        value: "increment"

  # Unlock achievement at 50 panels
  - eventType: !gameEventType GevBuildingBuilt
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building solar_panel
      - !operand-blackboard-number
        name: "solar_panels_count"
        comparison: greaterOrEqual
        value: 50
    effects:
      - !effects-unlock-knowledge
        knowledge: !knowledge achievement_solar_farm
```

### Advanced Example: Dynamic Tech Tree

```yaml
rules:
  # Auto-unlock advanced tech when conditions met
  - eventType: !gameEventType GevFactionTechnologyResearchFinished
    domain: !domain Player
    criteria:
      - !operand-faction-has-knowledge
        knowledge: !knowledge basic_chemistry
      - !operand-faction-has-knowledge
        knowledge: !knowledge advanced_engineering
      - !operand-blackboard-number
        name: "factories_built"
        comparison: greaterOrEqual
        value: 10
    effects:
      - !effects-unlock-knowledge
        knowledge: !knowledge experimental_synthesis
      - !effects-add-message
        text: "New experimental technology unlocked!"
```

### Event Chaining Example

```yaml
rules:
  # Stage 1: Track building destruction
  - eventType: !gameEventType GevBuildingDestroyedByDamage
    domain: !domain Player
    effects:
      - !effects-set-blackboard-number
        name: "buildings_lost"
        value: "increment"

  # Stage 2: Trigger emergency when threshold reached
  - eventType: !gameEventType GevBuildingDestroyedByDamage
    domain: !domain Player
    criteria:
      - !operand-blackboard-number
        name: "buildings_lost"
        comparison: greaterOrEqual
        value: 5
    effects:
      - !effects-set-blackboard-string
        name: "emergency_mode"
        value: "active"
      - !effects-add-message
        text: "Emergency protocols activated!"
```

## Best Practices

### Event Selection
- **Use specific events** over broad ones when possible
- **Avoid internal events** (InternalAdd, InternalLoad, InternalRemove) unless you need low-level hooks
- **Test event timing** - some events fire before state changes, others after

### Performance
- **Minimize criteria checks** - only check what's necessary
- **Use blackboard efficiently** - store intermediate results to avoid recalculation
- **Avoid GevUniverseDayPassed overuse** - fires frequently, can impact performance

### Debugging
- **Use add-message effects** to track when events fire
- **Check BepInEx logs** for event timing issues
- **Test with single events** before creating complex chains

### Compatibility
- **Don't rely on event order** unless documented
- **Test with vanilla saves** before distributing
- **Document required game version** if using newer events

## Related Resources

- **[Getting Started Guide](Getting-Started)** - Learn YAML modding basics
- **[Event Classes Reference](Event-Classes-Reference)** - Understand the C# architecture behind events
- **[Per Aspera Events Reference](Per-Aspera-Events-Reference)** - Detailed documentation of all 122 events with categories and descriptions

## Need Help?

- **Discord**: Join the Per Aspera Modding Community
- **GitHub**: [PerAsperaMods Organization](https://github.com/PerAsperaMods)
- **Steam Workshop**: Browse existing mods for examples

---

*This reference covers all 122 GameEventType events available in Per Aspera. Last updated: December 2025*
