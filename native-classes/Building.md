# 🏗️ Building Class Reference

The `Building` wrapper provides type-safe access to Per Aspera's building system. All infrastructure, factories, and production facilities are represented as Building instances.

## 📖 Overview

```csharp
using PerAspera.GameAPI.Wrappers;

// Access via Planet wrapper
var planet = SDK.Game.CurrentPlanet;
var buildings = planet.Buildings;

// Find specific building type
var solarPanels = buildings.Where(b => b.TypeKey == "SolarPanel");

// Get building properties
foreach (var building in solarPanels)
{
    Logger.LogInfo($"Building #{building.Number}:");
    Logger.LogInfo($"  Position: {building.Position}");
    Logger.LogInfo($"  Powered: {building.IsPowered}");
    Logger.LogInfo($"  Health: {building.Health:F2}");
}
```

---

## 🔑 Core Identification

### Number
```csharp
public int Number { get; }
```

**Description:** Unique identifier for this building instance.

**Usage:**
```csharp
int buildingId = building.Number;
Logger.LogInfo($"Building ID: {buildingId}");

// Use for override system
SDK.Overrides.SetOverride(buildingId, "energyProduction", 200f);
```

**Important:** This number is unique per building instance, not per building type.

---

### BuildingType
```csharp
public object? BuildingType { get; }
```

**Description:** Reference to the building's type definition (from YAML).

**Usage:**
```csharp
var buildingType = building.BuildingType;
if (buildingType != null)
{
    string name = buildingType.GetFieldValue<string>("name");
    int index = buildingType.GetFieldValue<int>("index");
}
```

**Note:** Access underlying YAML definition properties.

---

### TypeKey
```csharp
public string TypeKey { get; }
```

**Description:** Building type identifier (e.g., "SolarPanel", "WaterExtractor").

**Usage:**
```csharp
string type = building.TypeKey;

// Filter buildings by type
var mines = planet.Buildings.Where(b => b.TypeKey == "Mine");

// Check building type
if (building.TypeKey == "SolarPanel")
{
    // Solar panel specific logic
}
```

**Common TypeKeys:**
- `"SolarPanel"` - Solar power generation
- `"WaterExtractor"` - Water extraction
- `"Mine"` - Resource mining
- `"Factory"` - Production facility
- `"Greenhouse"` - Food production
- `"Hab"` - Housing

---

### BuildingTypeName
```csharp
public string BuildingTypeName { get; }
```

**Description:** Human-readable building name.

**Usage:**
```csharp
string name = building.BuildingTypeName;
Logger.LogInfo($"Building: {name}");  // "Solar Panel", "Water Extractor", etc.
```

---

## 📍 Position & Location

### Position
```csharp
public (float x, float y) Position { get; }
```

**Description:** 2D coordinates on the planet surface.

**Usage:**
```csharp
var (x, y) = building.Position;
Logger.LogInfo($"Location: ({x}, {y})");

// Calculate distance between buildings
float Distance(Building a, Building b)
{
    var (x1, y1) = a.Position;
    var (x2, y2) = b.Position;
    float dx = x2 - x1;
    float dy = y2 - y1;
    return (float)Math.Sqrt(dx * dx + dy * dy);
}

// Find nearby buildings
var nearbyBuildings = planet.Buildings
    .Where(b => Distance(building, b) < 10.0f);
```

---

## 🔌 State Properties

### IsAlive
```csharp
public bool IsAlive { get; }
```

**Description:** Is the building still exists (not destroyed).

**Usage:**
```csharp
if (building.IsAlive)
{
    // Building exists, safe to interact
}
else
{
    // Building has been destroyed
}
```

---

### IsOperative
```csharp
public bool IsOperative { get; }
```

**Description:** Is the building currently operational.

**Usage:**
```csharp
if (building.IsOperative)
{
    Logger.LogInfo("Building is running");
}
else
{
    Logger.LogWarning("Building is offline");
}
```

**Note:** Operative means activated and functional (not paused or broken).

---

### IsBuilt
```csharp
public bool IsBuilt { get; }
```

**Description:** Is construction complete (not still being built).

**Usage:**
```csharp
if (building.IsBuilt)
{
    // Fully constructed, can produce
}
else
{
    // Still under construction
    Logger.LogInfo("Construction in progress...");
}
```

---

### IsPowered
```csharp
public bool IsPowered { get; }
```

**Description:** Is the building receiving power.

**Usage:**
```csharp
if (!building.IsPowered)
{
    Logger.LogWarning($"{building.BuildingTypeName} has no power!");
}
```

**Note:** Buildings without power cannot produce or function.

---

### IsBroken
```csharp
public bool IsBroken { get; }
```

**Description:** Is the building broken (needs repair).

**Usage:**
```csharp
if (building.IsBroken)
{
    Logger.LogWarning($"{building.BuildingTypeName} is broken!");
    // Trigger repair logic
}
```

---

### Health
```csharp
public float Health { get; }
```

**Description:** Current health as percentage (0.0 to 1.0).

**Usage:**
```csharp
float healthPercent = building.Health * 100f;
Logger.LogInfo($"Health: {healthPercent:F0}%");

// Check critical health
if (building.Health < 0.25f)
{
    Logger.LogWarning($"{building.BuildingTypeName} critically damaged!");
}
```

---

## 👥 Ownership

### GetFaction()
```csharp
public object? GetFaction()
```

**Description:** Get the faction that owns this building.

**Usage:**
```csharp
var faction = building.GetFaction();
if (faction != null)
{
    string factionName = faction.GetFieldValue<string>("name");
    Logger.LogInfo($"Owned by: {factionName}");
}
```

**Common factions:** Player, Rival AI factions

---

## 📦 Resources

### GetStockpile()
```csharp
public object? GetStockpile()
```

**Description:** Get the building's resource stockpile.

**Usage:**
```csharp
var stockpile = building.GetStockpile();
if (stockpile != null)
{
    // Access stockpile resources
    var resources = stockpile.InvokeMethod<object>("GetResources");
    // Process resources...
}
```

**Note:** Not all buildings have stockpiles (only production/storage buildings).

---

## 💡 Practical Examples

### Monitor Building Health

```csharp
SDK.Events.MartianDayChanged += (data) =>
{
    var damagedBuildings = SDK.Game.CurrentPlanet.Buildings
        .Where(b => b.Health < 0.5f && b.IsAlive);
    
    foreach (var building in damagedBuildings)
    {
        Logger.LogWarning($"⚠️ {building.BuildingTypeName} at ({building.Position.x:F0}, {building.Position.y:F0}) " +
                         $"health: {building.Health * 100:F0}%");
    }
};
```

### Count Buildings by Type

```csharp
var buildingCounts = SDK.Game.CurrentPlanet.Buildings
    .GroupBy(b => b.TypeKey)
    .ToDictionary(g => g.Key, g => g.Count());

foreach (var (type, count) in buildingCounts)
{
    Logger.LogInfo($"{type}: {count}");
}
```

### Find Unpowered Buildings

```csharp
var unpowered = SDK.Game.CurrentPlanet.Buildings
    .Where(b => b.IsBuilt && !b.IsPowered && b.IsAlive);

if (unpowered.Any())
{
    Logger.LogWarning($"⚡ {unpowered.Count()} buildings without power!");
    
    foreach (var building in unpowered.Take(5))  // Show first 5
    {
        Logger.LogInfo($"  - {building.BuildingTypeName} at {building.Position}");
    }
}
```

### Proximity-Based Bonuses

```csharp
SDK.Events.BuildingSpawned += (eventData) =>
{
    var newBuilding = eventData.Building;
    
    // Find nearby water extractors (within 5 tiles)
    var nearbyWater = SDK.Game.CurrentPlanet.Buildings
        .Where(b => b.TypeKey == "WaterExtractor")
        .Where(b => Distance(newBuilding, b) < 5.0f);
    
    if (nearbyWater.Any())
    {
        Logger.LogInfo($"💧 {newBuilding.BuildingTypeName} near water source!");
        // Apply production bonus via SDK.Overrides
    }
};

float Distance(Building a, Building b)
{
    var (x1, y1) = a.Position;
    var (x2, y2) = b.Position;
    float dx = x2 - x1;
    float dy = y2 - y1;
    return (float)Math.Sqrt(dx * dx + dy * dy);
}
```

### Building Health Monitor

```csharp
[BepInPlugin("com.example.healthmonitor", "Building Health Monitor", "1.0.0")]
public class HealthMonitor : PerAsperaSDKPlugin
{
    private Dictionary<int, float> _lastHealthCheck = new();
    
    protected override void OnSDKReady()
    {
        SDK.Events.MartianDayChanged += CheckBuildingHealth;
    }
    
    private void CheckBuildingHealth(MartianDayEventData data)
    {
        foreach (var building in SDK.Game.CurrentPlanet.Buildings)
        {
            if (!building.IsAlive) continue;
            
            // Track health changes
            if (_lastHealthCheck.TryGetValue(building.Number, out float lastHealth))
            {
                float damage = lastHealth - building.Health;
                if (damage > 0.1f)  // Significant damage
                {
                    Logger.LogWarning($"⚠️ {building.BuildingTypeName} took {damage * 100:F0}% damage!");
                }
            }
            
            _lastHealthCheck[building.Number] = building.Health;
            
            // Alert critical health
            if (building.Health < 0.25f && building.Health > 0)
            {
                Logger.LogError($"🚨 CRITICAL: {building.BuildingTypeName} at {building.Health * 100:F0}% health!");
            }
        }
    }
}
```

---

## ⚠️ Important Notes

### Performance Considerations

**Avoid frequent iterations:**
```csharp
❌ BAD - Every frame
void Update()
{
    foreach (var building in planet.Buildings) { }  // Expensive!
}

✅ GOOD - On events only
SDK.Events.MartianDayChanged += (data) =>
{
    foreach (var building in planet.Buildings) { }  // Once per day
};
```

### Null Safety

**Always check for null:**
```csharp
var planet = SDK.Game.CurrentPlanet;
if (planet == null)
{
    Logger.LogWarning("Planet not loaded yet");
    return;
}

foreach (var building in planet.Buildings)
{
    if (!building.IsAlive) continue;  // Skip destroyed buildings
    
    // Safe to use building
}
```

### Building State Changes

Buildings can change state dynamically:
- Construction completes → `IsBuilt` becomes true
- Power loss → `IsPowered` becomes false
- Damage → `Health` decreases
- Destruction → `IsAlive` becomes false

**Subscribe to events to track changes:**
```csharp
SDK.Events.BuildingSpawned += (data) => { /* New building */ };
SDK.Events.BuildingDespawned += (data) => { /* Building destroyed */ };
SDK.Events.BuildingUpgraded += (data) => { /* Building upgraded */ };
```

---

## 🔗 Related Classes

- **[Planet](Planet.md)** - Access all buildings on a planet
- **[Universe](Universe.md)** - Multi-planet building management
- **[BaseGame](BaseGame.md)** - Global game instance

---

## 📚 Related Documentation

- **[Building Events](../sdk/Events.md#building-events)** - Building-related events
- **[Override System](../sdk/Overrides.md)** - Modify building properties at runtime
- **[Buildings Tutorial](../tutorials/Buildings.md)** - Practical building modification guide

---

**Last Updated:** December 17, 2025  
**SDK Version:** 1.1.0
