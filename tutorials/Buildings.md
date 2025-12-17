# 🏗️ Modifying Buildings Tutorial

Learn how to modify building stats, production rates, and behaviors in Per Aspera. This tutorial covers both **YAML datamodel** modifications (no coding) and **SDK/BepInEx** approaches (runtime modification).

## 🎯 What You'll Learn

- ✅ Modify building stats in YAML (energy, production, cost)
- ✅ Change production chains and resource conversion
- ✅ Override building values at runtime with SDK
- ✅ Create dynamic building modifications with events
- ✅ Ensure save game compatibility

---

## 📋 Prerequisites

- Basic understanding of Per Aspera gameplay
- Text editor (VS Code, Notepad++) for YAML
- Visual Studio 2022 for SDK mods (optional)
- Backup of vanilla game files

---

## Part 1: YAML Datamodel Modifications

### 🎮 Use Case: Increase Solar Panel Power Output

**Goal:** Make solar panels produce 50% more energy

#### Step 1: Locate YAML Files

Navigate to game datamodel:
```
C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\
  └── PerAspera_Data\
      └── datamodel\
          └── building.yaml
```

**⚠️ IMPORTANT:** Always backup original files!
```powershell
copy building.yaml building.yaml.backup
```

#### Step 2: Find Solar Panel Entry

Open `building.yaml` and search for solar panel:

```yaml
- !building
  name: solarPanel
  index: 10                          # ⚠️ NEVER MODIFY - breaks save compatibility
  buildingCategory: !buildingCategory energy
  buildTime: 10.0
  
  # Energy production (THIS is what we modify)
  energyProduction: 100.0            # Vanilla: 100 MW
  energyConsumption: 0.0
  
  # Costs
  buildCost:
    - !resource steel: 50
    - !resource electronics: 25
  
  # Maintenance
  maintenanceCost:
    - !resource electronics: 2
  maintenanceInterval: 100.0
```

#### Step 3: Modify Energy Production

Change the value:

```yaml
- !building
  name: solarPanel
  index: 10                          
  buildingCategory: !buildingCategory energy
  buildTime: 10.0
  
  energyProduction: 150.0            # Modified: 150 MW (+50%)
  energyConsumption: 0.0
  
  # ... rest unchanged
```

#### Step 4: Test Changes

1. **Save** `building.yaml`
2. **Launch** Per Aspera
3. **Build** a solar panel
4. **Verify** it produces 150 MW in building panel

### ✅ Save Compatibility Rules

**NEVER modify these fields:**
- `index` - Breaks all save files
- `name` - Breaks references from other files
- `buildingCategory` - Changes game logic

**Safe to modify:**
- `energyProduction` / `energyConsumption`
- `buildCost` / `maintenanceCost`
- `buildTime`
- `productionRates` (see below)
- Description text, icons, sounds

---

## Part 2: Production Chain Modifications

### 🎮 Use Case: Make Water Extractors More Efficient

**Goal:** Water extractors produce water 2x faster

#### Find Water Extractor

```yaml
- !building
  name: waterExtractor
  index: 15
  buildingCategory: !buildingCategory extraction
  
  # Production chain
  productionRates:
    - !resource water: 10.0          # Vanilla: 10 water/hour
  
  # Consumes energy
  energyConsumption: 50.0
```

#### Modify Production

```yaml
- !building
  name: waterExtractor
  index: 15
  buildingCategory: !buildingCategory extraction
  
  productionRates:
    - !resource water: 20.0          # Modified: 20 water/hour (2x)
  
  energyConsumption: 50.0             # Consider increasing energy cost for balance
```

#### Balance Considerations

When increasing production, consider balancing:
- **Energy cost:** Increase `energyConsumption` proportionally
- **Maintenance:** Increase `maintenanceCost` frequency
- **Build cost:** Make more expensive to build

Example balanced modification:
```yaml
productionRates:
  - !resource water: 20.0             # 2x production
energyConsumption: 75.0               # 1.5x energy (not 2x for slight advantage)
maintenanceCost:
  - !resource electronics: 4          # 2x maintenance cost
```

---

## Part 3: Complex Production Chains

### 🎮 Use Case: Modify Chemical Plant

Chemical plants convert resources. Let's change the ratios.

#### Vanilla Chemical Plant

```yaml
- !building
  name: chemicalPlant
  index: 25
  
  # Consumes (inputs)
  consumptionRates:
    - !resource water: 5.0
    - !resource nitrogen: 3.0
  
  # Produces (outputs)
  productionRates:
    - !resource ammonia: 8.0
  
  energyConsumption: 100.0
```

#### Custom Recipe: More Efficient

```yaml
- !building
  name: chemicalPlant
  index: 25
  
  consumptionRates:
    - !resource water: 4.0            # Reduced input (20% less water)
    - !resource nitrogen: 2.5          # Reduced input (17% less nitrogen)
  
  productionRates:
    - !resource ammonia: 10.0          # Increased output (25% more ammonia)
  
  energyConsumption: 120.0             # Increased energy cost for balance
```

---

## Part 4: SDK Runtime Overrides (Advanced)

For **dynamic** modifications that don't require editing YAML:

### 🎮 Use Case: Double All Energy Production at Night

Create a mod that boosts solar panels during Martian night (simulation purposes).

```csharp
using BepInEx;
using PerAspera.ModSDK;
using PerAspera.GameAPI.Overrides;

[BepInPlugin("com.example.nightboost", "Night Energy Boost", "1.0.0")]
public class NightBoostPlugin : PerAsperaSDKPlugin
{
    protected override void OnSDKReady()
    {
        Logger.LogInfo("🌙 Night Energy Boost Mod loaded!");
        
        // Subscribe to time changes
        SDK.Events.MartianDayChanged += OnDayChanged;
    }
    
    private void OnDayChanged(MartianDayEventData eventData)
    {
        // Get all energy buildings
        var energyBuildings = SDK.Game.CurrentPlanet?.Buildings
            .Where(b => b.Category == "energy");
        
        if (energyBuildings == null) return;
        
        // Check if it's nighttime (simplified)
        bool isNight = eventData.DayOfYear % 2 == 0; // Simplified day/night
        
        foreach (var building in energyBuildings)
        {
            if (building.Name == "solarPanel")
            {
                float multiplier = isNight ? 0.5f : 1.5f; // Less at night, more during day
                
                // Override energy production (SDK Overrides System)
                SDK.Overrides.SetOverride(
                    building.InstanceId,
                    "energyProduction",
                    building.BaseEnergyProduction * multiplier
                );
                
                Logger.LogInfo($"☀️ Solar panel #{building.InstanceId}: " +
                              $"{building.EnergyProduction:F0} MW (multiplier: {multiplier}x)");
            }
        }
    }
}
```

### How SDK Overrides Work

```csharp
// Set an override
SDK.Overrides.SetOverride(instanceId, "propertyName", newValue);

// Remove an override (restore vanilla)
SDK.Overrides.RemoveOverride(instanceId, "propertyName");

// Check if overridden
bool isOverridden = SDK.Overrides.HasOverride(instanceId, "propertyName");

// Get current value (respects overrides)
float currentValue = SDK.Overrides.GetValue(instanceId, "propertyName");
```

---

## Part 5: Event-Driven Building Modifications

### 🎮 Use Case: Boost Buildings Near Water

Increase production of buildings adjacent to water sources.

```csharp
using BepInEx;
using PerAspera.ModSDK;
using PerAspera.GameAPI.Events.Data;

[BepInPlugin("com.example.waterboost", "Water Proximity Boost", "1.0.0")]
public class WaterBoostPlugin : PerAsperaSDKPlugin
{
    private const float WATER_RANGE = 5.0f;         // tiles
    private const float PRODUCTION_BOOST = 1.2f;    // +20%
    
    protected override void OnSDKReady()
    {
        Logger.LogInfo("💧 Water Proximity Boost loaded!");
        
        // React to building construction
        SDK.Events.BuildingSpawned += OnBuildingSpawned;
    }
    
    private void OnBuildingSpawned(BuildingEventData eventData)
    {
        var building = eventData.Building;
        
        // Skip energy buildings (only production buildings)
        if (building.Category == "energy") return;
        
        // Find nearby water extractors
        var nearbyWater = SDK.Game.CurrentPlanet?.Buildings
            .Where(b => b.Name == "waterExtractor")
            .Where(b => DistanceTo(building, b) <= WATER_RANGE);
        
        if (nearbyWater?.Any() == true)
        {
            // Apply production boost
            foreach (var resource in building.ProductionRates.Keys)
            {
                float baseRate = building.ProductionRates[resource];
                SDK.Overrides.SetOverride(
                    building.InstanceId,
                    $"production_{resource}",
                    baseRate * PRODUCTION_BOOST
                );
            }
            
            Logger.LogInfo($"💧 Boosted {building.Name} near water: +{(PRODUCTION_BOOST - 1f) * 100:F0}%");
        }
    }
    
    private float DistanceTo(Building a, Building b)
    {
        float dx = a.Position.X - b.Position.X;
        float dy = a.Position.Y - b.Position.Y;
        return (float)Math.Sqrt(dx * dx + dy * dy);
    }
}
```

---

## 🐛 Common Issues

### YAML Syntax Errors

**Symptom:** Game won't load or buildings missing

**Solution:**
1. Check YAML indentation (2 spaces, not tabs)
2. Validate references: `!resource water` (not just `water`)
3. Ensure colons have space: `name: value` (not `name:value`)
4. Restore backup if broken

### Save Game Incompatibility

**Symptom:** Can't load existing saves

**Cause:** Modified `index` or `name` fields

**Solution:**
- Restore original YAML
- Load save
- Re-apply safe modifications only

### Runtime Overrides Not Working

**Symptom:** SDK overrides don't affect gameplay

**Debug:**
```csharp
Logger.LogInfo($"Override applied: {SDK.Overrides.HasOverride(id, property)}");
Logger.LogInfo($"Current value: {building.EnergyProduction}");
Logger.LogInfo($"Base value: {building.BaseEnergyProduction}");
```

Check:
- Property name correct?
- Building instance ID valid?
- Override timing (game fully loaded?)

---

## 📊 Comparison: YAML vs SDK Overrides

| Aspect | YAML Modification | SDK Override |
|--------|-------------------|--------------|
| **Complexity** | ⭐ Simple | ⭐⭐⭐ Advanced |
| **Requires Coding** | ❌ No | ✅ Yes |
| **Dynamic** | ❌ Static | ✅ Runtime |
| **Save Compatible** | ⚠️ Careful | ✅ Always |
| **Mod Distribution** | Easy (files only) | Medium (DLL + files) |
| **Revert Changes** | Manual restore | Auto (disable mod) |
| **Best For** | Permanent changes | Conditional logic |

### When to Use Each

**Use YAML when:**
- Simple stat changes (energy, costs, time)
- Permanent modifications
- No programming experience
- Quick balancing tweaks

**Use SDK when:**
- Dynamic/conditional changes
- Event-driven modifications
- Complex logic (proximity, time-based)
- Save game safety critical
- Distribution as mod DLL

---

## 🎯 Next Steps

### More Building Modifications
- **[Resource Tutorial](Resources.md)** - Create custom resources
- **[Technology Trees](Technology.md)** - Modify research
- **[Event System](Events.md)** - Advanced event handling

### Advanced Topics
- **[Override System Guide](../sdk/Overrides.md)** - Complete override documentation
- **[Performance Guide](../advanced/Performance.md)** - Optimize building mods
- **[YAML Reference](../content/YAML-Datamodel.md)** - Complete YAML documentation

---

## ✅ Summary

You learned:
- ✅ YAML building stat modifications
- ✅ Production chain customization
- ✅ Save compatibility rules
- ✅ SDK runtime overrides
- ✅ Event-driven building modifications
- ✅ When to use YAML vs SDK

**Ready for more?** Try the [Resource Tutorial](Resources.md) next!
