# 🌍 Planet Class Reference

The `Planet` wrapper provides type-safe access to planetary properties, resources, climate systems, and buildings. This is the main interface for interacting with Mars terraforming systems.

## 📖 Overview

```csharp
using PerAspera.GameAPI.Wrappers;

// Access current planet
var planet = SDK.Game.CurrentPlanet;

// Or get directly
Planet planet = Planet.GetCurrent();

if (planet != null)
{
    Logger.LogInfo($"Water stock: {planet.WaterStock}");
    Logger.LogInfo($"Temperature: {planet.Atmosphere.Temperature}K");
    Logger.LogInfo($"Buildings: {planet.Buildings.Count()}");
}
```

---

## 🌡️ Atmosphere Access

### Atmosphere
```csharp
public Atmosphere Atmosphere { get; }
```

**Description:** Access to complete atmospheric system (temperature, pressure, composition).

**Usage:**
```csharp
var atmosphere = planet.Atmosphere;

// Temperature and pressure
float tempK = atmosphere.Temperature;
float tempC = tempK - 273.15f;
float pressure = atmosphere.Pressure;

// Gas composition
var co2 = atmosphere.Composition["CO2"];
Logger.LogInfo($"CO2: {co2.Percentage:F1}%");

// Check terraforming progress
if (tempC > 0)
{
    Logger.LogInfo("Water can exist in liquid form!");
}
```

**See:** [Atmosphere Class](Atmosphere.md) for complete climate API.

---

## 💧 Resource Properties

### WaterStock
```csharp
public float WaterStock { get; set; }
```

**Description:** Total water reserves on the planet.

**Usage:**
```csharp
// Get water
float water = planet.WaterStock;

// Add water
planet.WaterStock += 1000f;

// Check sufficiency
if (planet.WaterStock < 100f)
{
    Logger.LogWarning("Low water reserves!");
}
```

**Note:** Value in game units (typically kilotonnes).

---

### SiliconStock
```csharp
public float SiliconStock { get; }
```

**Description:** Silicon ore reserves (read-only property).

**Usage:**
```csharp
float silicon = planet.SiliconStock;
Logger.LogInfo($"Silicon reserves: {silicon:F0}");
```

---

### IronStock
```csharp
public float IronStock { get; }
```

**Description:** Iron ore reserves (read-only property).

**Usage:**
```csharp
float iron = planet.IronStock;
Logger.LogInfo($"Iron reserves: {iron:F0}");
```

---

### CarbonStock
```csharp
public float CarbonStock { get; }
```

**Description:** Carbon reserves (read-only property).

**Usage:**
```csharp
float carbon = planet.CarbonStock;
Logger.LogInfo($"Carbon reserves: {carbon:F0}");
```

---

### CalciteStock
```csharp
public float CalciteStock { get; }
```

**Description:** Calcite (limestone) reserves (read-only property).

**Usage:**
```csharp
float calcite = planet.CalciteStock;
Logger.LogInfo($"Calcite reserves: {calcite:F0}");
```

---

## ✅ Resource Status Checks

### HasSufficientWater
```csharp
public bool HasSufficientWater { get; }
```

**Description:** Quick check if planet has adequate water (> 1000 units).

**Usage:**
```csharp
if (planet.HasSufficientWater)
{
    Logger.LogInfo("Water reserves adequate");
}
else
{
    Logger.LogWarning("Critical water shortage!");
}
```

---

### HasBalancedResources
```csharp
public bool HasBalancedResources { get; }
```

**Description:** Checks if all major resources are at healthy levels.

**Criteria:**
- Water > 500
- Silicon > 100
- Iron > 100  
- Carbon > 50

**Usage:**
```csharp
if (!planet.HasBalancedResources)
{
    Logger.LogWarning("Resource imbalance detected!");
    
    // Check individual resources
    if (planet.WaterStock < 500f)
        Logger.LogWarning("  - Low water");
    if (planet.SiliconStock < 100f)
        Logger.LogWarning("  - Low silicon");
}
```

---

## 🏗️ Building Access

### Buildings
```csharp
public IEnumerable<Building> Buildings { get; }
```

**Description:** Access all buildings on the planet.

**Usage:**
```csharp
// Count all buildings
int totalBuildings = planet.Buildings.Count();

// Filter by type
var solarPanels = planet.Buildings
    .Where(b => b.TypeKey == "SolarPanel");

// Check operational buildings
var operational = planet.Buildings
    .Where(b => b.IsOperative && b.IsPowered);

Logger.LogInfo($"Operational: {operational.Count()}/{totalBuildings}");
```

**See:** [Building Class](Building.md) for building properties.

---

## 📦 Resource Methods

### GetResourceStock(resourceKey)
```csharp
public float GetResourceStock(string resourceKey)
```

**Description:** Get stock of any resource by its key identifier.

**Parameters:**
- `resourceKey` - Resource identifier (e.g., "resource_water", "resource_silicon")

**Returns:** Current stock amount, or 0 if resource not found.

**Usage:**
```csharp
float water = planet.GetResourceStock("resource_water");
float fuel = planet.GetResourceStock("resource_fuel");
float food = planet.GetResourceStock("resource_food");

Logger.LogInfo($"Resources:");
Logger.LogInfo($"  Water: {water:F0}");
Logger.LogInfo($"  Fuel: {fuel:F0}");
Logger.LogInfo($"  Food: {food:F0}");
```

**Common Resource Keys:**
- `"resource_water"` - Water
- `"resource_silicon"` - Silicon ore
- `"resource_iron"` - Iron ore
- `"resource_carbon"` - Carbon
- `"resource_calcite"` - Calcite/limestone
- `"resource_fuel"` - Rocket fuel
- `"resource_steel"` - Steel
- `"resource_electronics"` - Electronics
- `"resource_food"` - Food

---

### AddResource(resourceKey, amount)
```csharp
public void AddResource(string resourceKey, float amount)
```

**Description:** Add resources to planet stock.

**Parameters:**
- `resourceKey` - Resource identifier
- `amount` - Quantity to add (can be negative to subtract)

**Usage:**
```csharp
// Add resources
planet.AddResource("resource_water", 1000f);
planet.AddResource("resource_steel", 500f);

// Remove resources
planet.AddResource("resource_fuel", -100f);

Logger.LogInfo($"New water stock: {planet.WaterStock}");
```

**Note:** Validates resource exists before adding.

---

## 🌊 Climate Data Access

### GetAtmosphereData()
```csharp
public AtmosphericComposition GetAtmosphereData()
```

**Description:** Get detailed atmospheric composition analysis.

**Returns:** Complete atmospheric data with gas percentages.

**Usage:**
```csharp
var atmosphere = planet.GetAtmosphereData();

Logger.LogInfo("Atmospheric Composition:");
foreach (var gas in atmosphere.AllGases)
{
    Logger.LogInfo($"  {gas.Name} ({gas.Symbol}): {gas.Percentage:F2}%");
    Logger.LogInfo($"    Partial Pressure: {gas.PartialPressure:F2} kPa");
}
```

**See:** [Atmosphere Class](Atmosphere.md) for detailed API.

---

## 💡 Practical Examples

### Resource Monitoring System

```csharp
[BepInPlugin("com.example.resources", "Resource Monitor", "1.0.0")]
public class ResourceMonitor : PerAsperaSDKPlugin
{
    private Dictionary<string, float> _resourceThresholds = new()
    {
        ["resource_water"] = 500f,
        ["resource_fuel"] = 100f,
        ["resource_food"] = 200f,
        ["resource_steel"] = 150f
    };
    
    protected override void OnSDKReady()
    {
        SDK.Events.MartianDayChanged += CheckResources;
    }
    
    private void CheckResources(MartianDayEventData data)
    {
        var planet = SDK.Game.CurrentPlanet;
        if (planet == null) return;
        
        foreach (var (resource, threshold) in _resourceThresholds)
        {
            float stock = planet.GetResourceStock(resource);
            
            if (stock < threshold)
            {
                Logger.LogWarning($"⚠️ Low {resource}: {stock:F0} (threshold: {threshold:F0})");
            }
        }
    }
}
```

### Building Efficiency Tracker

```csharp
SDK.Events.MartianDayChanged += (data) =>
{
    var planet = SDK.Game.CurrentPlanet;
    if (planet == null) return;
    
    var buildings = planet.Buildings;
    int total = buildings.Count();
    int operative = buildings.Count(b => b.IsOperative);
    int powered = buildings.Count(b => b.IsPowered);
    int healthy = buildings.Count(b => b.Health > 0.75f);
    
    Logger.LogInfo($"Building Status:");
    Logger.LogInfo($"  Total: {total}");
    Logger.LogInfo($"  Operative: {operative} ({operative * 100f / total:F0}%)");
    Logger.LogInfo($"  Powered: {powered} ({powered * 100f / total:F0}%)");
    Logger.LogInfo($"  Healthy: {healthy} ({healthy * 100f / total:F0}%)");
};
```

### Terraforming Progress Dashboard

```csharp
SDK.Events.ClimateAnalysisComplete += (data) =>
{
    var planet = SDK.Game.CurrentPlanet;
    if (planet == null) return;
    
    float tempC = data.Temperature - 273.15f;
    float pressurePa = data.Pressure;
    
    Logger.LogInfo("═══════════════════════════════");
    Logger.LogInfo("   TERRAFORMING PROGRESS");
    Logger.LogInfo("═══════════════════════════════");
    
    // Climate
    Logger.LogInfo($"🌡️ Temperature: {tempC:F1}°C");
    Logger.LogInfo($"🌫️ Pressure: {pressurePa:F0} Pa");
    Logger.LogInfo($"💨 Oxygen: {data.OxygenLevel:F1}%");
    
    // Resources
    Logger.LogInfo($"💧 Water: {planet.WaterStock:F0}");
    Logger.LogInfo($"⚙️ Iron: {planet.IronStock:F0}");
    Logger.LogInfo($"🔩 Silicon: {planet.SiliconStock:F0}");
    
    // Infrastructure
    Logger.LogInfo($"🏗️ Buildings: {planet.Buildings.Count()}");
    
    // Progress checks
    List<string> milestones = new();
    if (tempC > 0) milestones.Add("✅ Liquid water possible");
    if (pressurePa > 50000) milestones.Add("✅ Pressure increasing");
    if (data.OxygenLevel > 5) milestones.Add("✅ Oxygen detected");
    
    if (milestones.Any())
    {
        Logger.LogInfo("Milestones:");
        foreach (var milestone in milestones)
        {
            Logger.LogInfo($"  {milestone}");
        }
    }
    
    Logger.LogInfo("═══════════════════════════════");
};
```

### Dynamic Resource Balancer

```csharp
[BepInPlugin("com.example.balancer", "Resource Balancer", "1.0.0")]
public class ResourceBalancer : PerAsperaSDKPlugin
{
    protected override void OnSDKReady()
    {
        SDK.Events.MartianDayChanged += BalanceResources;
    }
    
    private void BalanceResources(MartianDayEventData data)
    {
        var planet = SDK.Game.CurrentPlanet;
        if (planet == null) return;
        
        // Check if balancing needed
        if (!planet.HasBalancedResources)
        {
            Logger.LogInfo("🔧 Balancing resources...");
            
            // Boost low resources
            if (planet.WaterStock < 500f)
            {
                float deficit = 500f - planet.WaterStock;
                planet.AddResource("resource_water", deficit);
                Logger.LogInfo($"  Added {deficit:F0} water");
            }
            
            if (planet.SiliconStock < 100f)
            {
                float deficit = 100f - planet.SiliconStock;
                planet.AddResource("resource_silicon", deficit);
                Logger.LogInfo($"  Added {deficit:F0} silicon");
            }
        }
    }
}
```

---

## ⚠️ Important Notes

### Performance Considerations

**Avoid frequent building iterations:**
```csharp
❌ BAD - Every second
void Update()
{
    var buildings = planet.Buildings;  // Expensive enumeration!
}

✅ GOOD - On events
SDK.Events.MartianDayChanged += (data) =>
{
    var buildings = planet.Buildings;  // Once per day
};
```

### Null Safety

**Always check planet exists:**
```csharp
var planet = SDK.Game.CurrentPlanet;
if (planet == null)
{
    Logger.LogWarning("Planet not loaded yet");
    return;
}

// Safe to use planet now
```

### Resource Key Validation

**Use GetResourceStock() for safety:**
```csharp
❌ RISKY - Direct field access
float water = planet._someInternalField;

✅ SAFE - Validated method
float water = planet.GetResourceStock("resource_water");
```

---

## 🔗 Related Classes

- **[Building](Building.md)** - Individual building management
- **[Atmosphere](Atmosphere.md)** - Climate and terraforming systems
- **[Universe](Universe.md)** - Multi-planet and game state
- **[BaseGame](BaseGame.md)** - Global game instance

---

## 📚 Related Documentation

- **[Climate Events](../sdk/Events.md#climate-events)** - Atmospheric event handling
- **[Resource Events](../sdk/Events.md#resource-events)** - Resource change tracking
- **[Building Tutorial](../tutorials/Buildings.md)** - Modifying planetary infrastructure
- **[SDK Overview](../sdk/Overview.md)** - Complete SDK reference

---

**Last Updated:** December 17, 2025  
**SDK Version:** 1.1.0
