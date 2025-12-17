# Performance Guidelines for Per Aspera Mods

## 📚 Overview

This guide covers performance optimization techniques for Per Aspera mods. IL2CPP has different performance characteristics than standard .NET, and Unity imposes specific constraints.

**Target Audience:**
- Mod developers experiencing lag or frame drops
- Creators of complex mods with many game modifications
- Developers optimizing for late-game performance (1000+ buildings)

**Performance Budget:**
- **< 1ms per frame** - Individual patch impact
- **< 5ms per frame** - Total mod overhead
- **< 100KB allocations/frame** - Garbage collection threshold

---

## ⚡ Core Performance Principles

### 1. Event-Driven > Polling

```csharp
// ❌ BAD: Polling in Update loop (60+ calls per second)
public class BadPerformance : MonoBehaviour
{
    void Update()
    {
        foreach (var building in SDK.Game.Planet.Buildings)
        {
            if (building.Health < 50f)
            {
                // Check 1000 buildings 60 times per second = 60,000 checks/sec
            }
        }
    }
}

// ✅ GOOD: Event-driven (only executes when damage occurs)
public class GoodPerformance
{
    public void Initialize()
    {
        SDK.Events.OnBuildingDamaged += HandleBuildingDamage;
    }
    
    private void HandleBuildingDamage(Building building, float damage)
    {
        if (building.Health < 50f)
        {
            // Executes only when damage occurs
        }
    }
}
```

**Impact**: 60,000 checks/sec → ~10 checks/sec (6000x reduction)

### 2. Cache Expensive Lookups

```csharp
// ❌ BAD: Repeated expensive calls
public class ExpensiveRepeated
{
    public void ProcessBuildings()
    {
        for (int i = 0; i < 100; i++)
        {
            // SDK.Game.Planet.Buildings creates new array every call
            var buildings = SDK.Game.Planet.Buildings;
            // Process...
        }
    }
}

// ✅ GOOD: Cache once per frame
public class CachedAccess
{
    private Building[] _cachedBuildings;
    private int _lastCacheFrame = -1;
    
    public void ProcessBuildings()
    {
        int currentFrame = UnityEngine.Time.frameCount;
        
        // Refresh cache only once per frame
        if (_lastCacheFrame != currentFrame)
        {
            _cachedBuildings = SDK.Game.Planet.Buildings;
            _lastCacheFrame = currentFrame;
        }
        
        for (int i = 0; i < 100; i++)
        {
            // Reuse cached array
            var buildings = _cachedBuildings;
            // Process...
        }
    }
}
```

### 3. Allocation-Free Hot Paths

```csharp
// ❌ BAD: Allocates new list every frame
public class AllocatingHotPath
{
    public void Update()
    {
        var solarPanels = new List<Building>(); // 40 bytes + array allocation
        
        foreach (var building in SDK.Game.Planet.Buildings)
        {
            if (building.TypeKey == "SolarPanel")
                solarPanels.Add(building); // Potential array resize = more allocations
        }
    }
}

// ✅ GOOD: Reuse pre-allocated collection
public class AllocationFree
{
    private List<Building> _solarPanels = new List<Building>(100); // Pre-sized
    
    public void Update()
    {
        _solarPanels.Clear(); // Reuse, no allocation
        
        foreach (var building in SDK.Game.Planet.Buildings)
        {
            if (building.TypeKey == "SolarPanel")
                _solarPanels.Add(building); // No resize if capacity sufficient
        }
    }
}
```

---

## 🔍 Profiling & Measurement

### Unity Profiler Integration

```csharp
using UnityEngine.Profiling;

public class ProfiledMod
{
    public void ExpensiveOperation()
    {
        Profiler.BeginSample("MyMod.ExpensiveOperation");
        try
        {
            // Your code here
            ProcessComplexLogic();
        }
        finally
        {
            Profiler.EndSample();
        }
    }
    
    private void ProcessComplexLogic()
    {
        Profiler.BeginSample("MyMod.SubOperation");
        // Sub-operation code
        Profiler.EndSample();
    }
}
```

**View Results:**
1. Enable Developer Mode in Per Aspera
2. Press `F12` to open Unity Profiler
3. Look for `MyMod.*` samples in timeline
4. Identify hotspots > 1ms per frame

### Custom Performance Logging

```csharp
using System.Diagnostics;

public class PerformanceMonitor
{
    private Stopwatch _stopwatch = new Stopwatch();
    
    public void MeasureOperation(string operationName, Action operation)
    {
        _stopwatch.Restart();
        
        operation();
        
        _stopwatch.Stop();
        
        if (_stopwatch.Elapsed.TotalMilliseconds > 1.0)
        {
            SDK.Logger.LogWarning(
                $"{operationName} took {_stopwatch.Elapsed.TotalMilliseconds:F2}ms (> 1ms budget)");
        }
    }
}

// Usage:
var monitor = new PerformanceMonitor();
monitor.MeasureOperation("ProcessBuildings", () =>
{
    // Your expensive code
});
```

---

## 🧰 Optimization Patterns

### Pattern 1: Lazy Initialization

```csharp
public class LazyResourceManager
{
    private Dictionary<string, float>? _resourceCache;
    
    // Only create when first accessed
    public Dictionary<string, float> ResourceCache
    {
        get
        {
            if (_resourceCache == null)
            {
                SDK.Logger.LogDebug("Initializing resource cache");
                _resourceCache = new Dictionary<string, float>(50); // Pre-sized
                BuildResourceCache();
            }
            return _resourceCache;
        }
    }
    
    private void BuildResourceCache()
    {
        // Expensive initialization only runs once
        foreach (var resource in SDK.Game.Planet.AllResources)
        {
            _resourceCache[resource.Name] = resource.Stock;
        }
    }
}
```

### Pattern 2: Batched Processing

```csharp
public class BatchedProcessor
{
    private Queue<Building> _processingQueue = new Queue<Building>();
    private int _batchSize = 10; // Process 10 per frame
    
    public void QueueBuilding(Building building)
    {
        _processingQueue.Enqueue(building);
    }
    
    public void ProcessBatch()
    {
        int processed = 0;
        
        while (_processingQueue.Count > 0 && processed < _batchSize)
        {
            var building = _processingQueue.Dequeue();
            ProcessSingleBuilding(building);
            processed++;
        }
        
        if (_processingQueue.Count > 0)
        {
            SDK.Logger.LogDebug($"{_processingQueue.Count} buildings remaining in queue");
        }
    }
    
    private void ProcessSingleBuilding(Building building)
    {
        // Expensive per-building processing
    }
}
```

### Pattern 3: Spatial Partitioning

```csharp
using UnityEngine;
using System.Collections.Generic;

public class SpatialGrid
{
    private Dictionary<Vector2Int, List<Building>> _grid = new();
    private int _cellSize = 50; // 50 units per cell
    
    public void AddBuilding(Building building)
    {
        var cell = GetCell(building.Position);
        
        if (!_grid.ContainsKey(cell))
            _grid[cell] = new List<Building>();
            
        _grid[cell].Add(building);
    }
    
    public List<Building> GetNearbyBuildings(Vector3 position, float radius)
    {
        var results = new List<Building>();
        var cell = GetCell(position);
        
        // Only check nearby cells instead of all buildings
        for (int x = -1; x <= 1; x++)
        {
            for (int y = -1; y <= 1; y++)
            {
                var checkCell = new Vector2Int(cell.x + x, cell.y + y);
                if (_grid.TryGetValue(checkCell, out var buildings))
                {
                    foreach (var building in buildings)
                    {
                        if (Vector3.Distance(position, building.Position) <= radius)
                            results.Add(building);
                    }
                }
            }
        }
        
        return results;
    }
    
    private Vector2Int GetCell(Vector3 worldPos)
    {
        return new Vector2Int(
            Mathf.FloorToInt(worldPos.x / _cellSize),
            Mathf.FloorToInt(worldPos.z / _cellSize)
        );
    }
}
```

**Impact**: O(N) → O(9 * N/cells) for proximity queries (100x+ faster for large maps)

### Pattern 4: Object Pooling

```csharp
public class ObjectPool<T> where T : class, new()
{
    private Stack<T> _pool = new Stack<T>(100);
    private int _maxSize = 100;
    
    public T Get()
    {
        if (_pool.Count > 0)
        {
            return _pool.Pop();
        }
        
        return new T(); // Create new if pool empty
    }
    
    public void Return(T obj)
    {
        if (_pool.Count < _maxSize)
        {
            // Clear state if needed
            if (obj is IResettable resettable)
                resettable.Reset();
                
            _pool.Push(obj);
        }
    }
}

// Usage:
public class PooledDataProcessor
{
    private static ObjectPool<List<float>> _listPool = new();
    
    public void ProcessData()
    {
        var tempList = _listPool.Get(); // Reuse from pool
        try
        {
            tempList.Clear();
            
            // Use list...
            
        }
        finally
        {
            _listPool.Return(tempList); // Return to pool
        }
    }
}
```

---

## 🐌 Common Performance Pitfalls

### Pitfall 1: String Operations in Loops

```csharp
// ❌ BAD: String concatenation allocates
public void LogManyBuildings(Building[] buildings)
{
    string result = "";
    foreach (var building in buildings)
    {
        result += building.TypeKey + ", "; // Allocates new string each iteration
    }
    SDK.Logger.LogInfo(result);
}

// ✅ GOOD: Use StringBuilder
using System.Text;

public void LogManyBuildings(Building[] buildings)
{
    var sb = new StringBuilder(buildings.Length * 20); // Pre-size estimate
    foreach (var building in buildings)
    {
        sb.Append(building.TypeKey).Append(", ");
    }
    SDK.Logger.LogInfo(sb.ToString());
}
```

### Pitfall 2: LINQ in Hot Paths

```csharp
// ❌ BAD: LINQ allocates enumerators
public void UpdateEveryFrame()
{
    var solarPanels = SDK.Game.Planet.Buildings
        .Where(b => b.TypeKey == "SolarPanel") // Allocates enumerator + predicate delegate
        .OrderBy(b => b.Health) // More allocations
        .ToArray(); // Final allocation
}

// ✅ GOOD: Manual loop
public void UpdateEveryFrame()
{
    var buildings = SDK.Game.Planet.Buildings;
    int solarCount = 0;
    
    // Count first
    for (int i = 0; i < buildings.Length; i++)
    {
        if (buildings[i].TypeKey == "SolarPanel")
            solarCount++;
    }
    
    // Allocate once with exact size
    var solarPanels = new Building[solarCount];
    int index = 0;
    
    for (int i = 0; i < buildings.Length; i++)
    {
        if (buildings[i].TypeKey == "SolarPanel")
            solarPanels[index++] = buildings[i];
    }
    
    // Manual sort if needed
    System.Array.Sort(solarPanels, (a, b) => a.Health.CompareTo(b.Health));
}
```

**Note**: LINQ is fine for initialization or infrequent operations, avoid in Update/FixedUpdate.

### Pitfall 3: Boxing Value Types

```csharp
// ❌ BAD: Boxing allocates
public void LogResourceStock(float stock)
{
    object boxed = stock; // Boxes float to object (16 bytes allocation)
    SDK.Logger.LogInfo($"Stock: {boxed}"); // String interpolation boxes too
}

// ✅ GOOD: Avoid boxing
public void LogResourceStock(float stock)
{
    SDK.Logger.LogInfo($"Stock: {stock}"); // Direct value, no box
}

// ✅ BETTER: Cache formatted string if used repeatedly
private string _stockFormat = "Stock: {0:F2}";

public void LogResourceStock(float stock)
{
    SDK.Logger.LogInfo(string.Format(_stockFormat, stock));
}
```

### Pitfall 4: Unnecessary IL2CPP Conversions

```csharp
// ❌ BAD: Convert to managed array every frame
public void UpdateFrame()
{
    var buildings = SDK.Game.Planet.Buildings.ToArray(); // IL2CPP → managed conversion
    
    foreach (var building in buildings)
    {
        // Process...
    }
}

// ✅ GOOD: Iterate IL2CPP collection directly (if supported)
public void UpdateFrame()
{
    var buildings = SDK.Game.Planet.Buildings;
    
    // Direct iteration (no conversion)
    for (int i = 0; i < buildings.Length; i++)
    {
        var building = buildings[i];
        // Process...
    }
}
```

---

## 📊 Performance Testing Checklist

### Before Release
- [ ] Profile with Unity Profiler on large save (1000+ buildings)
- [ ] Measure frame time impact (< 1ms per mod feature)
- [ ] Check GC allocations (< 100KB per frame)
- [ ] Test on minimum spec hardware
- [ ] Verify late-game performance (Sol 500+)
- [ ] Profile memory usage over 1-hour session
- [ ] Test with multiple mods enabled
- [ ] Benchmark worst-case scenarios

### Profiling Setup

```csharp
public class PerformanceTest
{
    [Conditional("DEBUG")]
    public static void ProfileSession()
    {
        SDK.Logger.LogInfo("=== Performance Test Started ===");
        
        // Simulate late-game load
        CreateMassiveColony();
        
        // Measure over 1000 frames
        var frameTimes = new List<float>(1000);
        
        for (int i = 0; i < 1000; i++)
        {
            var start = Time.realtimeSinceStartup;
            
            // Your mod's Update logic
            YourMod.UpdateLogic();
            
            var elapsed = (Time.realtimeSinceStartup - start) * 1000f; // ms
            frameTimes.Add(elapsed);
        }
        
        // Statistics
        float avg = frameTimes.Average();
        float max = frameTimes.Max();
        float p95 = frameTimes.OrderBy(t => t).ElementAt((int)(frameTimes.Count * 0.95));
        
        SDK.Logger.LogInfo($"Avg: {avg:F2}ms | Max: {max:F2}ms | P95: {p95:F2}ms");
    }
}
```

---

## 🎯 Optimization Workflow

### Step-by-Step Process

1. **Measure First**: Profile before optimizing
   ```csharp
   Profiler.BeginSample("MyMod.SuspectedHotspot");
   // Suspected slow code
   Profiler.EndSample();
   ```

2. **Identify Hotspots**: Look for >1ms samples

3. **Apply Pattern**: Choose optimization from this guide

4. **Measure Again**: Verify improvement
   ```csharp
   // Before: 5.2ms average
   // After: 0.8ms average (6.5x faster)
   ```

5. **Repeat**: Target next hotspot

### Target Metrics

| Metric | Good | Warning | Critical |
|--------|------|---------|----------|
| Frame time | <1ms | 1-5ms | >5ms |
| GC allocations | <50KB/frame | 50-100KB | >100KB |
| Memory usage | <100MB | 100-500MB | >500MB |
| CPU % | <5% | 5-15% | >15% |

---

## 🔗 Related Documentation

- [Harmony Patching](Harmony-Patching.md) - Performance-conscious patching
- [SDK Overview](../sdk/Overview.md) - Event-driven patterns
- [Troubleshooting](../troubleshooting/Common-Errors.md) - Performance issues

---

## 📚 External Resources

- **Unity Profiler Guide**: [docs.unity3d.com/Manual/Profiler.html](https://docs.unity3d.com/Manual/Profiler.html)
- **IL2CPP Optimization**: `DOC/bepinex6-docs/articles/advanced/il2cpp_performance.md`
- **C# Performance Tips**: [github.com/dotnet/performance](https://github.com/dotnet/performance)

---

**Next Steps:** Review [Architecture Documentation](../architecture/Overview.md) for system design patterns.
