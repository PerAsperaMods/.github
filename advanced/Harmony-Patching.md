# Advanced Harmony Patching Techniques

## 📚 Overview

This guide covers advanced HarmonyX patching techniques for Per Aspera IL2CPP modding. When SDK event-driven development isn't sufficient, these patterns enable deep runtime modifications.

**Prerequisites:**
- Completed [First Mod Tutorial](../getting-started/First-Mod.md)
- Understanding of [SDK Overview](../sdk/Overview.md)
- Basic C# reflection knowledge
- BepInEx 6.x IL2CPP setup

**Documentation References:**
- **BepInEx 6 Runtime Patching**: `F:\ModPeraspera\DOC\bepinex6-docs\articles\dev_guide\runtime_patching.md`
- **HarmonyX Wiki**: `F:\ModPeraspera\DOC\HarmonyX.wiki\**\*.md`
- **Validated Patterns**: `F:\ModPeraspera\DOC\ARCHITECTURE\VALIDATED-PATTERNS.md`

---

## 🎯 When to Use Harmony Patches

### SDK-First Philosophy
```csharp
// ✅ PREFER: SDK event-driven approach
SDK.Events.OnBuildingPlaced += (building) => {
    if (building.TypeKey == "SolarPanel") {
        // Modify behavior
    }
};

// ⚠️ ONLY IF SDK INSUFFICIENT: Harmony patch
[HarmonyPatch(typeof(Building), "UpdateEnergyProduction")]
public static void ModifyProduction(Building __instance) { }
```

### Valid Use Cases for Patches
- **Runtime stat modification** - Change calculations mid-execution
- **Event interception** - Hook into undocumented game events
- **Performance optimization** - Cache expensive calculations
- **IL2CPP type conversion** - Handle native/managed boundaries
- **Method replacement** - Complete override of game logic

---

## 🔧 Patch Types & Patterns

### 1. Prefix Patches - Pre-Execution Modification

**Purpose**: Execute code before original method, optionally skip original.

```csharp
using HarmonyLib;
using PerAspera.ModSDK;

[HarmonyPatch(typeof(Building), "TakeDamage")]
public class BuildingDamagePatch
{
    /// <summary>
    /// Prefix patch to make solar panels invulnerable
    /// </summary>
    /// <param name="__instance">The building instance (injected by Harmony)</param>
    /// <param name="damageAmount">Damage amount parameter</param>
    /// <returns>False = skip original method, True = execute original</returns>
    [HarmonyPrefix]
    public static bool PreventSolarPanelDamage(Building __instance, ref float damageAmount)
    {
        // Access wrapped instance properties
        if (__instance.TypeKey == "SolarPanel")
        {
            SDK.Logger.LogInfo($"Solar panel immune to {damageAmount} damage!");
            return false; // Skip original TakeDamage method
        }
        
        // Modify damage for other buildings
        damageAmount *= 0.5f; // 50% damage reduction
        return true; // Execute original method
    }
}
```

**Key Patterns:**
- `__instance` - The object the method is called on
- `ref` parameters - Can modify original method parameters
- Return `false` - Skip original method execution
- Return `true` - Continue to original method

### 2. Postfix Patches - Post-Execution Modification

**Purpose**: Execute code after original method, modify results.

```csharp
[HarmonyPatch(typeof(Planet), "CalculateResourceIncome")]
public class ResourceIncomePatch
{
    /// <summary>
    /// Postfix patch to boost water income by 50%
    /// </summary>
    /// <param name="__result">Return value from original method (modifiable)</param>
    /// <param name="resourceType">Original method parameter</param>
    [HarmonyPostfix]
    public static void BoostWaterIncome(ref float __result, string resourceType)
    {
        if (resourceType == "Water")
        {
            float originalIncome = __result;
            __result *= 1.5f; // 50% boost
            
            SDK.Logger.LogDebug($"Water income boosted: {originalIncome} → {__result}");
        }
    }
}
```

**Key Patterns:**
- `ref __result` - Modify return value of original method
- Executes even if original threw exception (use `__exception` to check)
- Cannot prevent original method from executing

### 3. Transpiler Patches - IL Code Modification

**Purpose**: Modify the intermediate language (IL) code directly for complex logic changes.

```csharp
using System.Collections.Generic;
using System.Reflection.Emit;
using HarmonyLib;

[HarmonyPatch(typeof(Building), "ConsumeEnergy")]
public class EnergyConsumptionTranspiler
{
    /// <summary>
    /// Transpiler to halve energy consumption for all buildings
    /// WARNING: Advanced technique, requires IL knowledge
    /// </summary>
    [HarmonyTranspiler]
    public static IEnumerable<CodeInstruction> HalveEnergyConsumption(
        IEnumerable<CodeInstruction> instructions)
    {
        var codes = new List<CodeInstruction>(instructions);
        
        for (int i = 0; i < codes.Count; i++)
        {
            // Find: ldc.r4 1.0 (loading energy multiplier constant)
            if (codes[i].opcode == OpCodes.Ldc_R4 && 
                codes[i].operand is float f && f == 1.0f)
            {
                // Replace: 1.0 → 0.5 (half energy consumption)
                codes[i].operand = 0.5f;
                SDK.Logger.LogInfo("Energy consumption halved via IL modification");
                break;
            }
        }
        
        return codes;
    }
}
```

**⚠️ Transpiler Warnings:**
- Most complex patch type
- Fragile - game updates can break IL structure
- Use only when Prefix/Postfix insufficient
- Thoroughly test with updated game versions

---

## 🧰 IL2CPP Interop Patterns

### Type Conversion & Null Safety

```csharp
using Il2CppInterop.Runtime;
using Il2CppSystem.Collections.Generic;

[HarmonyPatch(typeof(BuildingManager), "GetBuildingsInRange")]
public class IL2CPPCollectionPatch
{
    [HarmonyPostfix]
    public static void WrapIL2CPPCollection(ref List<Building> __result)
    {
        // IL2CPP collections require special handling
        if (__result == null)
        {
            SDK.Logger.LogWarning("GetBuildingsInRange returned null!");
            __result = new List<Building>(); // Create IL2CPP list
            return;
        }
        
        // Convert IL2CPP List to C# array for safe iteration
        var buildings = __result.ToArray();
        
        foreach (var building in buildings)
        {
            // Null check required - IL2CPP can have fake nulls
            if (building == null || building.Pointer == System.IntPtr.Zero)
                continue;
                
            SDK.Logger.LogDebug($"Building: {building.TypeKey}");
        }
    }
}
```

**Critical IL2CPP Rules:**
- **Always use `System.Type`** - NOT bare `Type` (assembly conflicts)
- Check `Pointer != IntPtr.Zero` for real null detection
- Use `.ToArray()` for safe IL2CPP collection iteration
- Marshal strings with `Il2CppSystem.String`

### Type Safety Pattern

```csharp
using System.Reflection;

public class SafeReflectionPatch
{
    // ✅ CORRECT: Explicit System.Type declaration
    private static System.Type? _buildingType;
    
    static SafeReflectionPatch()
    {
        _buildingType = AccessTools.TypeByName("Building");
        if (_buildingType == null)
        {
            SDK.Logger.LogError("Failed to find Building type!");
        }
    }
    
    // ❌ WRONG: Bare Type causes ambiguity
    // private static Type? _buildingType; // DON'T DO THIS
}
```

---

## ⚡ Performance Optimization Patterns

### 1. Caching Expensive Calls

```csharp
using System.Collections.Generic;

[HarmonyPatch(typeof(Planet), "GetTotalEnergyProduction")]
public class EnergyProductionCache
{
    private static Dictionary<Planet, (float value, int frame)> _cache = new();
    private static int _cacheLifetime = 10; // Cache for 10 frames
    
    [HarmonyPrefix]
    public static bool CachedEnergyProduction(Planet __instance, ref float __result)
    {
        int currentFrame = UnityEngine.Time.frameCount;
        
        // Check cache validity
        if (_cache.TryGetValue(__instance, out var cached))
        {
            if (currentFrame - cached.frame < _cacheLifetime)
            {
                __result = cached.value;
                return false; // Skip expensive calculation
            }
        }
        
        return true; // Execute original, cache in Postfix
    }
    
    [HarmonyPostfix]
    public static void UpdateCache(Planet __instance, float __result)
    {
        int currentFrame = UnityEngine.Time.frameCount;
        _cache[__instance] = (__result, currentFrame);
        
        // Cleanup old cache entries (prevent memory leak)
        if (_cache.Count > 100)
        {
            var toRemove = new List<Planet>();
            foreach (var kvp in _cache)
            {
                if (currentFrame - kvp.Value.frame > _cacheLifetime * 10)
                    toRemove.Add(kvp.Key);
            }
            toRemove.ForEach(p => _cache.Remove(p));
        }
    }
}
```

### 2. Allocation-Free Patterns

```csharp
[HarmonyPatch(typeof(ResourceManager), "UpdateResourceFlow")]
public class AllocationFreePatch
{
    // Reuse collections instead of allocating new ones
    private static List<Building> _reusableList = new List<Building>(100);
    
    [HarmonyPrefix]
    public static bool OptimizedResourceFlow(ResourceManager __instance)
    {
        _reusableList.Clear(); // Reuse instead of new List<Building>()
        
        // Fill with buildings
        foreach (var building in SDK.Game.Planet.Buildings)
        {
            if (building != null && building.IsAlive)
                _reusableList.Add(building);
        }
        
        // Process without allocations
        for (int i = 0; i < _reusableList.Count; i++)
        {
            var building = _reusableList[i];
            // Process building...
        }
        
        return false; // Skip original (we replaced it)
    }
}
```

---

## 🛡️ Error Handling & Debugging

### Robust Patch Template

```csharp
using System;

[HarmonyPatch(typeof(Building), "OnDestroyed")]
public class SafeDestructionPatch
{
    [HarmonyPrefix]
    public static bool SafePrefix(Building __instance)
    {
        try
        {
            // Validate instance
            if (__instance == null || __instance.Pointer == IntPtr.Zero)
            {
                SDK.Logger.LogWarning("Building OnDestroyed called on null instance!");
                return true; // Let original handle it
            }
            
            // Your patch logic
            SDK.Logger.LogInfo($"Building {__instance.TypeKey} destroyed at {__instance.Position}");
            
            return true;
        }
        catch (Exception ex)
        {
            SDK.Logger.LogError($"Error in SafeDestructionPatch: {ex}");
            return true; // Continue to original on error
        }
    }
}
```

### Patch Debugging Checklist

```csharp
// Enable Harmony debug logging in your plugin
public class MyPlugin : BasePlugin
{
    public override void Load()
    {
        // Enable detailed Harmony logs
        Harmony.DEBUG = true;
        
        var harmony = new Harmony("com.yourmod.id");
        harmony.PatchAll();
        
        // Verify patches applied
        var patchedMethods = harmony.GetPatchedMethods();
        foreach (var method in patchedMethods)
        {
            Log.LogInfo($"Patched: {method.DeclaringType?.Name}.{method.Name}");
        }
    }
}
```

**Common Issues:**
- **Patch not applying**: Check method signature matches exactly
- **NullReferenceException**: Validate `__instance` and IL2CPP pointers
- **Performance degradation**: Profile with Unity Profiler, check allocation rates
- **Game crashes**: Ensure exception handling in all patch methods

---

## 🎓 Real-World Examples

### Example 1: Dynamic Building Stats Modifier

```csharp
[HarmonyPatch(typeof(Building), nameof(Building.EnergyProduction), MethodType.Getter)]
public class DynamicEnergyPatch
{
    private static Dictionary<string, float> _bonuses = new()
    {
        { "SolarPanel", 1.5f },  // +50% solar
        { "WindTurbine", 2.0f }, // +100% wind
    };
    
    [HarmonyPostfix]
    public static void ApplyEnergyBonus(Building __instance, ref float __result)
    {
        if (_bonuses.TryGetValue(__instance.TypeKey, out float multiplier))
        {
            __result *= multiplier;
        }
    }
}
```

### Example 2: Custom Resource Production Chain

```csharp
[HarmonyPatch(typeof(Planet), "ProduceResources")]
public class CustomResourceChain
{
    [HarmonyPostfix]
    public static void AddBonusProduction(Planet __instance)
    {
        // Every 10 water produces 1 carbon (custom chain)
        float waterStock = __instance.GetResourceStock("Water");
        if (waterStock >= 10f)
        {
            float bonusCarbon = Mathf.Floor(waterStock / 10f);
            __instance.AddResource("Carbon", bonusCarbon);
            
            SDK.Logger.LogDebug($"Produced {bonusCarbon} carbon from water");
        }
    }
}
```

### Example 3: Event Injection Pattern

```csharp
[HarmonyPatch(typeof(Building), "CompleteConstruction")]
public class ConstructionEventPatch
{
    public static event Action<Building>? OnBuildingConstructed;
    
    [HarmonyPostfix]
    public static void TriggerCustomEvent(Building __instance)
    {
        // Inject custom event into game flow
        OnBuildingConstructed?.Invoke(__instance);
    }
}

// In your plugin:
ConstructionEventPatch.OnBuildingConstructed += (building) =>
{
    SDK.Logger.LogInfo($"Custom event: {building.TypeKey} completed!");
};
```

---

## 📋 Best Practices Summary

### ✅ DO
- Prefer SDK event system over patches when possible
- Use Prefix/Postfix over Transpilers
- Cache expensive calculations with frame-based invalidation
- Validate `__instance` for null and `Pointer != IntPtr.Zero`
- Use `System.Type` for all type declarations
- Add comprehensive error handling with try/catch
- Profile performance impact with Unity Profiler
- Document patch purpose and SDK gaps in comments

### ❌ DON'T
- Patch methods with SDK event equivalents
- Use bare `Type` declarations (IL2CPP conflicts)
- Allocate in hot paths (Update loops, frequent calls)
- Modify IL2CPP collections without `.ToArray()`
- Skip null validation on IL2CPP objects
- Create patches without error handling
- Patch without understanding original method behavior

---

## 🔗 Related Documentation

- [SDK Overview](../sdk/Overview.md) - Try SDK first before patching
- [Performance Guidelines](Performance.md) - Optimization best practices
- [Troubleshooting](../troubleshooting/Common-Errors.md) - Common patching errors
- [Native Classes](../native-classes/Building.md) - Available properties/methods

---

## 📚 External Resources

- **BepInEx Runtime Patching Guide**: `DOC/bepinex6-docs/articles/dev_guide/runtime_patching.md`
- **HarmonyX Patching Techniques**: `DOC/HarmonyX.wiki/Patching.md`
- **IL2CPP Interop Docs**: `DOC/bepinex6-docs/articles/advanced/il2cpp_interop.md`
- **Validated Patterns**: `DOC/ARCHITECTURE/VALIDATED-PATTERNS.md`

---

**Next Steps:** Explore [Performance Guidelines](Performance.md) for optimization techniques.
