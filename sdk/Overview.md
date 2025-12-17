# 🔧 PerAspera ModSDK - Complete API Reference

The **PerAspera ModSDK** is a high-level, event-driven framework that makes Per Aspera modding **10x simpler** than raw BepInEx IL2CPP development.

## 🎯 Why Use the ModSDK?

### Before SDK (Raw BepInEx)
```csharp
using BepInEx;
using HarmonyLib;
using Il2CppInterop.Runtime;

[BepInPlugin("mod.id", "My Mod", "1.0.0")]
public class MyMod : BasePlugin
{
    public override void Load()
    {
        // Manual Harmony patching
        var harmony = new Harmony("mod.id");
        harmony.PatchAll();
        
        // Manual Update loops (performance killer!)
        AddComponent<UpdateComponent>();
    }
}

[HarmonyPatch(typeof(SomeGameClass), "SomeMethod")]
public class Patch
{
    static void Prefix(SomeGameClass __instance)
    {
        // Complex IL2CPP type conversion
        var il2cppString = __instance.someField;
        string managedString = il2cppString?.ToString() ?? "";
        // Error-prone, verbose, fragile
    }
}
```

### After SDK (Modern Approach)
```csharp
using PerAspera.ModSDK;

[BepInPlugin("mod.id", "My Mod", "1.0.0")]
public class MyMod : PerAsperaSDKPlugin
{
    protected override void OnSDKReady()
    {
        // Clean event subscription
        SDK.Events.BuildingSpawned += (data) =>
        {
            Logger.LogInfo($"Building: {data.BuildingName}");
            // Type-safe, automatic conversion, zero boilerplate
        };
    }
}
```

**Result:** 90% less code, type-safe, maintainable, performant.

---

## 📦 SDK Architecture

### Package Structure

```
PerAspera.ModSDK (High-Level API - Use This!)
├── SDK.Events          → Event subscription system
├── SDK.Game            → Game state access (BaseGame, Universe, Planet)
├── SDK.Overrides       → Runtime value modification
└── SDK.Commands        → Game command integration

PerAspera.GameAPI.Events (Event Definitions)
├── SDK Events          → High-level mod events (DayPassed, GameLoaded)
├── Native Events       → Low-level game events (BuildingSpawned, ResourceChanged)
└── Event Data          → Typed event data structures

PerAspera.GameAPI.Wrappers (Type-Safe Game Access)
├── BaseGame           → Singleton game instance
├── Universe           → Factions, planets, global state
├── Planet             → Buildings, resources, climate
├── Building           → Individual building wrapper
├── Atmosphere         → Climate and terraforming data
└── Resource           → Resource type information

PerAspera.GameAPI (Low-Level Native Access)
├── EventBus           → Native event system
├── EventConstantIDs   → Event name constants
└── NativeWrappers     → Direct IL2CPP object access

PerAspera.Core (IL2CPP Utilities)
├── IL2CppExtensions   → Type conversion helpers
├── LogAspera          → Enhanced logging
└── BaseInitialization → SDK lifecycle management
```

---

## 🚀 Getting Started

### Installation

**Option 1: Direct Reference** (Recommended for development)
```xml
<ItemGroup>
  <Reference Include="PerAspera.ModSDK">
    <HintPath>..\..\SDK\bin\Debug\net6.0\PerAspera.ModSDK.dll</HintPath>
  </Reference>
</ItemGroup>
```

**Option 2: NuGet Package** (Coming soon)
```xml
<ItemGroup>
  <PackageReference Include="PerAspera.ModSDK" Version="1.1.0" />
</ItemGroup>
```

### Basic Plugin Template

```csharp
using BepInEx;
using BepInEx.Logging;
using PerAspera.ModSDK;

namespace MyMod;

[BepInPlugin(MyPluginInfo.PLUGIN_GUID, MyPluginInfo.PLUGIN_NAME, MyPluginInfo.PLUGIN_VERSION)]
public class MyModPlugin : PerAsperaSDKPlugin
{
    // Lifecycle: Called when SDK fully initialized
    protected override void OnSDKReady()
    {
        Logger.LogInfo($"{MyPluginInfo.PLUGIN_NAME} loaded!");
        
        // Subscribe to events here
        RegisterEvents();
    }
    
    private void RegisterEvents()
    {
        // Game lifecycle
        SDK.Events.GameFullyLoaded += OnGameLoaded;
        
        // Daily events
        SDK.Events.MartianDayChanged += OnDayChanged;
        
        // Building events
        SDK.Events.BuildingSpawned += OnBuildingSpawned;
    }
    
    private void OnGameLoaded()
    {
        Logger.LogInfo("Game fully loaded and ready!");
    }
    
    private void OnDayChanged(MartianDayEventData data)
    {
        Logger.LogInfo($"Day {data.TotalDays} - Year {data.Year}, Day {data.DayOfYear}");
    }
    
    private void OnBuildingSpawned(BuildingEventData data)
    {
        Logger.LogInfo($"New building: {data.BuildingName} at {data.Position}");
    }
    
    // Lifecycle: Called when mod unloads
    public override bool Unload()
    {
        // Cleanup: Unsubscribe events
        SDK.Events.GameFullyLoaded -= OnGameLoaded;
        SDK.Events.MartianDayChanged -= OnDayChanged;
        SDK.Events.BuildingSpawned -= OnBuildingSpawned;
        
        return base.Unload();
    }
}
```

---

## 📊 Event System

### Available Events

#### 🎮 Game Lifecycle Events

```csharp
// Fired when BaseGame instance detected (very early)
SDK.Events.BaseGameDetected += () => { };

// Fired when game fully loaded and ready for mod interaction
SDK.Events.GameFullyLoaded += () => { };

// Fired when planet initialized
SDK.Events.PlanetInitialized += () => { };

// Fired when blackboard (save data) initialized
SDK.Events.BlackboardInitialized += () => { };
```

#### ⏰ Time Events

```csharp
// Fired every Martian day
SDK.Events.MartianDayChanged += (MartianDayEventData data) =>
{
    int totalDays = data.TotalDays;
    int year = data.Year;
    int dayOfYear = data.DayOfYear;
    string season = data.Season;  // "Spring", "Summer", "Fall", "Winter"
};

// Legacy event (simple day count)
SDK.Events.DayPassed += (int days) => { };
```

#### 🏗️ Building Events

```csharp
// Building constructed
SDK.Events.BuildingSpawned += (BuildingEventData data) =>
{
    string name = data.BuildingName;
    Vector3 position = data.Position;
    Building building = data.Building;  // Type-safe wrapper
};

// Building removed/destroyed
SDK.Events.BuildingDespawned += (BuildingEventData data) => { };

// Building upgraded
SDK.Events.BuildingUpgraded += (BuildingEventData data) => { };

// Building scrapped for resources
SDK.Events.BuildingScrapped += (BuildingEventData data) => { };
```

#### 🌡️ Climate Events

```csharp
// Comprehensive climate analysis complete
SDK.Events.ClimateAnalysisComplete += (ClimateEventData data) =>
{
    float temperature = data.Temperature;      // Kelvin
    float pressure = data.Pressure;            // Pascals
    float oxygenLevel = data.OxygenLevel;      // Percentage
    float waterVapor = data.WaterVapor;        // Percentage
    
    // Check terraforming progress
    if (temperature > 273.15f)
    {
        Logger.LogInfo("Water can exist in liquid form!");
    }
};
```

#### 💎 Resource Events

```csharp
// Resource added to planet
SDK.Events.ResourceAdded += (resourceName, amount) =>
{
    Logger.LogInfo($"Added {amount} of {resourceName}");
};

// Native resource change event (more detailed)
SDK.Events.ResourceChanged += (NativeResourceEventData data) =>
{
    string resourceName = data.ResourceName;
    float newValue = data.NewValue;
    float oldValue = data.OldValue;
    float delta = newValue - oldValue;
};
```

#### 🎮 Player Events

```csharp
// Player faction detected
SDK.Events.PlayerFactionDetected += () =>
{
    Logger.LogInfo("Player faction initialized!");
};

// Player action executed (building placement, etc.)
SDK.Events.PlayerActionExecuted += (actionName) =>
{
    Logger.LogInfo($"Player action: {actionName}");
};
```

#### 🚁 Drone Events

```csharp
// Drone spawned
SDK.Events.DroneSpawned += (DroneEventData data) => { };

// Drone despawned
SDK.Events.DroneDespawned += (DroneEventData data) => { };
```

---

## 🎮 Game Access API

### SDK.Game - Current Game State

```csharp
// Access current planet
Planet planet = SDK.Game.CurrentPlanet;

if (planet != null)
{
    // Get all buildings
    IEnumerable<Building> buildings = planet.Buildings;
    
    // Count specific building type
    int solarPanels = planet.Buildings
        .Count(b => b.Name == "solarPanel");
    
    // Access atmosphere
    Atmosphere atmosphere = planet.Atmosphere;
    float temp = atmosphere.Temperature;
    float pressure = atmosphere.Pressure;
    
    // Get atmospheric composition
    var composition = planet.GetAtmosphereData();
    foreach (var gas in composition.Gases)
    {
        Logger.LogInfo($"{gas.Name}: {gas.Percentage:F2}%");
    }
}

// Access universe
Universe universe = SDK.Game.Universe;
if (universe != null)
{
    // Get player faction
    Faction playerFaction = universe.PlayerFaction;
    
    // Access all factions
    IEnumerable<Faction> factions = universe.Factions;
    
    // Get all planets
    IEnumerable<Planet> planets = universe.Planets;
}

// Access base game singleton
BaseGame baseGame = SDK.Game.BaseGameInstance;
```

---

## ⚙️ Override System

Modify game values at runtime without YAML edits or Harmony patches.

### Basic Override Usage

```csharp
// Override building energy production
Building solarPanel = /* get building */;
SDK.Overrides.SetOverride(
    solarPanel.InstanceId,
    "energyProduction",
    200.0f  // Double production
);

// Check if overridden
bool isOverridden = SDK.Overrides.HasOverride(
    solarPanel.InstanceId,
    "energyProduction"
);

// Get current value (respects overrides)
float currentProduction = SDK.Overrides.GetValue<float>(
    solarPanel.InstanceId,
    "energyProduction"
);

// Remove override (restore vanilla)
SDK.Overrides.RemoveOverride(
    solarPanel.InstanceId,
    "energyProduction"
);
```

### Advanced Overrides with Validation

```csharp
using PerAspera.GameAPI.Overrides.Validation;

// Override with range validation
SDK.Overrides.SetOverride(
    buildingId,
    "temperature",
    350.0f,
    new RangeValidator<float>(200f, 400f)  // Clamps to range
);

// Override boolean with validation
SDK.Overrides.SetOverride(
    systemId,
    "isActive",
    true,
    new BooleanValidator()
);

// Override with positive number validation
SDK.Overrides.SetOverride(
    resourceId,
    "stock",
    1000f,
    new PositiveValidator()  // Ensures >= 0
);
```

### Bulk Overrides

```csharp
// Override multiple buildings at once
var solarPanels = SDK.Game.CurrentPlanet.Buildings
    .Where(b => b.Name == "solarPanel");

foreach (var panel in solarPanels)
{
    SDK.Overrides.SetOverride(
        panel.InstanceId,
        "energyProduction",
        panel.BaseEnergyProduction * 1.5f
    );
}
```

---

## 🌡️ Climate API

Specialized helpers for climate and terraforming systems.

```csharp
using PerAspera.GameAPI.Climate;

// Get detailed atmosphere data
var planet = SDK.Game.CurrentPlanet;
var climate = planet.GetAtmosphereData();

Logger.LogInfo($"Temperature: {climate.Temperature}K");
Logger.LogInfo($"Pressure: {climate.Pressure} Pa");

// Analyze gas composition
foreach (var gas in climate.Gases)
{
    Logger.LogInfo($"{gas.Name}:");
    Logger.LogInfo($"  Amount: {gas.Amount}");
    Logger.LogInfo($"  Percentage: {gas.Percentage:F2}%");
    Logger.LogInfo($"  Mass: {gas.Mass}");
}

// Check terraforming effects
foreach (var effect in climate.TerraformingEffects)
{
    Logger.LogInfo($"Effect: {effect.Name}");
    Logger.LogInfo($"  Impact: {effect.ImpactValue}");
}
```

---

## 🎯 Best Practices

### ✅ Do's

1. **Always inherit from PerAsperaSDKPlugin**
```csharp
public class MyMod : PerAsperaSDKPlugin  // ✅ Correct
```

2. **Subscribe to events in OnSDKReady()**
```csharp
protected override void OnSDKReady()
{
    SDK.Events.GameFullyLoaded += OnGameLoaded;  // ✅ SDK initialized
}
```

3. **Always unsubscribe in Unload()**
```csharp
public override bool Unload()
{
    SDK.Events.GameFullyLoaded -= OnGameLoaded;  // ✅ Clean cleanup
    return base.Unload();
}
```

4. **Use null checks for game objects**
```csharp
var planet = SDK.Game.CurrentPlanet;
if (planet != null)  // ✅ Safe
{
    // Use planet
}
```

5. **Use type-safe wrappers**
```csharp
Building building = eventData.Building;  // ✅ Type-safe wrapper
float energy = building.EnergyProduction;  // ✅ IntelliSense support
```

### ❌ Don'ts

1. **Don't use Update() loops**
```csharp
void Update()  // ❌ Performance killer!
{
    CheckGameState();
}
```
Use events instead:
```csharp
SDK.Events.MartianDayChanged += (data) => CheckGameState();  // ✅
```

2. **Don't subscribe in Load()**
```csharp
public override void Load()
{
    SDK.Events.GameLoaded += Handler;  // ❌ SDK not ready!
}
```

3. **Don't access game objects before ready**
```csharp
protected override void OnSDKReady()
{
    var planet = SDK.Game.CurrentPlanet;  // ❌ Game might not be loaded!
    
    // ✅ Wait for game loaded event
    SDK.Events.GameFullyLoaded += () =>
    {
        var planet = SDK.Game.CurrentPlanet;  // ✅ Safe now
    };
}
```

4. **Don't use raw IL2CPP types**
```csharp
Il2CppSystem.String str = /* ... */;  // ❌ Manual conversion needed
string str = eventData.SomeString;     // ✅ SDK handles conversion
```

---

## 🔗 Related Documentation

- **[Event System Deep Dive](Events.md)** - Complete event documentation
- **[Game Wrappers Reference](Wrappers.md)** - All wrapper classes
- **[Override System Guide](Overrides.md)** - Runtime modification patterns
- **[Climate API Guide](Climate.md)** - Terraforming system details
- **[Commands API](Commands.md)** - Game command integration

---

## 📚 Example Mods

### Resource Production Tracker

```csharp
[BepInPlugin("com.example.tracker", "Resource Tracker", "1.0.0")]
public class ResourceTracker : PerAsperaSDKPlugin
{
    private Dictionary<string, float> _resourceStocks = new();
    
    protected override void OnSDKReady()
    {
        SDK.Events.ResourceChanged += OnResourceChanged;
    }
    
    private void OnResourceChanged(NativeResourceEventData data)
    {
        _resourceStocks[data.ResourceName] = data.NewValue;
        
        Logger.LogInfo($"📊 {data.ResourceName}: {data.NewValue:F0} " +
                      $"({(data.NewValue - data.OldValue):+0;-0})");
    }
}
```

### Terraforming Milestone Monitor

```csharp
[BepInPlugin("com.example.milestones", "Terraforming Milestones", "1.0.0")]
public class Milestones : PerAsperaSDKPlugin
{
    private bool _waterUnlocked = false;
    
    protected override void OnSDKReady()
    {
        SDK.Events.ClimateAnalysisComplete += CheckMilestones;
    }
    
    private void CheckMilestones(ClimateEventData data)
    {
        if (!_waterUnlocked && data.Temperature > 273.15f)
        {
            _waterUnlocked = true;
            Logger.LogInfo("🎉 MILESTONE: Water can now exist in liquid form!");
        }
    }
}
```

---

**Current Version:** 1.1.0  
**Last Updated:** December 17, 2025  
**Maintained by:** PerAspera Modding Community
