# Architecture Overview - Per Aspera Modding Framework

## 📚 Introduction

This document provides architectural guidance for building robust, maintainable, and compatible Per Aspera mods. Whether creating a simple tweak or a complex gameplay overhaul, following these patterns ensures quality and longevity.

**Target Audience:**
- Experienced mod developers planning complex features
- Teams building multi-component mods
- Developers ensuring multi-mod compatibility
- Contributors to the SDK framework itself

**Prerequisites:**
- Understanding of [SDK Overview](../sdk/Overview.md)
- Experience with [Harmony Patching](../advanced/Harmony-Patching.md)
- Familiarity with design patterns (MVC, Observer, Factory)

**Related Documentation:**
- **[SDK Components](./SDK-Components.md)** - Detailed reference for all 9 SDK projects
- **[Contributing Guidelines](../community/Contributing.md)** - How to contribute to the SDK

---

## 🏗️ Layered Architecture

### The Three-Layer Model

```
┌─────────────────────────────────────────┐
│         Your Mod Plugin                 │
│  (Event handlers, game logic, UI)       │
└─────────────┬───────────────────────────┘
              │ Uses events & wrappers
┌─────────────▼───────────────────────────┐
│       PerAspera ModSDK Layer            │
│  (Events, Wrappers, Override System)    │
└─────────────┬───────────────────────────┘
              │ IL2CPP interop
┌─────────────▼───────────────────────────┐
│        Per Aspera Game (IL2CPP)         │
│   (Unity Engine, Game Logic, Assets)    │
└─────────────────────────────────────────┘
```

### Layer Responsibilities

**1. Game Layer (IL2CPP)**
- Core game logic and Unity engine
- Native game objects and systems
- **You cannot modify** (compiled IL2CPP)

**2. ModSDK Layer**
- Event system for game lifecycle hooks
- Type-safe wrappers for game objects
- Override system for runtime modifications
- **SDK team maintains** (PerAspera.ModSDK)

**3. Your Mod Layer**
- Business logic specific to your mod
- Event subscriptions and handlers
- Custom UI components
- **You create and maintain**

### Dependency Flow

```csharp
// ✅ CORRECT: Depend on SDK abstractions
using PerAspera.ModSDK;
using PerAspera.GameAPI.Wrappers;

public class MyMod
{
    public void Initialize()
    {
        // Subscribe to SDK events
        SDK.Events.OnBuildingPlaced += HandleBuildingPlaced;
    }
    
    private void HandleBuildingPlaced(Building building)
    {
        // Use SDK wrappers
        if (building.TypeKey == "SolarPanel")
        {
            // Mod logic here
        }
    }
}

// ❌ WRONG: Direct IL2CPP dependencies
using Il2CppSystem.Collections.Generic; // Avoid in mod code
using Assembly-CSharp; // Never reference directly

public class BadMod
{
    public void Initialize()
    {
        // Direct game access = fragile, breaks with updates
        var gameObject = GameObject.Find("BuildingManager");
    }
}
```

---

## 🎯 Design Patterns for Modding

### 1. Event-Driven Architecture (Primary Pattern)

**Use Case:** React to game state changes without polling.

```csharp
public class EventDrivenMod : BasePlugin
{
    private ResourceMonitor _resourceMonitor;
    private BuildingOptimizer _buildingOptimizer;
    
    public override void Load()
    {
        _resourceMonitor = new ResourceMonitor();
        _buildingOptimizer = new BuildingOptimizer();
        
        // Subscribe to relevant events
        SDK.Events.OnBuildingPlaced += _buildingOptimizer.OnBuildingPlaced;
        SDK.Events.OnResourceChanged += _resourceMonitor.OnResourceChanged;
        SDK.Events.OnGameLoaded += InitializeModState;
    }
    
    private void InitializeModState()
    {
        Log.LogInfo("Game loaded, initializing mod state");
        _resourceMonitor.Reset();
        _buildingOptimizer.AnalyzeExistingBuildings();
    }
}

public class ResourceMonitor
{
    private Dictionary<string, float> _previousStocks = new();
    
    public void OnResourceChanged(string resourceType, float newStock)
    {
        if (_previousStocks.TryGetValue(resourceType, out float oldStock))
        {
            float delta = newStock - oldStock;
            if (delta < -100f)
            {
                SDK.Logger.LogWarning($"Large resource drop: {resourceType} -{delta}");
            }
        }
        
        _previousStocks[resourceType] = newStock;
    }
    
    public void Reset() => _previousStocks.Clear();
}
```

**Benefits:**
- No Update() overhead
- Automatic cleanup when event source destroyed
- Clear separation of concerns
- Easy to test individual components

### 2. Service Locator Pattern

**Use Case:** Access shared services across mod components.

```csharp
public class ModServiceLocator
{
    private static readonly Dictionary<System.Type, object> _services = new();
    
    public static void Register<T>(T service) where T : class
    {
        _services[typeof(T)] = service;
    }
    
    public static T Get<T>() where T : class
    {
        if (_services.TryGetValue(typeof(T), out var service))
        {
            return (T)service;
        }
        
        throw new InvalidOperationException($"Service {typeof(T).Name} not registered");
    }
    
    public static void Clear() => _services.Clear();
}

// Usage in plugin:
public class MyPlugin : BasePlugin
{
    public override void Load()
    {
        // Register services
        ModServiceLocator.Register<IDataStore>(new JsonDataStore());
        ModServiceLocator.Register<INotificationService>(new UINotifier());
        
        // Components can now access services
        var buildingManager = new CustomBuildingManager();
    }
}

public class CustomBuildingManager
{
    private readonly IDataStore _dataStore;
    
    public CustomBuildingManager()
    {
        _dataStore = ModServiceLocator.Get<IDataStore>();
    }
    
    public void SaveBuildingData(Building building)
    {
        _dataStore.Save($"building_{building.Number}", building.TypeKey);
    }
}
```

### 3. Strategy Pattern for Extensibility

**Use Case:** Different behaviors for different building types.

```csharp
public interface IBuildingStrategy
{
    void OnPlaced(Building building);
    void OnDestroyed(Building building);
    void OnUpdate(Building building);
}

public class SolarPanelStrategy : IBuildingStrategy
{
    public void OnPlaced(Building building)
    {
        SDK.Logger.LogInfo($"Solar panel placed at {building.Position}");
        // Apply solar-specific bonuses
    }
    
    public void OnDestroyed(Building building)
    {
        // Solar cleanup
    }
    
    public void OnUpdate(Building building)
    {
        // Dynamic solar efficiency based on time of day
        float solarEfficiency = CalculateSolarEfficiency();
        SDK.Overrides.SetEnergyProduction(building, building.BaseEnergyOutput * solarEfficiency);
    }
    
    private float CalculateSolarEfficiency()
    {
        float solTime = SDK.Game.Universe.CurrentSol % 1f; // 0-1
        return Mathf.Sin(solTime * Mathf.PI); // Peak at noon
    }
}

public class BuildingStrategyManager
{
    private Dictionary<string, IBuildingStrategy> _strategies = new()
    {
        { "SolarPanel", new SolarPanelStrategy() },
        { "WindTurbine", new WindTurbineStrategy() },
        { "NuclearReactor", new NuclearReactorStrategy() }
    };
    
    public void Initialize()
    {
        SDK.Events.OnBuildingPlaced += building =>
        {
            if (_strategies.TryGetValue(building.TypeKey, out var strategy))
            {
                strategy.OnPlaced(building);
            }
        };
    }
}
```

### 4. Observer Pattern for Custom Events

**Use Case:** Notify multiple systems about mod-specific events.

```csharp
public class CustomEventBus
{
    public event Action<Building, float>? OnBuildingEfficiencyChanged;
    public event Action<string, float>? OnResourceThresholdReached;
    public event Action<int>? OnMilestoneAchieved;
    
    public void RaiseBuildingEfficiency(Building building, float efficiency)
    {
        OnBuildingEfficiencyChanged?.Invoke(building, efficiency);
    }
    
    public void RaiseResourceThreshold(string resource, float amount)
    {
        OnResourceThresholdReached?.Invoke(resource, amount);
    }
    
    public void RaiseMilestone(int milestoneId)
    {
        OnMilestoneAchieved?.Invoke(milestoneId);
    }
}

// Multiple listeners:
public class AchievementTracker
{
    public AchievementTracker(CustomEventBus eventBus)
    {
        eventBus.OnMilestoneAchieved += HandleMilestone;
    }
    
    private void HandleMilestone(int id)
    {
        SDK.Logger.LogInfo($"Achievement: Milestone {id} reached!");
    }
}

public class StatisticsLogger
{
    public StatisticsLogger(CustomEventBus eventBus)
    {
        eventBus.OnBuildingEfficiencyChanged += LogEfficiency;
        eventBus.OnResourceThresholdReached += LogThreshold;
    }
    
    private void LogEfficiency(Building building, float eff)
    {
        // Log to file
    }
    
    private void LogThreshold(string resource, float amount)
    {
        // Log to file
    }
}
```

---

## 🔌 Multi-Mod Compatibility

### Mod Communication Protocol

```csharp
// Define a public API interface for your mod
public interface IResourceBalancerAPI
{
    void RegisterCustomResource(string resourceType, float balanceFactor);
    float GetBalanceFactor(string resourceType);
}

// Implement and expose via BepInEx metadata
[BepInPlugin("com.yourname.resourcebalancer", "Resource Balancer", "1.0.0")]
[BepInProcess("Per Aspera.exe")]
public class ResourceBalancerPlugin : BasePlugin, IResourceBalancerAPI
{
    private Dictionary<string, float> _balanceFactors = new();
    
    public static IResourceBalancerAPI? Instance { get; private set; }
    
    public override void Load()
    {
        Instance = this;
        Log.LogInfo("Resource Balancer API available");
    }
    
    public void RegisterCustomResource(string resourceType, float balanceFactor)
    {
        _balanceFactors[resourceType] = balanceFactor;
    }
    
    public float GetBalanceFactor(string resourceType)
    {
        return _balanceFactors.TryGetValue(resourceType, out float factor) ? factor : 1.0f;
    }
}

// Other mods can find and use your API:
public class OtherMod : BasePlugin
{
    public override void Load()
    {
        // Try to find Resource Balancer mod
        var rbType = System.Type.GetType("ResourceBalancerPlugin, ResourceBalancer");
        if (rbType != null)
        {
            var rbInstance = rbType.GetProperty("Instance")?.GetValue(null) as IResourceBalancerAPI;
            if (rbInstance != null)
            {
                rbInstance.RegisterCustomResource("MyCustomOre", 1.5f);
                Log.LogInfo("Integrated with Resource Balancer");
            }
        }
    }
}
```

### Harmony Patch Coordination

```csharp
// Use unique prefix for all patches
[HarmonyPatch(typeof(Building), "UpdateProduction")]
public class MyMod_BuildingProductionPatch
{
    private const string MOD_ID = "com.yourname.yourmod";
    
    [HarmonyPrefix]
    [HarmonyPriority(Priority.Normal)] // Let other mods run too
    public static void AdjustProduction(Building __instance, ref float __result)
    {
        // Check if other mods already modified this
        if (__result != __instance.BaseProduction)
        {
            // Another mod changed it, apply relative adjustment
            __result *= 1.1f; // +10% on top of other mods
        }
        else
        {
            // No other mod touched it, apply absolute value
            __result = CalculateCustomProduction(__instance);
        }
    }
    
    private static float CalculateCustomProduction(Building building)
    {
        // Custom logic
        return building.BaseProduction * 1.5f;
    }
}
```

### Shared Configuration Format

```csharp
// Use JSON for inter-mod data exchange
public class SharedModData
{
    public string ModId { get; set; }
    public string Version { get; set; }
    public Dictionary<string, object> Data { get; set; }
}

public class ModDataExchange
{
    private static readonly string SHARED_DATA_PATH = 
        Path.Combine(BepInEx.Paths.ConfigPath, "SharedModData");
    
    public static void PublishData(string modId, string version, Dictionary<string, object> data)
    {
        Directory.CreateDirectory(SHARED_DATA_PATH);
        
        var sharedData = new SharedModData
        {
            ModId = modId,
            Version = version,
            Data = data
        };
        
        string filePath = Path.Combine(SHARED_DATA_PATH, $"{modId}.json");
        File.WriteAllText(filePath, JsonConvert.SerializeObject(sharedData, Formatting.Indented));
    }
    
    public static SharedModData? ReadData(string modId)
    {
        string filePath = Path.Combine(SHARED_DATA_PATH, $"{modId}.json");
        if (!File.Exists(filePath))
            return null;
            
        return JsonConvert.DeserializeObject<SharedModData>(File.ReadAllText(filePath));
    }
}
```

---

## 📦 Component Organization

### Recommended Mod Structure

```
YourMod/
├── Plugin.cs                    # BepInEx entry point
├── Configuration/
│   ├── ModConfig.cs            # User settings
│   └── ConfigDefaults.cs       # Default values
├── Core/
│   ├── ModServiceLocator.cs    # Service registration
│   ├── EventBus.cs             # Custom events
│   └── DataStore.cs            # Persistence layer
├── Features/
│   ├── ResourceBalancing/
│   │   ├── ResourceBalancer.cs
│   │   └── BalanceStrategy.cs
│   ├── BuildingOptimizer/
│   │   ├── BuildingOptimizer.cs
│   │   └── OptimizationRules.cs
│   └── CustomUI/
│       ├── DashboardPanel.cs
│       └── NotificationSystem.cs
├── Patches/                     # Harmony patches (if needed)
│   ├── BuildingPatches.cs
│   └── ResourcePatches.cs
└── Utils/
    ├── Logger.cs               # Logging helpers
    ├── MathHelpers.cs          # Shared utilities
    └── Extensions.cs           # Extension methods
```

### Component Lifecycle Management

```csharp
public interface IModComponent
{
    void Initialize();
    void Shutdown();
}

public class ModComponentManager
{
    private readonly List<IModComponent> _components = new();
    
    public void RegisterComponent(IModComponent component)
    {
        _components.Add(component);
        component.Initialize();
    }
    
    public void ShutdownAll()
    {
        foreach (var component in _components)
        {
            try
            {
                component.Shutdown();
            }
            catch (Exception ex)
            {
                SDK.Logger.LogError($"Error shutting down component: {ex}");
            }
        }
        _components.Clear();
    }
}

// In your plugin:
public class MyPlugin : BasePlugin
{
    private ModComponentManager _componentManager;
    
    public override void Load()
    {
        _componentManager = new ModComponentManager();
        
        // Register components in dependency order
        _componentManager.RegisterComponent(new DataStore());
        _componentManager.RegisterComponent(new EventBus());
        _componentManager.RegisterComponent(new ResourceBalancer());
        _componentManager.RegisterComponent(new UIManager());
    }
    
    public override bool Unload()
    {
        _componentManager?.ShutdownAll();
        return base.Unload();
    }
}
```

---

## 🧪 Testing Architecture

### Unit Testing Strategy

```csharp
// Separate testable logic from BepInEx dependencies
public interface IGameDataProvider
{
    Building[] GetAllBuildings();
    float GetResourceStock(string resourceType);
}

// Production implementation uses SDK
public class SDKGameDataProvider : IGameDataProvider
{
    public Building[] GetAllBuildings() => SDK.Game.Planet.Buildings;
    public float GetResourceStock(string resourceType) => 
        SDK.Game.Planet.GetResourceStock(resourceType);
}

// Test implementation uses mocks
public class MockGameDataProvider : IGameDataProvider
{
    public Building[] TestBuildings { get; set; } = Array.Empty<Building>();
    public Dictionary<string, float> TestResources { get; set; } = new();
    
    public Building[] GetAllBuildings() => TestBuildings;
    public float GetResourceStock(string resourceType) =>
        TestResources.TryGetValue(resourceType, out float stock) ? stock : 0f;
}

// Business logic depends on interface
public class ResourceBalancer
{
    private readonly IGameDataProvider _gameData;
    
    public ResourceBalancer(IGameDataProvider gameData)
    {
        _gameData = gameData;
    }
    
    public float CalculateOptimalProduction(string resourceType)
    {
        float currentStock = _gameData.GetResourceStock(resourceType);
        var buildings = _gameData.GetAllBuildings();
        
        // Complex logic here - easily testable
        return currentStock / buildings.Length;
    }
}

// Unit test (using your preferred framework)
[Test]
public void CalculateOptimalProduction_WithMockData_ReturnsExpected()
{
    var mockProvider = new MockGameDataProvider
    {
        TestResources = new() { { "Water", 1000f } },
        TestBuildings = new Building[10]
    };
    
    var balancer = new ResourceBalancer(mockProvider);
    float result = balancer.CalculateOptimalProduction("Water");
    
    Assert.AreEqual(100f, result); // 1000 / 10 = 100
}
```

### Integration Testing

```csharp
public class IntegrationTestHarness
{
    [Conditional("DEBUG")]
    public static void RunTests()
    {
        SDK.Logger.LogInfo("=== Integration Tests Started ===");
        
        TestEventFlow();
        TestResourceBalancing();
        TestBuildingOptimization();
        
        SDK.Logger.LogInfo("=== Integration Tests Completed ===");
    }
    
    private static void TestEventFlow()
    {
        bool eventReceived = false;
        
        SDK.Events.OnBuildingPlaced += (building) =>
        {
            eventReceived = true;
            SDK.Logger.LogInfo($"✓ Event received: {building.TypeKey}");
        };
        
        // Trigger actual game event
        // ... place building via game
        
        if (!eventReceived)
            SDK.Logger.LogError("✗ Event not received!");
    }
}
```

---

## 📋 Architecture Best Practices

### ✅ DO

**Separation of Concerns**
- Keep game interaction in SDK layer
- Business logic independent of BepInEx
- UI separate from core logic

**Dependency Injection**
- Use constructor injection for dependencies
- Depend on interfaces, not concrete types
- Makes testing easier

**Event-Driven Design**
- Subscribe to SDK events over Update loops
- Create custom events for mod-specific notifications
- Unsubscribe on component shutdown

**Defensive Programming**
- Validate all inputs from game
- Handle IL2CPP null checks (`Pointer != IntPtr.Zero`)
- Catch exceptions in event handlers

**Configuration**
- Expose settings via BepInEx ConfigEntry
- Provide sensible defaults
- Validate configuration values

### ❌ DON'T

**Tight Coupling**
- Direct IL2CPP dependencies in business logic
- Hardcoded paths or magic strings
- Singleton abuse (prefer dependency injection)

**Performance Killers**
- Update() loops for state checking
- Repeated expensive calculations
- Large allocations in hot paths

**Compatibility Breakers**
- Assume your mod loads first/last
- Overwrite other mod's patches completely
- Use global state without coordination

**Anti-Patterns**
- God objects (one class does everything)
- Circular dependencies
- Leaking resources (unsubscribe from events!)

---

## 🔗 Related Documentation

- [SDK Overview](../sdk/Overview.md) - Event system and wrappers
- [Harmony Patching](../advanced/Harmony-Patching.md) - When SDK insufficient
- [Performance Guidelines](../advanced/Performance.md) - Optimization patterns
- [Contributing](../community/Contributing.md) - SDK contribution architecture

---

## 📚 Architecture Decision Records

For major architectural decisions in your mod, document them:

```markdown
# ADR-001: Event-Driven vs Polling Architecture

## Status
Accepted

## Context
Need to monitor resource levels and react to changes.

## Decision
Use SDK event system instead of Update() polling.

## Consequences
**Positive:**
- 60x performance improvement
- Automatic cleanup
- Clear separation of concerns

**Negative:**
- Depends on SDK event coverage
- Slightly more complex setup

## Alternatives Considered
- Update() polling: Rejected due to performance
- Harmony patches: Rejected as SDK events sufficient
```

---

**Next Steps:** Review [Contributing Guidelines](../community/Contributing.md) to contribute architecture improvements to the SDK.
