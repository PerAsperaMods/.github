# Quick Reference

🚀 **Essential commands, code snippets, and API patterns for rapid Per Aspera modding.**

## 🎯 Table of Contents

- [Essential Patterns](#essential-patterns) - Copy-paste ready code
- [Common Tasks](#common-tasks) - Frequent operations
- [Debug & Troubleshoot](#debug--troubleshoot) - Problem-solving tools
- [Performance Tips](#performance-tips) - Optimization patterns
- [API Cheat Sheet](#api-cheat-sheet) - Quick reference

---

## Essential Patterns

### 🔧 Basic Plugin Setup

```csharp
[BepInPlugin(PLUGIN_GUID, PLUGIN_NAME, PLUGIN_VERSION)]
public class YourModPlugin : BasePlugin
{
    public const string PLUGIN_GUID = "com.yourname.peraspera.yourmod";
    public const string PLUGIN_NAME = "Your Mod Name";
    public const string PLUGIN_VERSION = "1.0.0";

    public override void Load()
    {
        try
        {
            // Initialize SDK first
            ModSDK.Initialize(this);
            Log.LogInfo($"Plugin {PLUGIN_NAME} loaded successfully!");
            
            // Subscribe to events
            ModSDK.Events.Subscribe("game.loaded", OnGameLoaded);
            ModSDK.Events.Subscribe("climate.temperatureChanged", OnTemperatureChanged);
            
            // Apply overrides
            ApplyGameBalanceOverrides();
            
        }
        catch (Exception ex)
        {
            Log.LogError($"Failed to load {PLUGIN_NAME}: {ex.Message}");
        }
    }

    private void OnGameLoaded(object eventData)
    {
        Log.LogInfo("🎮 Game loaded - starting mod operations");
        StartModOperations();
    }

    private void OnTemperatureChanged(object eventData)
    {
        var data = eventData as ClimateEventData;
        Log.LogInfo($"🌡️ Temperature: {data.CurrentValue:F1}K (Δ{data.Delta:+0.0;-0.0})");
    }

    private void ApplyGameBalanceOverrides()
    {
        OverrideSystem.RegisterOverride("buildings.solar.efficiency", 1.2f, "20% solar boost");
        OverrideSystem.RegisterOverride("climate.greenhouse.multiplier", 1.3f, "Enhanced warming");
    }
}
```

### 🌡️ Climate Monitoring

```csharp
public class ClimateMonitor
{
    public void CheckClimateStatus()
    {
        var climate = ModSDK.Universe.GetClimateData();
        if (climate == null) return;

        // Temperature analysis
        float tempC = climate.Temperature - 273.15f;
        string tempStatus = tempC < -40 ? "🥶 FREEZING" : 
                           tempC < 0 ? "❄️ COLD" : 
                           tempC < 20 ? "🌡️ COOL" : "☀️ WARM";

        // Atmospheric analysis
        float oxygenPct = (climate.O2Pressure / climate.TotalPressure) * 100f;
        float co2Pct = (climate.CO2Pressure / climate.TotalPressure) * 100f;
        
        bool breathable = oxygenPct > 16f && co2Pct < 4f && climate.TotalPressure > 0.1f;

        Log.LogInfo($"🌍 MARS STATUS - Sol {ModSDK.Universe.GetCurrentSol()}");
        Log.LogInfo($"   🌡️ Temperature: {climate.Temperature:F1}K ({tempC:+0.0;-0.0}°C) {tempStatus}");
        Log.LogInfo($"   🌫️ Pressure: {climate.TotalPressure:F3} atm");
        Log.LogInfo($"   🫁 Oxygen: {oxygenPct:F1}% | CO2: {co2Pct:F1}%");
        Log.LogInfo($"   🏠 Habitable: {(breathable ? "✅ YES" : "❌ NO")}");
    }
}
```

### 🏗️ Building Management

```csharp
public class BuildingManager
{
    public void AnalyzeInfrastructure()
    {
        var allBuildings = BuildingSystem.GetAllBuildings();
        var powerBuildings = BuildingSystem.GetBuildingsByType("SolarArray");
        var waterBuildings = BuildingSystem.GetBuildingsByType("WaterExtractor");

        // Power analysis
        float totalPower = 0f;
        float powerEfficiency = 0f;
        foreach (var building in powerBuildings)
        {
            var production = BuildingSystem.GetBuildingProduction(building.RawBuilding);
            totalPower += production.ActualOutput;
            powerEfficiency += production.Efficiency;
        }
        powerEfficiency = powerBuildings.Length > 0 ? powerEfficiency / powerBuildings.Length : 0f;

        Log.LogInfo($"🏗️ INFRASTRUCTURE STATUS");
        Log.LogInfo($"   🏭 Total buildings: {allBuildings.Length}");
        Log.LogInfo($"   ⚡ Power: {totalPower:F1} MW ({powerEfficiency*100:F0}% efficiency)");
        Log.LogInfo($"   💧 Water extractors: {waterBuildings.Length}");

        // Find problem buildings
        var problemBuildings = allBuildings.Where(b => {
            var prod = BuildingSystem.GetBuildingProduction(b.RawBuilding);
            return prod.Efficiency < 0.5f;
        }).ToList();

        if (problemBuildings.Count > 0)
        {
            Log.LogWarning($"   ⚠️ {problemBuildings.Count} buildings need attention");
        }
    }
}
```

### ⚙️ Dynamic Overrides

```csharp
public class DynamicBalance
{
    public void UpdateGameBalance()
    {
        int currentSol = ModSDK.Universe.GetCurrentSol();
        var climate = ModSDK.Universe.GetClimateData();

        // Early game boosts
        if (currentSol < 50)
        {
            OverrideSystem.RegisterOverride("buildings.solar.output", 1.25f, "Early solar boost");
            OverrideSystem.RegisterOverride("buildings.water.efficiency", 1.2f, "Early water boost");
        }

        // Temperature-based adjustments
        if (climate.Temperature < 240f)
        {
            float boost = 1f + ((240f - climate.Temperature) / 100f);  // Scale boost with cold
            OverrideSystem.UpdateOverride("buildings.greenhouse_gas.rate", boost);
            Log.LogInfo($"🔥 Cold weather boost: {boost:F2}x greenhouse gas production");
        }

        // Pressure-based adjustments
        if (climate.TotalPressure < 0.2f)
        {
            OverrideSystem.UpdateOverride("buildings.atmosphere_processor.rate", 1.5f);
            Log.LogInfo("🌬️ Low pressure boost: 1.5x atmosphere processing");
        }

        // Oxygen-focused late game
        if (currentSol > 150)
        {
            float oxygenRatio = climate.O2Pressure / climate.TotalPressure;
            if (oxygenRatio < 0.15f)
            {
                OverrideSystem.UpdateOverride("buildings.oxygen_generator.efficiency", 1.4f);
                Log.LogInfo("🫁 Late game oxygen boost: 1.4x efficiency");
            }
        }
    }
}
```

---

## Common Tasks

### 📊 Resource Analysis

```csharp
// Quick resource status check
public void CheckResources()
{
    var resources = new[] { "Water", "Energy", "Iron", "Silicon" };
    
    foreach (var resource in resources)
    {
        var production = GetResourceProduction(resource);
        var consumption = GetResourceConsumption(resource);
        var net = production - consumption;
        
        string status = net > 0 ? "✅ Surplus" : net < 0 ? "❌ Deficit" : "⚖️ Balanced";
        Log.LogInfo($"📦 {resource}: {production:F1} - {consumption:F1} = {net:+F1;-F1} {status}");
    }
}
```

### 🔍 Find Buildings Near Location

```csharp
// Find buildings within radius
public List<BuildingInfo> FindNearbyBuildings(Vector3 center, float radius)
{
    var allBuildings = BuildingSystem.GetAllBuildings();
    return allBuildings
        .Where(b => Vector3.Distance(b.Position, center) <= radius)
        .OrderBy(b => Vector3.Distance(b.Position, center))
        .ToList();
}

// Usage
var nearbyBuildings = FindNearbyBuildings(new Vector3(100, 0, 100), 25f);
Log.LogInfo($"🗺️ Found {nearbyBuildings.Count} buildings within 25km of (100,0,100)");
```

### ⏰ Periodic Actions

```csharp
// Execute action every N sols
private int lastActionSol = 0;
private const int ACTION_INTERVAL = 5;

public void PeriodicUpdate()
{
    int currentSol = ModSDK.Universe.GetCurrentSol();
    
    if (currentSol >= lastActionSol + ACTION_INTERVAL)
    {
        PerformPeriodicMaintenance();
        lastActionSol = currentSol;
    }
}

private void PerformPeriodicMaintenance()
{
    Log.LogInfo($"🔧 Performing maintenance on Sol {ModSDK.Universe.GetCurrentSol()}");
    // Your maintenance code here
}
```

### 🎯 Smart Building Placement

```csharp
// Find optimal location for building type
public Vector3 FindOptimalBuildingLocation(string buildingType)
{
    var candidatePositions = GenerateGridPositions(50, 20f);  // 50 positions, 20m apart
    float bestScore = float.MinValue;
    Vector3 bestPosition = Vector3.zero;

    foreach (var position in candidatePositions)
    {
        float score = EvaluateBuildingLocation(position, buildingType);
        if (score > bestScore)
        {
            bestScore = score;
            bestPosition = position;
        }
    }

    Log.LogInfo($"📍 Optimal {buildingType} location: {bestPosition} (score: {bestScore:F2})");
    return bestPosition;
}

private float EvaluateBuildingLocation(Vector3 position, string buildingType)
{
    float score = 0f;
    
    // Proximity to power sources
    var powerBuildings = FindNearbyBuildings(position, 50f)
        .Where(b => b.Type.Contains("Reactor") || b.Type.Contains("Solar"))
        .ToList();
    score += powerBuildings.Count * 10f;
    
    // Resource availability (simplified)
    score += GetResourceDensityAtLocation(position) * 5f;
    
    // Avoid overcrowding
    var nearbyCount = FindNearbyBuildings(position, 20f).Count;
    score -= nearbyCount * 3f;
    
    return score;
}
```

---

## Debug & Troubleshoot

### 🔍 System Diagnostics

```csharp
public class SystemDiagnostics
{
    public void RunFullDiagnostics()
    {
        Log.LogInfo("🔍 RUNNING SYSTEM DIAGNOSTICS...");
        
        // SDK Status
        bool sdkReady = ModSDK.IsInitialized;
        Log.LogInfo($"   🔧 SDK Initialized: {(sdkReady ? "✅ YES" : "❌ NO")}");
        
        // Game Status
        bool gameReady = ModSDK.Universe.IsGameReady();
        Log.LogInfo($"   🎮 Game Ready: {(gameReady ? "✅ YES" : "❌ NO")}");
        
        if (!gameReady) return;
        
        // Climate System
        var climate = ModSDK.Universe.GetClimateData();
        bool climateValid = climate != null && climate.Temperature > 0;
        Log.LogInfo($"   🌡️ Climate Data: {(climateValid ? "✅ VALID" : "❌ INVALID")}");
        
        // Building System
        var buildings = BuildingSystem.GetAllBuildings();
        Log.LogInfo($"   🏗️ Buildings Found: {buildings?.Length ?? 0}");
        
        // Event System
        int eventCount = GetSubscribedEventCount();  // Custom method
        Log.LogInfo($"   📡 Event Subscriptions: {eventCount}");
        
        // Override System
        var overrides = OverrideSystem.GetAllOverrides();
        Log.LogInfo($"   ⚙️ Active Overrides: {overrides?.Count ?? 0}");
        
        Log.LogInfo("✅ DIAGNOSTICS COMPLETE");
    }
    
    public void DiagnosePerformanceIssues()
    {
        var stopwatch = System.Diagnostics.Stopwatch.StartNew();
        
        // Test climate data access speed
        stopwatch.Restart();
        for (int i = 0; i < 100; i++)
        {
            ModSDK.Universe.GetClimateData();
        }
        stopwatch.Stop();
        Log.LogInfo($"⏱️ Climate data access: {stopwatch.ElapsedMilliseconds}ms (100 calls)");
        
        // Test building query speed
        stopwatch.Restart();
        for (int i = 0; i < 10; i++)
        {
            BuildingSystem.GetAllBuildings();
        }
        stopwatch.Stop();
        Log.LogInfo($"⏱️ Building queries: {stopwatch.ElapsedMilliseconds}ms (10 calls)");
        
        if (stopwatch.ElapsedMilliseconds > 100)
        {
            Log.LogWarning("⚠️ Performance issue detected - consider caching");
        }
    }
}
```

### 🐛 Error Recovery

```csharp
// Safe API access with fallbacks
public T SafeGameAccess<T>(Func<T> accessor, T fallbackValue, string operation = "game operation")
{
    try
    {
        return accessor();
    }
    catch (NullReferenceException ex)
    {
        Log.LogWarning($"⚠️ {operation} failed (null reference): {ex.Message}");
        return fallbackValue;
    }
    catch (Exception ex)
    {
        Log.LogError($"❌ {operation} failed: {ex.Message}");
        return fallbackValue;
    }
}

// Usage examples
var climate = SafeGameAccess(
    () => ModSDK.Universe.GetClimateData(), 
    null, 
    "climate data access"
);

var buildings = SafeGameAccess(
    () => BuildingSystem.GetAllBuildings(), 
    new BuildingInfo[0], 
    "building query"
);
```

### 📝 Debug Logging Patterns

```csharp
// Structured debug output
public static class DebugLogger
{
    public static void LogClimateState(PlanetClimateData climate)
    {
        if (climate == null) return;
        
        Log.LogDebug("╔══════════ CLIMATE DEBUG ══════════╗");
        Log.LogDebug($"║ Temperature: {climate.Temperature:F1}K");
        Log.LogDebug($"║ Pressure:    {climate.TotalPressure:F6} atm");
        Log.LogDebug($"║ O2:          {climate.O2Pressure:F6} atm");
        Log.LogDebug($"║ CO2:         {climate.CO2Pressure:F6} atm");
        Log.LogDebug($"║ Water:       {climate.WaterStock:F2}");
        Log.LogDebug($"║ Habitable:   {climate.IsHabitable}");
        Log.LogDebug("╚═══════════════════════════════════╝");
    }
    
    public static void LogBuildingState(BuildingInfo building)
    {
        var production = BuildingSystem.GetBuildingProduction(building.RawBuilding);
        
        Log.LogDebug($"🏗️ {building.Type} @ {building.Position}:");
        Log.LogDebug($"   ⚡ Power: {building.PowerConsumption:F1} MW");
        Log.LogDebug($"   📈 Efficiency: {production.Efficiency*100:F0}%");
        Log.LogDebug($"   🏭 Output: {production.ActualOutput:F3}/sol");
        Log.LogDebug($"   ✅ Operational: {building.IsOperational}");
    }
}
```

---

## Performance Tips

### ⚡ Caching Patterns

```csharp
// Simple cache with expiration
public class SimpleCache<T>
{
    private T cachedValue;
    private DateTime cacheTime;
    private readonly TimeSpan cacheTimeout;
    
    public SimpleCache(TimeSpan timeout)
    {
        cacheTimeout = timeout;
    }
    
    public T GetOrFetch(Func<T> fetcher)
    {
        if (cachedValue == null || DateTime.Now - cacheTime > cacheTimeout)
        {
            cachedValue = fetcher();
            cacheTime = DateTime.Now;
        }
        return cachedValue;
    }
}

// Usage
private readonly SimpleCache<PlanetClimateData> climateCache = new SimpleCache<PlanetClimateData>(TimeSpan.FromSeconds(5));

public PlanetClimateData GetCachedClimate()
{
    return climateCache.GetOrFetch(() => ModSDK.Universe.GetClimateData());
}
```

### 📊 Batch Operations

```csharp
// Process buildings in batches
public void ProcessBuildingsInBatches(int batchSize = 50)
{
    var allBuildings = BuildingSystem.GetAllBuildings();
    
    for (int i = 0; i < allBuildings.Length; i += batchSize)
    {
        var batch = allBuildings.Skip(i).Take(batchSize).ToArray();
        ProcessBuildingBatch(batch);
        
        // Yield control between batches
        System.Threading.Thread.Sleep(10);
    }
}

private void ProcessBuildingBatch(BuildingInfo[] buildings)
{
    foreach (var building in buildings)
    {
        // Process building without intermediate logging
        ProcessBuildingSilently(building);
    }
    
    Log.LogInfo($"Processed batch of {buildings.Length} buildings");
}
```

### 🎯 Event Optimization

```csharp
// Efficient event handling
private DateTime lastEventProcess = DateTime.MinValue;
private readonly Queue<object> eventQueue = new Queue<object>();

private void OnClimateEvent(object eventData)
{
    // Queue events instead of processing immediately
    eventQueue.Enqueue(eventData);
    
    // Process queued events periodically
    if ((DateTime.Now - lastEventProcess).TotalSeconds > 1.0)
    {
        ProcessQueuedEvents();
        lastEventProcess = DateTime.Now;
    }
}

private void ProcessQueuedEvents()
{
    var eventsToProcess = eventQueue.ToArray();
    eventQueue.Clear();
    
    if (eventsToProcess.Length > 0)
    {
        Log.LogInfo($"📡 Processing {eventsToProcess.Length} queued events");
        // Process all events in batch
    }
}
```

---

## API Cheat Sheet

### 🌍 Universe API
```csharp
ModSDK.Universe.GetCurrentSol()              // Current Martian day
ModSDK.Universe.GetGameTime()                // Game DateTime
ModSDK.Universe.GetClimateData()             // Full climate info
ModSDK.Universe.GetPlanet()                  // Raw planet object  
ModSDK.Universe.IsGameReady()                // Game loaded status
```

### 🏗️ Building System API
```csharp
BuildingSystem.GetAllBuildings()                     // All buildings array
BuildingSystem.GetBuildingsByType("SolarArray")     // Specific type
BuildingSystem.GetBuildingProduction(building)      // Production data
BuildingSystem.GetAtmosphericImpact(building)       // Climate impact
```

### 📡 Event System API
```csharp
ModSDK.Events.Subscribe("event.name", handler)      // Add event listener
ModSDK.Events.Unsubscribe("event.name", handler)    // Remove listener
ModSDK.Events.UnsubscribeAll()                      // Remove all listeners
```

### ⚙️ Override System API
```csharp
OverrideSystem.RegisterOverride(key, value, desc)   // Create override
OverrideSystem.UpdateOverride(key, newValue)        // Update existing
OverrideSystem.GetOverride(key)                     // Get override info
OverrideSystem.RemoveOverride(key)                  // Remove override
```

### 🌡️ Climate Events
```
"climate.temperatureChanged"     // Temperature change
"climate.atmosphereChanged"      // Atmosphere composition
"climate.waterStockChanged"      // Water resources
"climate.pressureChanged"        // Atmospheric pressure
```

### 🏭 Building Events
```
"building.constructed"           // Building completed
"building.destroyed"             // Building removed
"building.productionComplete"    // Production cycle done
```

### 🎮 Game Events
```
"game.loaded"                    // Game fully loaded
"planet.initialized"             // Planet systems ready
"game.dayPassed"                 // New sol started
"game.paused" / "game.resumed"   // Game state changes
```

### 🔧 Common Overrides
```
"buildings.solar.efficiency"           // Solar panel efficiency
"buildings.nuclear.output"             // Nuclear power output
"buildings.atmosphere_processor.rate"  // Atmospheric processing speed
"buildings.greenhouse_gas.efficiency"  // Greenhouse gas production
"climate.greenhouse.multiplier"        // Global greenhouse effect
"buildings.water_extractor.rate"       // Water extraction speed
"buildings.oxygen_generator.efficiency" // Oxygen production efficiency
```

---

## 🚀 Next Steps

### 📚 Learn More
- **[First Mod Tutorial](First-Mod-Tutorial)** - Step-by-step mod creation
- **[Climate System Guide](Climate-System-Guide)** - Advanced terraforming
- **[Building System Guide](Building-System-Guide)** - Infrastructure management

### 💡 Get Help
- **[Discord Community](https://discord.gg/peraspera-modding)** - Real-time help
- **[GitHub Discussions](https://github.com/PerAsperaMods/PerAspera-SDK/discussions)** - Q&A forum
- **[Code Examples](Code-Examples)** - Working mod examples

---

**Keep this reference handy while developing your Per Aspera mods! 🔖**