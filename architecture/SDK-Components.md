# SDK Components Reference

## 📚 Introduction

The Per Aspera ModSDK is structured as a **multi-project solution** with clear separation of concerns. This modular architecture allows you to use only the components you need while maintaining clean dependencies and testability.

**Quick Navigation:**
- [Core Layer](#core-layer) - Foundation utilities
- [GameAPI Layer](#gameapi-layer) - Native game access
- [Feature Modules](#feature-modules) - Domain-specific functionality
- [SDK Layer](#sdk-layer) - Public API
- [Dependency Graph](#dependency-graph)
- [Usage Guide](#usage-guide)

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    PerAspera.ModSDK                     │
│              (Unified Public API for Mods)              │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┼────────────┬──────────────┐
        │            │            │              │
┌───────▼──────┐ ┌──▼──────┐ ┌──▼──────┐ ┌─────▼─────┐
│   GameAPI    │ │ Events  │ │ Climate │ │ Overrides │
│  .Wrappers   │ │         │ │         │ │           │
└───────┬──────┘ └──┬──────┘ └──┬──────┘ └─────┬─────┘
        │           │            │              │
        └───────────┴────────────┴──────────────┘
                     │
        ┌────────────▼────────────────┐
        │    PerAspera.GameAPI        │
        │  (Native Game Access)       │
        └────────────┬────────────────┘
                     │
        ┌────────────┼────────────────┐
        │            │                │
┌───────▼──────┐ ┌──▼──────────────┐ │
│ PerAspera    │ │  PerAspera.Core │ │
│   .Core      │ │  .IL2CppExtensions│
└──────────────┘ └─────────────────┘ │
                                      │
                ┌─────────────────────▼┐
                │ PerAspera.Abstractions│
                │  (Interfaces & Base)  │
                └───────────────────────┘
```

---

## 🔧 Core Layer

### PerAspera.Core

**Purpose:** Foundation utilities with zero game dependencies. Pure C# helper library.

**Key Components:**
```csharp
// Logging utility
LogAspera.cs
- LogAspera.Info(string message)
- LogAspera.Warning(string message)
- LogAspera.Error(string message, Exception ex)

// Reflection helpers
ReflectionHelpers.cs
- GetFieldValue<T>(object obj, string fieldName)
- SetFieldValue(object obj, string fieldName, object value)
- InvokeMethod(object obj, string methodName, params object[] args)

// Type extensions
TypeExtensions.cs
- IsAssignableToGenericType(this Type type, Type genericType)
- GetGenericTypeDefinition(this Type type)

// Cargo quantity helpers
CargoQuantityHelper.cs
- ConvertToCargoQuantity(float value, string resourceType)
- ExtractValue(CargoQuantity cargoQuantity)
```

**Dependencies:** None (pure utilities)

**When to Use:**
- ✅ Logging with consistent format
- ✅ Reflection operations on game objects
- ✅ Type checking and conversion
- ✅ Resource quantity calculations

**Example Usage:**
```csharp
using PerAspera.Core;

public class MyMod : BasePlugin
{
    public override void Load()
    {
        LogAspera.Info("MyMod initializing...");
        
        // Reflection example
        var buildingType = typeof(Building);
        var nameField = ReflectionHelpers.GetFieldValue<string>(buildingInstance, "name");
        
        LogAspera.Info($"Building name: {nameField}");
    }
}
```

---

### PerAspera.Core.IL2CppExtensions

**Purpose:** IL2CPP-specific extension methods and interop helpers.

**Key Components:**
```csharp
// IL2CPP type extensions
IL2CppTypeExtensions.cs
- ToCSharp(this Il2CppString str) → string
- ToIl2Cpp(this string str) → Il2CppString
- ToCSharpArray<T>(this Il2CppReferenceArray<T> arr)
- ToIl2CppList<T>(this IEnumerable<T> items)

// Native object helpers
NativeObjectExtensions.cs
- IsValid(this Il2CppObjectBase obj)
- GetNativePointer(this Object obj)
- WrapNative<T>(IntPtr ptr) where T : Il2CppObjectBase
```

**Dependencies:** 
- PerAspera.Core
- BepInEx.IL2CPP
- Il2CppInterop.Runtime

**When to Use:**
- ✅ Converting between C# and IL2CPP types
- ✅ Working with Il2CppSystem collections
- ✅ Native pointer manipulation
- ✅ IL2CPP object validation

**Example Usage:**
```csharp
using PerAspera.Core.IL2CppExtensions;

public void ProcessBuildings(Il2CppReferenceArray<Building> nativeBuildings)
{
    // Convert IL2CPP array to C# list
    var buildings = nativeBuildings.ToCSharpArray();
    
    foreach (var building in buildings)
    {
        if (building.IsValid())
        {
            var name = building.name.ToCSharp();
            LogAspera.Info($"Processing: {name}");
        }
    }
}
```

---

### PerAspera.Abstractions

**Purpose:** Interface definitions and base types for SDK patterns.

**Key Components:**
```csharp
// Base interfaces
IGameWrapper.cs
IEventData.cs
IOverrideStrategy<T>.cs
IModComponent.cs

// Base classes
GameComponentBase.cs
ModuleBase.cs
```

**Dependencies:** PerAspera.Core

**When to Use:**
- ✅ Implementing custom wrappers
- ✅ Creating event data types
- ✅ Building override strategies
- ✅ Structuring mod components

**Example Usage:**
```csharp
using PerAspera.Abstractions;

public class CustomWrapper : GameComponentBase
{
    private readonly Planet _planet;
    
    public CustomWrapper(Planet planet)
    {
        _planet = planet ?? throw new ArgumentNullException(nameof(planet));
    }
    
    public override void Initialize()
    {
        LogAspera.Info("Custom wrapper initialized");
    }
}
```

---

## 🎮 GameAPI Layer

### PerAspera.GameAPI

**Purpose:** Direct access to native game systems with minimal abstraction.

**Key Components:**
```csharp
// Native registry
Native/KeeperTypeRegistry.cs
- RegisterType<T>(string typeName)
- GetInstance<T>(string keeperKey)
- TryGetInstance<T>(string keeperKey, out T instance)

// Event patching
Native/NativeEventPatcher.cs
- PatchMethod(MethodInfo method, HarmonyMethod prefix, HarmonyMethod postfix)
- InstallEventPatches()

// Climate access
Helpers/ClimateHelper.cs
- GetTemperature() → float
- SetTemperature(float value)
- GetCO2Pressure() → float
- GetSnapshot() → ClimateSnapshot

// Data models
Models/ClimateSnapshot.cs
Models/AtmosphereData.cs
Models/TerraformingStatus.cs
```

**Dependencies:**
- PerAspera.Core
- PerAspera.Core.IL2CppExtensions
- PerAspera.Abstractions
- HarmonyX

**When to Use:**
- ✅ Direct game state access
- ✅ Installing Harmony patches
- ✅ Reading climate data
- ✅ Registering custom Keeper types

**Example Usage:**
```csharp
using PerAspera.GameAPI.Helpers;
using PerAspera.GameAPI.Models;

public class ClimateMonitor
{
    public void LogCurrentClimate()
    {
        var snapshot = ClimateHelper.GetSnapshot();
        
        LogAspera.Info($"Temperature: {snapshot.Temperature}°C");
        LogAspera.Info($"CO2 Pressure: {snapshot.CO2Pressure} Pa");
        LogAspera.Info($"Habitability: {snapshot.Habitability}%");
    }
    
    public void AdjustTemperature(float delta)
    {
        float current = ClimateHelper.GetTemperature();
        ClimateHelper.SetTemperature(current + delta);
        LogAspera.Info($"Temperature adjusted by {delta}°C");
    }
}
```

---

### PerAspera.GameAPI.Wrappers

**Purpose:** Type-safe, high-level wrappers for native game objects.

**Key Components:**
```csharp
// Base wrapper
WrapperBase.cs
- Protected access to native object
- Automatic null checks
- ToString() override for debugging

// Specific wrappers
Building.cs
- string TypeKey { get; }
- Vector3 Position { get; }
- float EnergyProduction { get; }
- bool IsOperational { get; }

Planet.cs
- string Name { get; }
- IEnumerable<Building> Buildings { get; }
- ClimateSnapshot Climate { get; }
- ResourceStocks Resources { get; }

Universe.cs
- Planet CurrentPlanet { get; }
- IEnumerable<Faction> Factions { get; }
- GameTime CurrentTime { get; }
```

**Dependencies:**
- PerAspera.Core
- PerAspera.GameAPI

**When to Use:**
- ✅ Safe access to game objects
- ✅ Avoiding null reference exceptions
- ✅ Working with collections of game entities
- ✅ Readable, maintainable mod code

**Example Usage:**
```csharp
using PerAspera.GameAPI.Wrappers;

public class BuildingAnalyzer
{
    public void AnalyzeSolarPanels(Planet planet)
    {
        var solarPanels = planet.Buildings
            .Where(b => b.TypeKey == "SolarPanel")
            .ToList();
        
        float totalEnergy = solarPanels.Sum(sp => sp.EnergyProduction);
        int operational = solarPanels.Count(sp => sp.IsOperational);
        
        LogAspera.Info($"Solar Panels: {solarPanels.Count} total, {operational} operational");
        LogAspera.Info($"Total Energy: {totalEnergy} MW");
    }
}
```

---

## 🔌 Feature Modules

### PerAspera.GameAPI.Events

**Purpose:** Event system for reactive programming with game lifecycle hooks.

**Key Components:**
```csharp
// Event interfaces
Core/IGameEvent.cs
Core/GameEventBase.cs
Core/NativeGameEventBase.cs

// Event data types
Data/ClimateEventData.cs
- float PreviousTemperature
- float NewTemperature
- float Delta
- MartianDay When

Data/BuildingEventData.cs
- Building Building
- BuildingType Type
- Vector3 Position

Data/MartianDayEventData.cs
- int DayNumber
- float GameTime
- Season CurrentSeason

// Native events
Native/ClimateEvents.cs
- OnTemperatureChanged
- OnCO2PressureChanged
- OnHabitabilityThresholdReached

Native/BuildingEvents.cs
- OnBuildingPlaced
- OnBuildingRemoved
- OnBuildingUpgraded

// Event constants
Constants/SDKEventConstants.cs
- Event type identifiers
- Priority levels
```

**Dependencies:**
- PerAspera.Core
- PerAspera.GameAPI
- HarmonyX

**When to Use:**
- ✅ Reacting to game state changes
- ✅ Avoiding Update() polling overhead
- ✅ Decoupling mod components
- ✅ Building event-driven systems

**Example Usage:**
```csharp
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Data;

public class EventDrivenMod : BasePlugin
{
    public override void Load()
    {
        // Subscribe to climate events
        ClimateEvents.OnTemperatureChanged += HandleTemperatureChange;
        
        // Subscribe to building events
        BuildingEvents.OnBuildingPlaced += HandleBuildingPlaced;
        
        LogAspera.Info("Event subscriptions registered");
    }
    
    private void HandleTemperatureChange(ClimateEventData data)
    {
        if (data.Delta > 5f)
        {
            LogAspera.Warning($"Large temperature spike: +{data.Delta}°C");
        }
    }
    
    private void HandleBuildingPlaced(BuildingEventData data)
    {
        if (data.Type.Key == "Heater")
        {
            LogAspera.Info($"Heater placed at {data.Position}");
        }
    }
}
```

---

### PerAspera.GameAPI.Overrides

**Purpose:** Generic system for overriding game getters and calculations with custom logic.

**Key Components:**
```csharp
// Override models
Models/GetterOverride<T>.cs
- string TargetProperty
- IOverrideStrategy<T> Strategy
- int Priority
- bool Enabled

Models/IOverrideStrategy<T>.cs
- T Calculate(T original)
- bool ShouldApply(object context)

Models/OverrideStrategies/
- MultiplicativeStrategy<T>
- AdditiveStrategy<T>
- ReplacementStrategy<T>
- ConditionalStrategy<T>

// Registry
Registry/GetterOverrideRegistry.cs
- RegisterOverride<T>(GetterOverride<T> override)
- UnregisterOverride(string id)
- GetOverrides<T>(string propertyName)
- ApplyOverrides<T>(string property, T original)

// Patching system
Patching/AutoOverridePatchAttribute.cs
Patching/OverridePatchSystem.cs
- InstallPatches()
- PatchGetter(PropertyInfo property)
```

**Dependencies:**
- PerAspera.Core
- PerAspera.Core.IL2CppExtensions
- HarmonyX

**When to Use:**
- ✅ Modifying game calculations
- ✅ Scaling resource production
- ✅ Adjusting building stats
- ✅ Implementing difficulty modifiers

**Example Usage:**
```csharp
using PerAspera.GameAPI.Overrides;
using PerAspera.GameAPI.Overrides.Models;

public class ProductionBoostMod : BasePlugin
{
    public override void Load()
    {
        // Create multiplicative override for energy production
        var energyBoost = new GetterOverride<float>
        {
            TargetProperty = "Building.EnergyProduction",
            Strategy = new MultiplicativeStrategy<float>(1.5f), // +50% boost
            Priority = 100,
            Enabled = true
        };
        
        GetterOverrideRegistry.RegisterOverride(energyBoost);
        
        LogAspera.Info("Energy production boosted by 50%");
    }
}

// Custom strategy example
public class NightPenaltyStrategy : IOverrideStrategy<float>
{
    public float Calculate(float originalProduction)
    {
        bool isNight = GameTime.CurrentHour < 6 || GameTime.CurrentHour > 18;
        return isNight ? originalProduction * 0.7f : originalProduction;
    }
    
    public bool ShouldApply(object context)
    {
        return context is Building building && building.TypeKey == "SolarPanel";
    }
}
```

---

### PerAspera.GameAPI.Climate

**Purpose:** Advanced climate simulation, analysis, and forecasting capabilities.

**Key Components:**
```csharp
// Simulation
Simulation/ClimateSimulator.cs
- SimulateStep(int martianDays) → ClimateSnapshot[]
- PredictTemperature(int daysAhead) → float
- CalculateGreenhouseEffect() → float

Simulation/Models/
- GreenhouseModel.cs
- TemperatureModel.cs
- PressureModel.cs

// Analysis
Analysis/HabitabilityAnalyzer.cs
- CalculateHabitability(ClimateSnapshot snapshot) → float
- GetBreathableAirStatus() → BreathableAirStatus
- EstimateTimeToHabitable() → int (days)

Analysis/TerraformingAnalyzer.cs
- AnalyzeProgress() → TerraformingStatus
- GetBottlenecks() → string[]
- SuggestImprovements() → TerraformingAction[]

Analysis/BuildingImpactAnalyzer.cs
- CalculateBuildingImpact(Building b) → ClimateImpact
- GetOptimalPlacements(BuildingType type) → Vector3[]

// Forecasting
Forecasting/ClimateForecast.cs
- GenerateForecast(int days) → ForecastData
- GetConfidenceInterval() → (float min, float max)

// Configuration
Configuration/ClimateConfig.cs
Configuration/TerraformingConstants.cs
```

**Dependencies:**
- PerAspera.Core
- PerAspera.GameAPI
- PerAspera.GameAPI.Events
- PerAspera.GameAPI.Overrides

**When to Use:**
- ✅ Climate prediction systems
- ✅ Habitability calculations
- ✅ Terraforming optimization
- ✅ Building placement analysis
- ✅ Advanced climate mods

**Example Usage:**
```csharp
using PerAspera.GameAPI.Climate;
using PerAspera.GameAPI.Climate.Analysis;

public class ClimateDashboard : BasePlugin
{
    private HabitabilityAnalyzer _analyzer;
    
    public override void Load()
    {
        _analyzer = new HabitabilityAnalyzer();
        
        SDK.Events.OnMartianDayChanged += UpdateDashboard;
    }
    
    private void UpdateDashboard(MartianDayEventData data)
    {
        var habitability = _analyzer.CalculateHabitability(ClimateHelper.GetSnapshot());
        var airStatus = _analyzer.GetBreathableAirStatus();
        var daysRemaining = _analyzer.EstimateTimeToHabitable();
        
        LogAspera.Info($"Habitability: {habitability}%");
        LogAspera.Info($"Air Status: {airStatus}");
        LogAspera.Info($"Days to Habitable: {daysRemaining}");
    }
}
```

---

### PerAspera.GameAPI.Commands

**Purpose:** Command system for mod-to-mod communication and extensibility.

**Key Components:**
```csharp
// Command infrastructure
Core/ICommand.cs
Core/CommandBase.cs
Core/CommandResult.cs

// Registry
Registry/CommandRegistry.cs
- RegisterCommand(string name, ICommand command)
- ExecuteCommand(string name, params object[] args)
- GetAvailableCommands() → string[]

// Built-in commands
Commands/ClimateCommands.cs
Commands/BuildingCommands.cs
Commands/DebugCommands.cs
```

**Dependencies:**
- PerAspera.Core
- PerAspera.GameAPI

**When to Use:**
- ✅ Mod-to-mod communication
- ✅ Console command systems
- ✅ Scriptable game actions
- ✅ Debugging tools

**Example Usage:**
```csharp
using PerAspera.GameAPI.Commands;

public class CustomCommand : CommandBase
{
    public override string Name => "boost_solar";
    public override string Description => "Temporarily boosts solar panel output";
    
    public override CommandResult Execute(params object[] args)
    {
        float multiplier = args.Length > 0 ? (float)args[0] : 2.0f;
        
        // Apply boost logic
        ApplySolarBoost(multiplier);
        
        return CommandResult.Success($"Solar panels boosted by {multiplier}x");
    }
}

// Register in plugin
public override void Load()
{
    CommandRegistry.RegisterCommand("boost_solar", new CustomCommand());
}
```

---

## 🎯 SDK Layer

### PerAspera.ModSDK

**Purpose:** Unified, simplified public API that orchestrates all SDK components.

**Key Components:**
```csharp
// Main SDK class
ModSDK.cs
- static Events EventSystem { get; }
- static Wrappers WrapperFactory { get; }
- static Overrides OverrideRegistry { get; }
- static Climate ClimateAPI { get; }
- static Commands CommandRegistry { get; }
- static Logger LogService { get; }

// Plugin base
PerAsperaSDKPlugin.cs
- Initializes all SDK systems
- Manages lifecycle
- Provides unified logging

// System facades
Systems/EventSystemFacade.cs
Systems/WrapperSystemFacade.cs
Systems/OverrideSystemFacade.cs
```

**Dependencies:** ALL sub-projects

**When to Use:**
- ✅ **Always** - This is your primary mod interface
- ✅ Unified access to all SDK features
- ✅ Simplified API surface
- ✅ Consistent patterns across features

**Example Usage:**
```csharp
using PerAspera.ModSDK;

[BepInPlugin(GUID, NAME, VERSION)]
[BepInDependency(PerAsperaSDKPlugin.GUID)] // Declare dependency
public class MyMod : BasePlugin
{
    public override void Load()
    {
        // Access everything through SDK
        SDK.Logger.Info("MyMod initializing...");
        
        // Events
        SDK.Events.OnBuildingPlaced += HandleBuildingPlaced;
        
        // Climate
        var climate = SDK.Climate.GetSnapshot();
        SDK.Logger.Info($"Current temperature: {climate.Temperature}°C");
        
        // Overrides
        SDK.Overrides.RegisterMultiplier("Building.EnergyProduction", 1.5f);
        
        // Commands
        SDK.Commands.Register("my_command", new MyCustomCommand());
    }
    
    private void HandleBuildingPlaced(BuildingEventData data)
    {
        // Use wrappers for safe access
        var wrapper = SDK.Wrappers.WrapBuilding(data.Building);
        SDK.Logger.Info($"Building placed: {wrapper.TypeKey}");
    }
}
```

---

## 📊 Dependency Graph

### Visual Dependency Flow

```
┌─────────────────────────────────────────┐
│          PerAspera.ModSDK               │ ← Your mod depends on this
└─────────────┬───────────────────────────┘
              │ (Orchestrates all)
      ┌───────┼───────┬───────┬───────────┐
      │       │       │       │           │
┌─────▼───┐ ┌▼─────┐ ┌▼────┐ ┌▼────────┐ ┌▼────────┐
│ GameAPI │ │Events│ │Climate│Overrides│ │Commands │
│.Wrappers│ │      │ │      │ │         │ │         │
└────┬────┘ └──┬───┘ └──┬───┘ └────┬────┘ └────┬────┘
     │         │        │          │           │
     └─────────┴────────┴──────────┴───────────┘
              │
     ┌────────▼─────────┐
     │ PerAspera.GameAPI│
     └────────┬─────────┘
              │
     ┌────────┼─────────────────┐
     │        │                 │
┌────▼─────┐ ┌▼───────────────┐│
│PerAspera │ │ PerAspera.Core ││
│.Core     │ │.IL2CppExtensions││
└──────────┘ └────────────────┘│
                                │
          ┌─────────────────────▼┐
          │ PerAspera.Abstractions│
          └───────────────────────┘
```

### Dependency Rules

**✅ Allowed Dependencies:**
```csharp
// Lower layer → Higher layer (NEVER)
// Same layer → Same layer (OK, but minimize)
// Higher layer → Lower layer (YES)

PerAspera.ModSDK → ALL (orchestrator)
Feature Modules → GameAPI, Core, Abstractions
GameAPI → Core, IL2CppExtensions, Abstractions
Core.IL2CppExtensions → Core
Core → NONE (pure utilities)
```

**❌ Forbidden Dependencies:**
```csharp
// NEVER: Lower layers depend on higher layers
Core → GameAPI ❌
GameAPI → Events ❌
Abstractions → ModSDK ❌

// NEVER: Direct game assembly references
MyMod → Assembly-CSharp ❌
MyMod → Il2CppInterop.Generator ❌
```

---

## 🎓 Usage Guide

### Decision Tree: Which Component Do I Need?

```
Start Here: What do you want to do?
│
├─ "Log messages" → PerAspera.Core (LogAspera)
│
├─ "Access game data (read-only)" → PerAspera.GameAPI.Wrappers
│   └─ Example: Building.TypeKey, Planet.Climate
│
├─ "React to game events" → PerAspera.GameAPI.Events
│   └─ Example: OnBuildingPlaced, OnTemperatureChanged
│
├─ "Modify game values" → PerAspera.GameAPI.Overrides
│   └─ Example: Boost energy production, adjust costs
│
├─ "Climate simulation/prediction" → PerAspera.GameAPI.Climate
│   └─ Example: Forecast habitability, optimize terraforming
│
├─ "Mod-to-mod communication" → PerAspera.GameAPI.Commands
│   └─ Example: Expose API for other mods
│
├─ "IL2CPP type conversion" → PerAspera.Core.IL2CppExtensions
│   └─ Example: Il2CppString ↔ string, arrays
│
└─ "Everything together (recommended)" → PerAspera.ModSDK
    └─ Unified API, automatic initialization
```

### Simple Mod Example (ModSDK Only)

```csharp
using BepInEx;
using BepInEx.Logging;
using PerAspera.ModSDK;
using PerAspera.GameAPI.Events.Data;

namespace MyFirstMod
{
    [BepInPlugin(GUID, NAME, VERSION)]
    [BepInDependency(PerAsperaSDKPlugin.GUID)]
    public class MyFirstMod : BasePlugin
    {
        private const string GUID = "com.author.myfirstmod";
        private const string NAME = "My First Mod";
        private const string VERSION = "1.0.0";
        
        public override void Load()
        {
            SDK.Logger.Info($"{NAME} v{VERSION} loading...");
            
            // Subscribe to events
            SDK.Events.OnBuildingPlaced += OnBuildingPlaced;
            SDK.Events.OnTemperatureChanged += OnTemperatureChanged;
            
            // Apply override
            SDK.Overrides.RegisterMultiplier("Building.EnergyProduction", 2.0f);
            
            SDK.Logger.Info($"{NAME} loaded successfully!");
        }
        
        private void OnBuildingPlaced(BuildingEventData data)
        {
            SDK.Logger.Info($"Building placed: {data.Type.Key} at {data.Position}");
        }
        
        private void OnTemperatureChanged(ClimateEventData data)
        {
            if (data.NewTemperature > 0f)
            {
                SDK.Logger.Info("Mars is warming up!");
            }
        }
    }
}
```

### Advanced Mod Example (Multiple Components)

```csharp
using BepInEx;
using PerAspera.ModSDK;
using PerAspera.GameAPI.Climate;
using PerAspera.GameAPI.Climate.Analysis;
using PerAspera.GameAPI.Events.Data;
using PerAspera.GameAPI.Overrides.Models;

namespace AdvancedClimateMod
{
    [BepInPlugin(GUID, NAME, VERSION)]
    [BepInDependency(PerAsperaSDKPlugin.GUID)]
    public class AdvancedClimateMod : BasePlugin
    {
        private const string GUID = "com.author.advancedclimate";
        private const string NAME = "Advanced Climate Mod";
        private const string VERSION = "1.0.0";
        
        private HabitabilityAnalyzer _analyzer;
        private TerraformingAnalyzer _terraformAnalyzer;
        
        public override void Load()
        {
            SDK.Logger.Info($"{NAME} initializing...");
            
            // Initialize analyzers
            _analyzer = new HabitabilityAnalyzer();
            _terraformAnalyzer = new TerraformingAnalyzer();
            
            // Subscribe to events
            SDK.Events.OnMartianDayChanged += AnalyzeTerraforming;
            SDK.Events.OnBuildingPlaced += OptimizeBuildingImpact;
            
            // Register custom override strategy
            var dynamicHeaterStrategy = new DynamicHeaterStrategy();
            var heaterOverride = new GetterOverride<float>
            {
                TargetProperty = "Heater.TemperatureOutput",
                Strategy = dynamicHeaterStrategy,
                Priority = 100,
                Enabled = true
            };
            SDK.Overrides.Register(heaterOverride);
            
            SDK.Logger.Info($"{NAME} loaded!");
        }
        
        private void AnalyzeTerraforming(MartianDayEventData data)
        {
            var progress = _terraformAnalyzer.AnalyzeProgress();
            var bottlenecks = _terraformAnalyzer.GetBottlenecks();
            
            SDK.Logger.Info($"Terraforming Progress: {progress.OverallProgress}%");
            
            if (bottlenecks.Length > 0)
            {
                SDK.Logger.Warning($"Bottlenecks detected: {string.Join(", ", bottlenecks)}");
                
                var suggestions = _terraformAnalyzer.SuggestImprovements();
                foreach (var action in suggestions)
                {
                    SDK.Logger.Info($"Suggestion: {action.Description}");
                }
            }
        }
        
        private void OptimizeBuildingImpact(BuildingEventData data)
        {
            if (data.Type.Key == "Heater" || data.Type.Key == "Greenhouse")
            {
                var impact = CalculateClimateImpact(data.Building);
                SDK.Logger.Info($"{data.Type.Key} will change temperature by {impact.TemperatureDelta}°C");
            }
        }
        
        private ClimateImpact CalculateClimateImpact(Building building)
        {
            // Use Climate module for calculations
            var analyzer = new BuildingImpactAnalyzer();
            return analyzer.CalculateBuildingImpact(building);
        }
    }
    
    // Custom override strategy
    public class DynamicHeaterStrategy : IOverrideStrategy<float>
    {
        public float Calculate(float originalOutput)
        {
            // Scale heater output based on current temperature
            var currentTemp = SDK.Climate.GetTemperature();
            
            if (currentTemp < -50f)
                return originalOutput * 1.5f; // Boost in extreme cold
            else if (currentTemp > -10f)
                return originalOutput * 0.8f; // Reduce when warmer
            
            return originalOutput;
        }
        
        public bool ShouldApply(object context)
        {
            return context is Building building && building.TypeKey == "Heater";
        }
    }
}
```

---

## 🔍 Component Selection Matrix

| Need | Component | Complexity | Example |
|------|-----------|------------|---------|
| Simple logging | Core | ⭐ | `LogAspera.Info("message")` |
| Read building data | GameAPI.Wrappers | ⭐⭐ | `building.TypeKey` |
| React to events | GameAPI.Events | ⭐⭐ | `OnBuildingPlaced +=` |
| Modify values | GameAPI.Overrides | ⭐⭐⭐ | `RegisterMultiplier()` |
| Climate prediction | GameAPI.Climate | ⭐⭐⭐⭐ | `SimulateStep(30)` |
| IL2CPP conversion | Core.IL2CppExtensions | ⭐⭐⭐ | `.ToCSharp()` |
| Everything | ModSDK | ⭐⭐ | `SDK.Events`, `SDK.Climate` |

**Recommendation:** Start with **PerAspera.ModSDK** for most mods. Only reference individual components directly if you need fine-grained control or are building SDK extensions.

---

## 📚 Related Documentation

- **[Getting Started](../getting-started/First-Mod.md)** - Build your first mod
- **[SDK Overview](../sdk/Overview.md)** - High-level SDK concepts
- **[Architecture Overview](./Overview.md)** - Modding patterns and best practices
- **[Harmony Patching](../advanced/Harmony-Patching.md)** - Advanced runtime modification
- **[Performance Guidelines](../advanced/Performance.md)** - Optimization techniques

---

## 🤝 Contributing to SDK Components

If you want to contribute new features to the SDK itself:

1. **Read** [Contributing Guidelines](../community/Contributing.md)
2. **Choose** the right project layer for your feature
3. **Follow** dependency rules (higher → lower only)
4. **Test** with multiple mods for compatibility
5. **Document** your API additions

**Feature Module Checklist:**
- ✅ Single responsibility (one domain)
- ✅ Minimal dependencies
- ✅ Public API in ModSDK facade
- ✅ Unit tests included
- ✅ Example usage documented
- ✅ Backward compatibility maintained

---

**Last Updated:** December 17, 2025  
**SDK Version:** 1.1.0  
**Contact:** [GitHub Issues](https://github.com/yourusername/PerAspera-ModSDK/issues)
