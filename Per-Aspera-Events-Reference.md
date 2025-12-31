# Per Aspera Events Reference

🎮 **Comprehensive guide to Per Aspera game events for advanced mod development.**

## ⚡ **ENHANCED EVENT SYSTEM** (v2.0)

**🎯 MISE À JOUR 2025-12-20**: Architecture **dual events** + **Unity Input** validées avec succès!

**🎯 New Features:**
- ✅ **Type-Safe Wrappers**: All events now return SDK wrappers instead of dangerous IL2CPP objects
- ✅ **Auto-Conversion**: Automatic native-to-wrapper conversion with `EnhancedEventBus`
- ✅ **Performance Optimized**: <1ms overhead with intelligent caching
- ✅ **Backward Compatible**: Existing event code continues to work
- ✅ **Dual Architecture**: Harmony patches + Native events for complete coverage
- ✅ **Unity Input Integration**: Real Unity APIs via unity-libs dynamic loading

**🏗️ Architecture Dual - DÉCOUVERTE CRITIQUE**:

```csharp
// ✅ PATTERN REQUIS: Dual approach pour compatibilité complète
public override void Load()
{
    // 1. Harmony patches - Détection immédiate menu (GameHub)
    ApplyGameHubPatches();
    
    // 2. Native events - Accès BaseGame complet (Game State)
    SetupNativeEventSubscriptions();
    
    // 3. Unity Input - F9 Commands via unity-libs
    InitializeUnityInput();
}

// GameHub detection via Harmony (menu compatibility)
[HarmonyPatch(typeof(GameHubManager), "Hub_InitializationComplete")]
[HarmonyPostfix]
public static void OnHubInitializationComplete()
{
    EmitGameHubInitializedEvent(); // Immediate SDK events
}

// Native events pour game state complet
NativeEventManager.Subscribe<UniverseNewGameStartedNativeEvent>(
    NativeEventConstants.UniverseNewGameStarted, OnGameStarted);

// Unity Input via unity-libs (F9 Commands working!)
if (UnityInputWrapper.SafeGetKeyDown(KeyCode.F9))
{
    LogAspera.Success("🎯 F9 detected via UnityInputWrapper!");
}
```

**📋 Migration Status:** Events migrated to Enhanced System:
- ✅ `BaseGameDetectedEvent` - Now returns `BaseGame` wrapper (not `object`)
- ✅ `BuildingEvents` - All building events return `Building` wrappers
- ✅ `DroneEvents` - All drone events return `Drone` wrappers
- 🚧 **Next**: Climate, Resource, Universe events

## 🎯 Table of Contents

- [Overview](#overview) - Game event system fundamentals + Enhanced System
- [System Events](#system-events) - Core game state events (Enhanced)
- [Climate Events](#climate-events) - Atmospheric and terraforming events
- [Building Events](#building-events) - Infrastructure and construction events (Enhanced)
- [Resource Events](#resource-events) - Production and resource management events
- [Quest Events](#quest-events) - Narrative and mission progression events
- [SDK Integration](#sdk-integration) - Using events with the Per Aspera SDK
- [Enhanced System Usage](#enhanced-system-usage) - Type-safe event development

---

## Overview

### 🔄 Per Aspera Event System (Enhanced v2.0)

Per Aspera uses a robust event system to manage game progression, climate changes, infrastructure construction, and narrative interactions. The **Enhanced Event System v2.0** provides **type-safe SDK wrappers** instead of dangerous IL2CPP objects, with automatic conversion and performance optimization.

#### Enhanced Architecture Benefits

✅ **Type Safety**: No more `object` instances - full IntelliSense support  
✅ **Performance**: <1ms overhead per event with intelligent caching  
✅ **Developer Experience**: Automatic wrapper conversion, no manual casting  
✅ **Compatibility**: Existing mods work without modification

#### Enhanced Event Architecture (v2.0)

```csharp
// Enhanced Per Aspera Event Structure
public class PerAsperaGameEvent
{
    public GameEventType Type { get; set; }          // Event type identifier
    public GameEventPayload Payload { get; set; }    // Event data (auto-converted to wrappers)
    public DateTime Timestamp { get; set; }          // When event occurred
    public int MartianSol { get; set; }             // Current Martian sol
    public string Source { get; set; }              // Event source (building, system, etc.)
}

// Enhanced Event Subscription (Type-Safe)
using PerAspera.GameAPI.Events;

// OLD: Manual casting required
EventSystem.Subscribe("building.spawned", (object data) => {
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // object - dangerous!
});

// NEW: Type-safe with wrappers
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>("building.spawned", evt => {
    var building = evt.Building; // Building wrapper - safe!
    var energyProduction = building.GetEnergyProduction(); // IntelliSense works!
});
```

#### Enhanced Event Categories

| Category | Description | Frequency | Enhanced Status |
|----------|-------------|-----------|----------------|
| **System** | Game loading, sol changes, save/load | System-driven | ✅ **Enhanced** |
| **Building** | Construction, production, infrastructure | Action-triggered | ✅ **Enhanced** |
| **Climate** | Temperature, pressure, atmospheric changes | Continuous | 🚧 Migration planned |
| **Resource** | Production, consumption, availability | Continuous | 🚧 Migration planned |
| **Quest** | Story progression, objectives, dialogues | Scripted | 🚧 Migration planned |

---

## Enhanced System Usage

### 🎯 **Type-Safe Event Development**

**✅ MISE À JOUR 2025-12-20**: Patterns dual architecture + Unity Input validés!

The Enhanced Event System provides completely type-safe access to Per Aspera game objects with **dual architecture** for complete compatibility:

#### **🏗️ Dual Architecture Pattern (REQUIS)**
```csharp
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;
using PerAspera.GameAPI.Events.Integration;

public class YourMod : BasePlugin
{
    public override void Load()
    {
        // 1. Harmony patches - Menu compatibility (GameHub)
        ApplyGameHubPatches();
        
        // 2. Native events - Game state access (BaseGame)
        SetupNativeEventSubscriptions();
    }
    
    private void ApplyGameHubPatches()
    {
        // Harmony patch pour détection immédiate GameHub
        var harmony = new Harmony("com.yourmod.gamehub");
        harmony.PatchAll(typeof(GameHubManagerPatch));
    }
    
    private void SetupNativeEventSubscriptions()
    {
        // Native events pour accès BaseGame complet
        NativeEventManager.Subscribe<UniverseNewGameStartedNativeEvent>(
            NativeEventConstants.UniverseNewGameStarted, OnGameStarted);
    }
}
```

#### **🎮 Unity Input Integration (NOUVEAU)**
```csharp
// Unity Input via unity-libs - F9 Commands WORKING!
public static class UnityInputWrapper
{
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
}

// Usage in Commands Demo
private void Update()
{
    if (UnityInputWrapper.SafeGetKeyDown(KeyCode.F9))
    {
        ExecuteCommand(); // ✅ F9 Commands working!
    }
}
```

#### **Building Events (Enhanced)**
```csharp
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;

// Type-safe building event subscription
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt =>
    {
        // evt.Building is now Building wrapper (not object!)
        var building = evt.Building;
        var position = building.GetPosition();
        var energyOutput = building.GetEnergyProduction();
        var buildingType = building.GetBuildingType();
        
        LogAspera.Info($"Building {buildingType.GetName()} placed at {position}");
        LogAspera.Info($"Energy output: {energyOutput}");
    }
);
```

#### **System Events (Enhanced)**
```csharp
// Type-safe system events
EnhancedEvents.Subscribe<BaseGameDetectedEvent>(
    SDKEventConstants.BaseGameDetected,
    evt =>
    {
        // evt.BaseGame is now BaseGame wrapper (not object!)
        var baseGame = evt.BaseGame;
        var universe = evt.Universe;
        
        LogAspera.Info("Game detected with Enhanced Event System");
        LogAspera.Info($"Universe name: {universe.GetName()}");
    }
);
```

### 🔧 **Auto-Conversion Configuration**

The Enhanced Event System automatically converts native IL2CPP objects to SDK wrappers:

```csharp
using PerAspera.GameAPI.Events;

// Enable/disable auto-conversion (enabled by default)
EnhancedEvents.SetWrapperConversion(true);

// Check conversion status
var stats = EnhancedEvents.GetStats();
LogAspera.Info($"Auto-conversion: {stats.AutoConversionEnabled}");
LogAspera.Info($"Events processed: {stats.EventsProcessed}");
LogAspera.Info($"Conversion errors: {stats.ConversionErrors}");
```

### ⚡ **Performance Monitoring**

```csharp
// Monitor event system performance
var stats = EnhancedEvents.GetStats();

LogAspera.Info($"Event Types: {stats.EventTypeCount}");
LogAspera.Info($"Total Handlers: {stats.TotalHandlers}");
LogAspera.Info($"Average Conversion Time: {stats.AverageConversionTimeMs}ms");

// Performance warning
if (stats.AverageConversionTimeMs > 1.0)
{
    LogAspera.Warning("Event conversion overhead high - check wrapper cache");
}
```

### 🔄 **Backward Compatibility**

Enhanced Events maintain full backward compatibility:

```csharp
// Old code continues to work unchanged
EventSystem.Subscribe(NativeEventConstants.BuildingSpawned, (data) =>
{
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // Now automatically a Building wrapper!
    
    // Old-style access still works, but now type-safe
    var buildingTypeName = building.GetType().Name;
});

// But new type-safe pattern is recommended
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt => {
        var building = evt.Building; // Full IntelliSense support!
        // ... use building wrapper methods
    }
);
```

---

## System Events

### 🎮 Core Game Events (Enhanced)

#### `GameFullyLoadedEvent` ✅ **Enhanced**
**Type**: `PerAspera.Events.GameFullyLoadedEvent`

Triggered when the game has completely finished loading all systems.

```csharp
public class GameFullyLoadedEvent
{
    public BaseGame BaseGameInstance { get; }      // BaseGame wrapper ✅
    public Universe UniverseInstance { get; }      // Universe wrapper ✅  
    public Planet PlanetInstance { get; }          // Planet wrapper ✅
}
```

**Enhanced Usage:**
```csharp
using PerAspera.GameAPI.Events;

// Type-safe subscription
EnhancedEvents.Subscribe<GameFullyLoadedEvent>("game.loaded", evt =>
{
    // Full wrapper API access with IntelliSense
    var baseGame = evt.BaseGameInstance;
    var universe = evt.UniverseInstance;
    var planet = evt.PlanetInstance;
    
    LogAspera.Info($"🎮 Game fully loaded on planet: {planet.GetName()}");
    LogAspera.Info($"Universe: {universe.GetName()}");
    
    // Use wrapper methods directly
    var buildingCount = planet.GetBuildings().Count;
    LogAspera.Info($"Buildings on planet: {buildingCount}");
    
    InitializeModSystems();
});
```

#### `BaseGameDetectedEvent` ✅ **Enhanced** 
**Type**: `PerAspera.Events.BaseGameDetectedEvent`

Triggered when the base game instance is detected and available.

```csharp
public class BaseGameDetectedEvent
{
    public BaseGame BaseGame { get; }              // BaseGame wrapper ✅ (was object)
    public Universe Universe { get; }              // Universe wrapper ✅
    public bool IsReady { get; }                   // System ready status
}
```

**Enhanced Usage:**
```csharp
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;

// Type-safe BaseGame access
EnhancedEvents.Subscribe<BaseGameDetectedEvent>(
    SDKEventConstants.BaseGameDetected,
    evt =>
    {
        // No casting required - direct wrapper access
        var baseGame = evt.BaseGame;
        var universe = evt.Universe;
        
        LogAspera.Info("🎮 BaseGame detected with Enhanced System");
        
        // Direct wrapper method calls
        var gameVersion = baseGame.GetVersion();
        var universeName = universe.GetName();
        
        LogAspera.Info($"Game Version: {gameVersion}");
        LogAspera.Info($"Universe: {universeName}");
        
        if (evt.IsReady)
        {
            // Safe to initialize mod systems
            InitializeGameDependentSystems();
        }
    }
);
```

#### `PlanetDetectedEvent` 🚧 **Migration Planned**
**Type**: `PerAspera.Events.PlanetDetectedEvent`

Triggered when a planet instance becomes available.

```csharp
public class PlanetDetectedEvent
{
    public object PlanetInstance { get; }        // Planet instance
    public string PlanetName { get; }            // Planet identifier
    public Vector3 PlanetPosition { get; }       // Position in space
}
```

### ⏰ Time Events

#### `sol.changed`
**Type**: `Game.SolChangeEvent`

Triggered when a new Martian sol (day) begins.

```csharp
public class SolChangeEvent
{
    public int NewSol { get; set; }              // New sol number
    public int PreviousSol { get; set; }         // Previous sol
    public string SolPhase { get; set; }         // "dawn", "day", "dusk", "night"
    public DateTime GameTime { get; set; }       // Current game time
}
```

#### `game.paused`
**Type**: `Game.PauseEvent`

Triggered when the game is paused.

#### `game.resumed`
**Type**: `Game.ResumeEvent`

Triggered when the game is resumed from pause.

### 💾 Save/Load Events

#### `save.data.written`
**Type**: `SaveSystem.DataWrittenEvent`

Triggered when game data is saved to disk.

#### `save.data.loaded`
**Type**: `SaveSystem.DataLoadedEvent`

Triggered when a save file is loaded.

---

## Climate Events

### 🌡️ Temperature Events

#### `climate.temperature.changed`
**Type**: `Climate.TemperatureChangeEvent`

Triggered when planetary temperature changes significantly.

```csharp
public class TemperatureChangeEvent
{
    public float CurrentTemperature { get; set; }    // Current temp (K)
    public float PreviousTemperature { get; set; }   // Previous temp (K)
    public float DeltaTemperature { get; set; }      // Change amount (K)
    public string CauseType { get; set; }            // "greenhouse", "solar", "atmospheric"
    public Vector3 RegionCenter { get; set; }        // Affected region center
    public float RegionRadius { get; set; }          // Affected radius (km)
}
```

**Temperature Milestones:**
- `220K` (-53°C) - Significant warming threshold
- `240K` (-33°C) - Enhanced CO2 sublimation
- `260K` (-13°C) - Human activity comfort zone  
- `273K` (0°C) - Water melting point
- `290K` (17°C) - Human habitability range

### 🌫️ Atmospheric Events

#### `climate.pressure.changed`
**Type**: `Climate.PressureChangeEvent`

Triggered when atmospheric pressure changes.

```csharp
public class PressureChangeEvent
{
    public float CurrentPressure { get; set; }       // Current pressure (atm)
    public float PreviousPressure { get; set; }      // Previous pressure (atm)
    public float DeltaPressure { get; set; }         // Pressure change (atm)
    public AtmosphericComposition Composition { get; set; } // Gas composition
    public List<string> ContributingBuildings { get; set; } // Contributing buildings
}
```

**Pressure Thresholds:**
- `0.001 atm` - Detectable atmosphere
- `0.006 atm` - Liquid water possible (with heat)
- `0.01 atm` - Thin atmosphere
- `0.16 atm` - Human survival with equipment
- `0.5 atm` - Breathable with O2 mask
- `1.0 atm` - Earth-like conditions

#### `climate.atmosphere.composition.changed`
**Type**: `Climate.AtmosphereCompositionEvent`

Triggered when atmospheric gas composition changes.

```csharp
public class AtmosphereCompositionEvent
{
    public AtmosphericComposition NewComposition { get; set; }
    public AtmosphericComposition OldComposition { get; set; }
    public string ChangedGas { get; set; }           // Which gas changed
    public float ConcentrationDelta { get; set; }    // Change amount
}

public class AtmosphericComposition
{
    public float CO2Pressure { get; set; }           // CO2 partial pressure
    public float O2Pressure { get; set; }            // O2 partial pressure
    public float N2Pressure { get; set; }            // N2 partial pressure
    public float ArPressure { get; set; }            // Argon partial pressure
    public float H2OPressure { get; set; }           // Water vapor pressure
    public float GHGPressure { get; set; }           // Greenhouse gases pressure
}
```

### 💧 Water Events

#### `climate.water.stock.changed`
**Type**: `Climate.WaterStockEvent`

Triggered when planetary water reserves change.

```csharp
public class WaterStockEvent
{
    public float CurrentStock { get; set; }          // Current water stock
    public float PreviousStock { get; set; }         // Previous stock level
    public float DeltaStock { get; set; }            // Change amount
    public WaterSource Source { get; set; }          // Source of change
    public List<Vector3> ExtractionSites { get; set; } // Active extraction sites
}

public enum WaterSource
{
    Underground,        // Underground extraction
    Atmospheric,        // Atmospheric water vapor
    Asteroid,          // Asteroid impact
    Industrial,        // Industrial processes
    Biological         // Biological processes
}
```

---

## Building Events

### 🏗️ Construction Events

#### `building.construction.started`
**Type**: `Building.ConstructionStartedEvent`

Triggered when building construction begins.

```csharp
public class BuildingConstructionStartedEvent
{
    public string BuildingType { get; set; }         // Building type identifier
    public string BuildingId { get; set; }           // Unique building ID
    public Vector3 Position { get; set; }            // Construction position
    public float EstimatedTime { get; set; }         // Estimated completion time (sols)
    public Dictionary<string, float> ResourceCosts { get; set; } // Resource requirements
    public string ConstructionMethod { get; set; }   // "drone", "manual", "automated"
}
```

#### `building.construction.completed`
**Type**: `Building.ConstructionCompletedEvent`

Triggered when building construction finishes.

```csharp
public class BuildingConstructionCompletedEvent
{
    public string BuildingType { get; set; }         // Completed building type
    public string BuildingId { get; set; }           // Building identifier
    public Vector3 Position { get; set; }            // Final position
    public float ActualTime { get; set; }            // Actual construction time
    public bool IsOperational { get; set; }          // Ready for operation
}
```

#### `building.destroyed`
**Type**: `Building.DestroyedEvent`

Triggered when a building is destroyed.

**Common Destruction Causes:**
- Meteorite impacts
- Dust storms
- System failures
- Manual demolition
- Sabotage (campaign mode)
- Natural wear and tear

### ⚡ Power System Events

#### `power.grid.status.changed`
**Type**: `Power.GridStatusEvent`

Triggered when power grid status changes.

```csharp
public class PowerGridStatusEvent
{
    public float TotalGeneration { get; set; }       // Total power generation (MW)
    public float TotalConsumption { get; set; }      // Total power consumption (MW)
    public float NetPower { get; set; }              // Net available power (MW)
    public float GridEfficiency { get; set; }        // Grid efficiency (0-1)
    public PowerStatus Status { get; set; }          // Overall grid status
    public List<string> CriticalBuildings { get; set; } // Buildings at risk
}

public enum PowerStatus
{
    Surplus,           // Power surplus available
    Balanced,          // Generation matches consumption
    Deficit,           // Power shortage
    Critical,          // Critical shortage
    Emergency          // Emergency state
}
```

#### `power.outage.detected`
**Type**: `Power.OutageEvent`

Triggered when a power outage occurs.

### 🏭 Production Events

#### `production.cycle.completed`
**Type**: `Production.CycleCompletedEvent`

Triggered when a production cycle finishes.

```csharp
public class ProductionCycleCompletedEvent
{
    public string BuildingId { get; set; }           // Producer building ID
    public string ResourceType { get; set; }         // Resource produced
    public float AmountProduced { get; set; }        // Quantity produced
    public float Efficiency { get; set; }            // Cycle efficiency (0-1)
    public TimeSpan CycleDuration { get; set; }      // Cycle duration
    public Dictionary<string, float> InputResources { get; set; } // Resources consumed
    public List<string> QualityIssues { get; set; }  // Production issues
}
```

#### `building.efficiency.changed`
**Type**: `Building.EfficiencyChangedEvent`

Triggered when building efficiency changes.

**Efficiency Factors:**
- Maintenance requirements
- Environmental conditions (temperature, dust)
- Resource availability
- Building age
- Technology upgrades

---

## Resource Events

### 📦 Resource Management

#### `resource.production.changed`
**Type**: `Resource.ProductionChangedEvent`

Triggered when resource production rates change.

#### `resource.consumption.changed`
**Type**: `Resource.ConsumptionChangedEvent`

Triggered when resource consumption patterns change.

#### `resource.shortage.detected`
**Type**: `Resource.ShortageEvent`

Triggered when resource shortages are detected.

#### `resource.surplus.detected`
**Type**: `Resource.SurplusEvent`

Triggered when resource surpluses occur.

---

## Quest Events

### 📖 Campaign Progression

#### `quest.objective.completed`
**Type**: `Quest.ObjectiveCompletedEvent`

Triggered when quest objectives are accomplished.

#### `quest.dialogue.triggered`
**Type**: `Quest.DialogueEvent`

Triggered when character dialogues occur.

**Main Characters:**
- **AMI** - Primary artificial intelligence
- **Elya** - Chief scientist (may be replaced by Sonna)
- **Nathan** - Technical lead (may be replaced by Carlos)
- **Carlos** - Engineer replacement character
- **Carmine** - Specialized agent
- **Nian** - Mysterious contact

### 🔬 Research Events

#### `research.technology.unlocked`
**Type**: `Research.TechnologyUnlockedEvent`

Triggered when new technologies are unlocked.

**Research Domains:**
- **Engineering** - Buildings and infrastructure
- **Space** - Asteroids and satellites
- **Biology** - Plants and life support
- **Military** - Defense systems
- **Advanced** - Cutting-edge technologies

### 🌌 Asteroid Events

#### `asteroid.impact.incoming`
**Type**: `Asteroid.ImpactIncomingEvent`

Triggered when asteroids are approaching impact.

**Asteroid Types and Effects:**
- **Methane** - Increases greenhouse effect (+temperature)
- **Ice** - Adds water and atmospheric pressure
- **Nitrogen** - Improves atmospheric composition
- **Metal** - Provides construction materials

---

## SDK Integration

### 🔧 Event Subscription

#### Basic Subscription
```csharp
// Subscribe to game events
ModSDK.Events.Subscribe("game.loaded", OnGameLoaded);
ModSDK.Events.Subscribe("climate.temperature.changed", OnTemperatureChanged);
ModSDK.Events.Subscribe("building.construction.completed", OnBuildingCompleted);

// Event handlers
private void OnGameLoaded(object eventData)
{
    Log.LogInfo("🎮 Game loaded - starting mod operations");
}

private void OnTemperatureChanged(object eventData)
{
    var tempEvent = eventData as TemperatureChangeEvent;
    Log.LogInfo($"🌡️ Temperature: {tempEvent.CurrentTemperature:F1}K (Δ{tempEvent.DeltaTemperature:+0.0;-0.0})");
}
```

#### Filtered Subscription
```csharp
// Subscribe only to significant changes
ModSDK.Events.Subscribe("climate.temperature.changed", OnSignificantTempChange, 
    filter: (data) => {
        var tempEvent = data as TemperatureChangeEvent;
        return Math.Abs(tempEvent.DeltaTemperature) > 1.0f; // Only >1K changes
    });
```

### 🎯 Custom Events

#### Creating Custom Events
```csharp
// Define custom event types
public static class CustomEvents
{
    public const string TERRAFORMING_MILESTONE = "custom.terraforming.milestone";
    public const string RESOURCE_CRISIS = "custom.resource.crisis";
    public const string EFFICIENCY_OPTIMIZATION = "custom.efficiency.optimization";
}

// Trigger custom events
var milestoneEvent = new TerraformingMilestoneEvent
{
    Percentage = 25f,
    Description = "25% terraforming completion reached"
};

ModSDK.Events.Trigger(CustomEvents.TERRAFORMING_MILESTONE, milestoneEvent);
```

### ⚡ Performance Optimization

#### Event Batching
```csharp
public class EventBatcher
{
    private readonly Dictionary<string, List<object>> eventBuffer = new();
    private readonly Timer batchTimer;
    
    public EventBatcher()
    {
        // Process events every 30 seconds
        batchTimer = new Timer(ProcessBatchedEvents, null, 
            TimeSpan.Zero, TimeSpan.FromSeconds(30));
    }
    
    public void BufferEvent(string eventType, object eventData)
    {
        if (!eventBuffer.ContainsKey(eventType))
            eventBuffer[eventType] = new List<object>();
            
        eventBuffer[eventType].Add(eventData);
    }
    
    private void ProcessBatchedEvents(object state)
    {
        foreach (var eventGroup in eventBuffer)
        {
            if (eventGroup.Value.Count > 0)
            {
                Log.LogInfo($"📊 Processing {eventGroup.Value.Count} {eventGroup.Key} events");
                ProcessEventBatch(eventGroup.Key, eventGroup.Value);
                eventGroup.Value.Clear();
            }
        }
    }
}
```

---

## 🚀 Next Steps

### 📚 Related Documentation

1. **[SDK API Reference](SDK-API-Reference)** - Complete API documentation
2. **[Climate System Guide](Climate-System-Guide)** - Advanced climate manipulation
3. **[Building System Guide](Building-System-Guide)** - Infrastructure management
4. **[Quick Reference](Quick-Reference)** - Essential patterns and examples

### 💡 Community Resources

- **[Discord Community](https://discord.gg/peraspera-modding)** - Real-time help and discussion
- **[GitHub Discussions](https://github.com/PerAsperaMods/PerAspera-SDK/discussions)** - Q&A and showcases
- **[Code Examples](Code-Examples)** - Working mod projects and patterns

---

**Master the Per Aspera event system to create responsive, intelligent mods that react dynamically to Mars terraforming progress! 🔴➡️🌍**