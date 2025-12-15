# SDK API Reference

🔗 **Complete reference for the PerAspera SDK - Your gateway to Mars terraforming through code!**

## 🌍 International Documentation

This reference is available in multiple languages:
- **English** (this page)
- **Français** ([Référence-API-SDK](Reference-API-SDK-FR))
- **Deutsch** ([SDK-API-Referenz](SDK-API-Referenz-DE))

## 📚 Table of Contents

- [ModSDK API](#modsdk-api) - Main entry point
- [Universe API](#universe-api) - Planet and time access
- [Climate System API](#climate-system-api) - Atmospheric simulation
- [Building System API](#building-system-api) - Infrastructure management
- [Event System API](#event-system-api) - Event handling
- [Override System API](#override-system-api) - Value modification
- [Utility APIs](#utility-apis) - Helper functions
- [Data Structures](#data-structures) - Core data types

---

## ModSDK API

🎯 **The main entry point for all SDK functionality. Always initialize this first.**

### Core Methods

#### `ModSDK.Initialize(BasePlugin plugin)`

Initializes the entire SDK system with your plugin instance.

**Parameters:**
- `plugin` *(BasePlugin)*: Your BepInEx plugin instance

**Throws:**
- `ModSDKException`: If initialization fails
- `InvalidOperationException`: If already initialized

**Example:**
```csharp
public override void Load()
{
    ModSDK.Initialize(this);  // Pass your plugin instance
    Log.LogInfo("SDK initialized successfully!");
}
```

**Best Practices:**
- ✅ Always call this first in your `Load()` method
- ✅ Wrap in try-catch for error handling
- ✅ Check `ModSDK.IsInitialized` before using other APIs

---

## Universe API

🌌 **Access to universe-wide game data, time systems, and planetary information.**

### Time & Sol Management

#### `ModSDK.Universe.GetCurrentSol() -> int`

Gets the current Martian sol (day) number.

**Returns:** Current sol as integer (starts from 1)
**Thread-Safe:** Yes

**Example:**
```csharp
int currentSol = ModSDK.Universe.GetCurrentSol();
Log.LogInfo($"Today is Sol {currentSol} on Mars");

// Use in time-based logic
if (currentSol % 10 == 0)
{
    Log.LogInfo($"🎉 Sol {currentSol} milestone reached!");
}
```

#### `ModSDK.Universe.GetGameTime() -> DateTime`

Gets the current in-game time as DateTime.

**Example:**
```csharp
var gameTime = ModSDK.Universe.GetGameTime();
Log.LogInfo($"Game time: {gameTime:yyyy-MM-dd HH:mm:ss}");
```

### Climate Data Access

#### `ModSDK.Universe.GetClimateData() -> PlanetClimateData?`

Gets comprehensive climate data for the current planet.

**Returns:** Climate data object or null if not available
**Thread-Safe:** Yes
**Performance:** Cached for 1 second to avoid excessive calls

**Example:**
```csharp
var climate = ModSDK.Universe.GetClimateData();
if (climate != null)
{
    Log.LogInfo($"🌡️ Temperature: {climate.Temperature:F1}K");
    Log.LogInfo($"🌫️ Pressure: {climate.TotalPressure:F3} atm");
    Log.LogInfo($"💧 Water: {climate.WaterStock:F1}");
    
    // Check for habitability
    if (climate.IsHabitable)
    {
        Log.LogInfo("🎉 Mars is now habitable!");
    }
}
```

### Game State Checks

#### `ModSDK.Universe.IsGameReady() -> bool`

Checks if the game is fully loaded and systems are available.

**Returns:** True if game ready, false otherwise
**Use Cases:** Initialization checks, feature activation

**Example:**
```csharp
if (ModSDK.Universe.IsGameReady())
{
    StartAdvancedMonitoring();
}
else
{
    Log.LogInfo("⏳ Waiting for game systems to initialize...");
    // Subscribe to game.loaded event instead
}
```

### Planet Access

#### `ModSDK.Universe.GetPlanet() -> object?`

Gets the raw planet instance (advanced usage).

⚠️ **Warning:** Direct game object access - use with caution

**Example:**
```csharp
var planet = ModSDK.Universe.GetPlanet();
if (planet != null)
{
    // Advanced operations with raw game data
    // Requires knowledge of game internals
}
```

#### `ModSDK.Universe.GetPlanetMirror() -> MirrorPlanet?`

Gets the planet wrapped in a safe Mirror interface.

**Returns:** Mirror-wrapped planet or null
**Recommended:** Use this instead of raw planet access

**Example:**
```csharp
var planet = ModSDK.Universe.GetPlanetMirror();
if (planet != null)
{
    var temperature = planet.GetTemperature();
    var buildings = planet.GetBuildingCount("SolarArray");
    Log.LogInfo($"Planet has {buildings} solar arrays at {temperature}K");
}
```

---

## Event System API

📡 **Unified event system for subscribing to game events and creating custom events.**

### Event Subscription

#### `ModSDK.Events.Subscribe(string eventName, Action<object> handler)`

Subscribe to a named event with a handler function.

**Parameters:**
- `eventName` *(string)*: Name of event to subscribe to
- `handler` *(Action<object>)*: Function to call when event occurs

**Thread-Safe:** Yes
**Auto-Cleanup:** Events are automatically unsubscribed when mod unloads

**Example:**
```csharp
// Subscribe to climate events
ModSDK.Events.Subscribe("climate.temperatureChanged", OnTemperatureChanged);
ModSDK.Events.Subscribe("building.constructed", OnBuildingBuilt);

private void OnTemperatureChanged(object eventData)
{
    var data = eventData as ClimateEventData;
    if (data != null)
    {
        Log.LogInfo($"🌡️ Temperature: {data.CurrentValue}K (Δ{data.Delta:+0.0;-0.0})");
    }
}

private void OnBuildingBuilt(object eventData)
{
    var data = eventData as BuildingEventData;
    Log.LogInfo($"🏗️ New {data.BuildingType} constructed at {data.Position}");
}
```

### Event Management

#### `ModSDK.Events.Unsubscribe(string eventName, Action<object> handler)`

Remove a specific event handler.

#### `ModSDK.Events.UnsubscribeAll()`

Remove all event subscriptions for this mod.

**Example:**
```csharp
public void OnDestroy()
{
    ModSDK.Events.UnsubscribeAll();  // Cleanup on mod shutdown
}
```

### Available Events

#### Climate Events

| Event Name | Data Type | Description |
|------------|-----------|-------------|
| `"climate.temperatureChanged"` | `ClimateEventData` | Mars temperature changed |
| `"climate.atmosphereChanged"` | `ClimateEventData` | Atmospheric composition changed |
| `"climate.waterStockChanged"` | `ClimateEventData` | Water resources changed |
| `"climate.pressureChanged"` | `ClimateEventData` | Atmospheric pressure changed |

#### Building Events

| Event Name | Data Type | Description |
|------------|-----------|-------------|
| `"building.constructed"` | `BuildingEventData` | Building construction completed |
| `"building.destroyed"` | `BuildingEventData` | Building was destroyed |
| `"building.productionComplete"` | `ProductionEventData` | Production cycle finished |

#### Game State Events

| Event Name | Data Type | Description |
|------------|-----------|-------------|
| `"game.loaded"` | `null` | Game finished loading |
| `"planet.initialized"` | `PlanetData` | Planet systems ready |
| `"game.dayPassed"` | `SolData` | New Martian sol started |
| `"game.paused"` | `null` | Game was paused |
| `"game.resumed"` | `null` | Game was resumed |

---

## Climate System API

🌡️ **Advanced atmospheric simulation and climate modeling for terraforming mods.**

### Climate Simulation

#### `ClimateSimulation.GetCurrentStatus() -> ClimateStatus`

Gets comprehensive current climate status.

**Example:**
```csharp
var status = ClimateSimulation.GetCurrentStatus();
Log.LogInfo($"🌡️ Temperature: {status.Temperature:F1}K");
Log.LogInfo($"🌫️ Pressure: {status.AtmosphericPressure:F3} atm");
Log.LogInfo($"🫁 Breathability: {status.BreathabilityIndex:F1}%");
Log.LogInfo($"🏠 Habitability: {(status.IsHabitable ? "YES" : "NO")}");
```

#### `ClimateSimulation.PredictClimate(int solsInFuture) -> ClimateStatus`

Predicts climate state after specified sols.

**Use Cases:** Planning, warnings, optimization

**Example:**
```csharp
// Predict climate in 50 sols
var prediction = ClimateSimulation.PredictClimate(50);
Log.LogInfo($"🔮 Predicted in 50 sols:");
Log.LogInfo($"   Temperature: {prediction.Temperature:F1}K");
Log.LogInfo($"   Breathability: {prediction.BreathabilityIndex:F1}%");

// Warn if terraforming is stalling
if (prediction.BreathabilityIndex <= status.BreathabilityIndex)
{
    Log.LogWarning("⚠️ Terraforming progress may be stalling!");
}
```

### Atmospheric Composition

#### `AtmosphericComposition.RegisterGas(string symbol, string name, float concentration, bool isGreenhouseGas)`

Register a new atmospheric gas.

**Example:**
```csharp
var atmosphere = ClimateSimulation.Atmosphere;

// Add methane for enhanced greenhouse effect
atmosphere.RegisterGas("CH4", "Methane", 0.001f, true);

// Add noble gases for atmospheric density
atmosphere.RegisterGas("Ar", "Argon", 0.009f, false);
```

#### `AtmosphericComposition.SetGasConcentration(string symbol, float concentration)`

Set concentration of existing gas.

**Example:**
```csharp
// Boost oxygen for breathability
atmosphere.SetGasConcentration("O2", 0.18f);  // 18% oxygen

// Reduce CO2
atmosphere.SetGasConcentration("CO2", 0.75f);  // 75% CO2 (down from 96%)
```

### Climate Analysis

#### `AtmosphericComposition.CalculateBreathabilityIndex() -> float`

Calculate how breathable atmosphere is (0-100%).

**Algorithm:**
- Oxygen concentration (>16% needed)
- Total pressure (>0.1 atm needed)  
- Toxic gas levels (CO2 <4%, others)
- Temperature range (-10°C to +50°C optimal)

**Example:**
```csharp
float breathability = atmosphere.CalculateBreathabilityIndex();

if (breathability >= 80f)
    Log.LogInfo("🫁 Atmosphere is highly breathable!");
else if (breathability >= 50f)
    Log.LogInfo($"🟡 Atmosphere is {breathability:F1}% breathable (getting there!)");
else
    Log.LogWarning($"🔴 Atmosphere only {breathability:F1}% breathable (more work needed)");
```

---

## Building System API

🏗️ **Infrastructure monitoring and interaction for construction-focused mods.**

### Building Information

#### `BuildingSystem.GetAllBuildings() -> BuildingInfo[]`

Gets information about all buildings on planet.

**Example:**
```csharp
var buildings = BuildingSystem.GetAllBuildings();
Log.LogInfo($"🏗️ Total buildings on Mars: {buildings.Length}");

// Categorize buildings
var powerBuildings = buildings.Where(b => b.Category == "Power").ToArray();
var habitats = buildings.Where(b => b.Category == "Habitat").ToArray();

Log.LogInfo($"   Power facilities: {powerBuildings.Length}");
Log.LogInfo($"   Habitats: {habitats.Length}");
```

#### `BuildingSystem.GetBuildingsByType(string buildingType) -> BuildingInfo[]`

Gets all buildings of specific type.

**Common Building Types:**
- `"SolarArray"` - Solar power generation
- `"ResidentialDome"` - Housing for colonists
- `"AtmosphereProcessor"` - Atmospheric modification
- `"WaterExtractor"` - Water resource extraction
- `"OxygenGenerator"` - Oxygen production

**Example:**
```csharp
var solarPanels = BuildingSystem.GetBuildingsByType("SolarArray");
var totalPowerCapacity = 0f;

foreach (var panel in solarPanels)
{
    var production = BuildingSystem.GetBuildingProduction(panel.RawBuilding);
    totalPowerCapacity += production.OutputRate;
}

Log.LogInfo($"⚡ Total solar capacity: {totalPowerCapacity:F1} MW");
```

### Production Monitoring

#### `BuildingSystem.GetBuildingProduction(object building) -> ProductionData`

Gets production information for a building.

**Example:**
```csharp
var waterExtractors = BuildingSystem.GetBuildingsByType("WaterExtractor");
float totalWaterProduction = 0f;

foreach (var extractor in waterExtractors)
{
    var production = BuildingSystem.GetBuildingProduction(extractor.RawBuilding);
    totalWaterProduction += production.OutputRate * production.Efficiency;
}

Log.LogInfo($"💧 Water production: {totalWaterProduction:F2} units/sol");
```

### Atmospheric Impact

#### `BuildingSystem.GetAtmosphericImpact(object building) -> AtmosphereImpactData`

Gets how building affects planetary atmosphere.

**Example:**
```csharp
var processors = BuildingSystem.GetBuildingsByType("AtmosphereProcessor");
float totalCO2Reduction = 0f;
float totalO2Production = 0f;

foreach (var processor in processors)
{
    var impact = BuildingSystem.GetAtmosphericImpact(processor.RawBuilding);
    totalCO2Reduction += Math.Abs(impact.CO2Change);
    totalO2Production += impact.O2Change;
}

Log.LogInfo($"🌫️ Atmospheric processing:");
Log.LogInfo($"   CO2 reduction: {totalCO2Reduction:F4} atm/sol");
Log.LogInfo($"   O2 production: {totalO2Production:F4} atm/sol");
```

---

## Override System API

⚙️ **Dynamic runtime modification of game values for balance and feature mods.**

### Value Overrides

#### `OverrideSystem.RegisterOverride(string key, float value, string description)`

Register a new value override.

**Key Naming Convention:**
- Format: `category.subcategory.property`
- Examples: `"buildings.dome.efficiency"`, `"climate.greenhouse.multiplier"`

**Example:**
```csharp
// Increase building efficiency
OverrideSystem.RegisterOverride(
    "buildings.solar.efficiency", 
    1.25f, 
    "Solar panels produce 25% more power"
);

// Enhance greenhouse effect for faster terraforming
OverrideSystem.RegisterOverride(
    "climate.greenhouse.multiplier", 
    1.5f, 
    "Enhanced greenhouse warming"
);

// Reduce resource consumption
OverrideSystem.RegisterOverride(
    "buildings.maintenance.cost", 
    0.75f, 
    "Buildings require 25% less maintenance"
);
```

#### `OverrideSystem.UpdateOverride(string key, float newValue)`

Update existing override value.

**Example:**
```csharp
// Adjust override based on game progression
int currentSol = ModSDK.Universe.GetCurrentSol();
if (currentSol > 100)
{
    // Reduce bonus as game progresses
    OverrideSystem.UpdateOverride("buildings.solar.efficiency", 1.1f);
    Log.LogInfo("🔧 Solar efficiency bonus reduced for late game balance");
}
```

### Override Management

#### `OverrideSystem.GetOverride(string key) -> GetterOverride?`

Get information about an existing override.

**Example:**
```csharp
var solarOverride = OverrideSystem.GetOverride("buildings.solar.efficiency");
if (solarOverride != null)
{
    Log.LogInfo($"⚡ Solar Override:");
    Log.LogInfo($"   Current: {solarOverride.CurrentValue}x");
    Log.LogInfo($"   Default: {solarOverride.DefaultValue}x");
    Log.LogInfo($"   Effect: {((solarOverride.CurrentValue - 1) * 100):+0;-0}%");
}
```

#### `OverrideSystem.RemoveOverride(string key)`

Remove an override (restore original value).

---

## Utility APIs

🛠️ **Helper functions and utilities for common modding tasks.**

### Safe Game Access

#### `MirrorHelper.SafeAccess<T>(object target, Func<object, T> accessor, T defaultValue)`

Safely access game object properties with fallback.

**Example:**
```csharp
var planet = ModSDK.Universe.GetPlanet();
var temperature = MirrorHelper.SafeAccess(
    planet,
    p => ((PlanetInstance)p).Temperature,
    210.0f  // Default temperature if access fails
);

Log.LogInfo($"🌡️ Temperature: {temperature}K");
```

### Game Instance Detection

#### `GameInstanceDetector.FindPerAsperaInstallation() -> string?`

Automatically locate Per Aspera installation directory.

**Example:**
```csharp
var gamePath = GameInstanceDetector.FindPerAsperaInstallation();
if (gamePath != null)
{
    Log.LogInfo($"🎮 Found Per Aspera at: {gamePath}");
    
    var isValid = GameInstanceDetector.ValidateInstallation(gamePath);
    Log.LogInfo($"✅ Installation valid: {isValid}");
}
```

---

## Data Structures

📊 **Core data types used throughout the SDK.**

### ClimateEventData

Climate change event information.

```csharp
public class ClimateEventData
{
    public string EventType { get; set; }        // "temperature", "pressure", etc.
    public float CurrentValue { get; set; }      // New value
    public float PreviousValue { get; set; }     // Previous value  
    public float Delta { get; set; }             // Change amount
    public int MartianSol { get; set; }          // Sol when change occurred
    public DateTime Timestamp { get; set; }      // Real time when detected
    public object RawGameEvent { get; set; }     // Original game event
}
```

### PlanetClimateData

Complete planet climate information.

```csharp
public class PlanetClimateData
{
    public float Temperature { get; set; }        // Temperature in Kelvin
    public float CO2Pressure { get; set; }        // CO2 partial pressure (atm)
    public float O2Pressure { get; set; }         // O2 partial pressure (atm)
    public float N2Pressure { get; set; }         // N2 partial pressure (atm)
    public float GHGPressure { get; set; }        // Greenhouse gas pressure (atm)
    public float WaterStock { get; set; }         // Available water resources
    public float TotalPressure { get; set; }      // Total atmospheric pressure
    public bool IsHabitable { get; set; }         // Game's habitability determination
    public string PlanetName { get; set; }        // Planet name ("Mars")
    public DateTime Timestamp { get; set; }       // When data was captured
}
```

### BuildingInfo

Information about a single building.

```csharp
public class BuildingInfo
{
    public string Id { get; set; }               // Unique building identifier
    public string Type { get; set; }             // Building type name
    public string Category { get; set; }         // Building category
    public Vector3 Position { get; set; }        // 3D position on planet
    public bool IsOperational { get; set; }      // Whether building is active
    public float PowerConsumption { get; set; }  // Current power usage
    public float Efficiency { get; set; }        // Operating efficiency (0-1)
    public object RawBuilding { get; set; }      // Direct game building object
}
```

### ProductionData

Building production information.

```csharp
public class ProductionData
{
    public string ResourceType { get; set; }     // Type of resource produced
    public float OutputRate { get; set; }        // Base production rate
    public float Efficiency { get; set; }        // Current efficiency multiplier
    public float ActualOutput { get; set; }      // Actual production (rate × efficiency)
    public bool IsProducing { get; set; }        // Whether currently producing
}
```

---

## Error Handling

### Exception Types

#### `ModSDKException`

Base exception for all SDK-related errors.

```csharp
try
{
    ModSDK.Initialize(this);
}
catch (ModSDKException ex)
{
    Log.LogError($"SDK Error: {ex.Message}");
    // Handle gracefully - maybe retry or disable features
}
```

#### `GameAPIException`

Specific to game API access errors.

#### `ClimateSystemException`

Specific to climate system errors.

---

## Performance Tips

### Efficient Event Handling

```csharp
// ✅ Good: Use specific event types
ModSDK.Events.Subscribe("climate.temperatureChanged", OnTemperatureChanged);

// ❌ Avoid: Generic event handlers that check every event
ModSDK.Events.Subscribe("*", OnAnyEvent);  // Don't do this
```

### Caching Game Data

```csharp
// ✅ Good: Cache climate data for short periods
private PlanetClimateData _cachedClimateData;
private DateTime _cacheTime;

public PlanetClimateData GetCachedClimateData()
{
    if (_cachedClimateData == null || (DateTime.Now - _cacheTime).TotalSeconds > 1.0)
    {
        _cachedClimateData = ModSDK.Universe.GetClimateData();
        _cacheTime = DateTime.Now;
    }
    return _cachedClimateData;
}
```

### Efficient Override Updates

```csharp
// ✅ Good: Batch override updates
OverrideSystem.BeginBatchUpdate();
try
{
    OverrideSystem.UpdateOverride("buildings.solar.efficiency", 1.2f);
    OverrideSystem.UpdateOverride("buildings.dome.capacity", 1.5f);
    OverrideSystem.UpdateOverride("climate.greenhouse.effect", 1.3f);
}
finally
{
    OverrideSystem.EndBatchUpdate();
}
```

---

## 🌍 Community Resources

### Getting Help

- **[Discord Community](https://discord.gg/peraspera-modding)** - Real-time help in multiple languages
- **[GitHub Discussions](https://github.com/PerAsperaMods/PerAspera-SDK/discussions)** - Q&A and showcases
- **[Stack Overflow](https://stackoverflow.com/tags/per-aspera-modding)** - Programming questions

### Example Projects

- **[Climate Monitor Mod](Code-Examples#climate-monitor)** - Complete working example
- **[Building Enhancement Pack](Sample-Projects#building-enhancements)** - Advanced building modifications
- **[Terraforming Accelerator](Sample-Projects#terraforming-accelerator)** - Climate manipulation

### Contributing

- **[SDK Development](https://github.com/PerAsperaMods/PerAspera-SDK)** - Contribute to the framework
- **[Documentation Translation](Translation-Project)** - Help translate this reference
- **[Community Examples](Community-Creations)** - Share your code

---

**Ready to terraform Mars with code?** 🚀 Start with our [First Mod Tutorial](First-Mod-Tutorial) and join the international modding community!