# Enhanced Event System - API Migration Guide

**Date**: 2025-12-18  
**Target Audience**: Mod Developers using Per Aspera SDK Events

---

## 🎯 **Overview: Enhanced Event System v2.0**

The Enhanced Event System resolves the critical issue of **unsafe IL2CPP object access** in Per Aspera events by providing **automatic wrapper conversion** to type-safe SDK classes.

### **Problem Solved**

**BEFORE** ❌ Dangerous and Inconsistent:
```csharp
EventSystem.Subscribe("building.spawned", (object data) => {
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // object - no type safety!
    
    // Reflection required - error-prone
    var property = building?.GetType().GetProperty("energyProduction");
    var energy = property?.GetValue(building); 
});
```

**AFTER** ✅ Type-Safe and Performant:
```csharp
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>("building.spawned", evt => {
    var building = evt.Building; // Building wrapper - full IntelliSense!
    var energy = building.GetEnergyProduction(); // Direct API access
});
```

---

## 🚀 **Quick Migration Guide**

**✅ MISE À JOUR 2025-12-20**: Corrections critiques signatures d'événements - **Action<T>** requis!

### **1. Update Using Statements**
```csharp
// Add Enhanced Events import
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;
using PerAspera.GameAPI.Events.Integration; // ✅ AJOUTÉ pour EnhancedEventBus
```

### **🚨 CRITIQUE: Corrections Signatures d'Événements**

#### **❌ ERREUR COMMUNE - Parameter count mismatch**
```csharp
// ❌ FAUX: Action sans paramètres
EnhancedEventBus.SubscribeToGameHubReady(() => {
    // Cause: Parameter count mismatch error
});
```

#### **✅ CORRECT - Action<EventType> avec données**
```csharp
// ✅ CORRECT: Action<GameHubReadyEvent> avec paramètre
EnhancedEventBus.SubscribeToGameHubReady(evt => {
    LogAspera.Info($"GameHub ready at {evt.Timestamp}");
    // Accès aux données de l'événement
});
```

### **2. Unity Input System Integration** ✅ **NOUVEAU**

**DÉCOUVERTE**: Per Aspera a **DEUX sets de DLLs Unity**:
- **`unity-libs/`**: DLLs Unity réelles avec implémentations ✅
- **`interop/`**: DLLs IL2CPP stripped (compilation seulement) ❌

#### **Unity Input via unity-libs (FONCTIONNE)**
```csharp
public static class UnityInputWrapper
{
    private static string UnityLibsPath = Path.Combine(
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData),
        "BepInEx", "unity-libs");
        
    public static bool SafeGetKeyDown(KeyCode keyCode)
    {
        // Chargement dynamique depuis unity-libs/
        var assembly = Assembly.LoadFrom(
            Path.Combine(UnityLibsPath, "UnityEngine.CoreModule.dll"));
            
        var inputType = assembly.GetType("UnityEngine.Input");
        var method = inputType.GetMethod("GetKeyDown", new[] { typeof(KeyCode) });
        
        return (bool)method.Invoke(null, new object[] { keyCode });
    }
}

// ✅ UTILISATION: F9 Commands working!
if (UnityInputWrapper.SafeGetKeyDown(KeyCode.F9))
{
    LogAspera.Success("🎯 F9 detected via UnityInputWrapper!");
    ExecuteCommand();
}
```

### **3. Replace Event Subscriptions**

#### **Legacy Pattern**
```csharp
EventSystem.Subscribe(NativeEventConstants.BuildingSpawned, (object data) => {
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // object
    // Manual reflection/casting...
});
```

#### **Enhanced Pattern**  
```csharp
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt => {
        var building = evt.Building; // Building wrapper
        // Direct API access with IntelliSense...
    }
);
```

### **3. Use Type-Safe Wrapper APIs**

**Available Wrapper Types**:
- `Building` - Complete building API with energy, position, type access
- `Drone` - Drone operations, location, status  
- `BaseGame` - Core game instance access
- `Universe` - Universe-level operations
- `Planet` - Planet-specific data and operations

#### **Building Wrapper Example**
```csharp
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    NativeEventConstants.BuildingSpawned,
    evt =>
    {
        var building = evt.Building; // Building wrapper
        
        // Type-safe property access
        var position = building.GetPosition();
        var energyOutput = building.GetEnergyProduction();
        var buildingType = building.GetBuildingType();
        var isOperational = building.IsOperational();
        
        // Conditional logic with type safety
        if (buildingType.GetName() == "SolarPanel")
        {
            HandleSolarPanelPlacement(building);
        }
    }
);
```

#### **System Event Example**
```csharp
EnhancedEvents.Subscribe<BaseGameDetectedEvent>(
    SDKEventConstants.BaseGameDetected,
    evt =>
    {
        var baseGame = evt.BaseGame; // BaseGame wrapper
        var universe = evt.Universe; // Universe wrapper
        
        // Direct wrapper access
        var gameVersion = baseGame.GetVersion();
        var universeName = universe.GetName();
        
        LogAspera.Info($"Game {gameVersion} detected, universe: {universeName}");
    }
);
```

---

## 📋 **Event Migration Status**

### ✅ **Migrated Events (Type-Safe)**

| Event Category | Status | Wrapper Type | API Available |
|---------------|--------|--------------|---------------|
| **BaseGameDetectedEvent** | ✅ Complete | `BaseGame`, `Universe` | Core game access |
| **BuildingEvents** | ✅ Complete | `Building` | Energy, position, type, status |
| **DroneEvents** | ✅ Complete | `Drone` | Location, operations, status |

**Enhanced Building Events**:
- `BuildingSpawnedNativeEvent` → `Building` wrapper
- `BuildingDespawnedNativeEvent` → `Building` wrapper  
- `BuildingOperativeChangedNativeEvent` → `Building` wrapper
- + All 31 building event types

**Enhanced System Events**:
- `BaseGameDetectedEvent` → `BaseGame` + `Universe` wrappers

### 🚧 **Planned Migrations**

| Event Category | Timeline | Target Wrapper |
|---------------|----------|----------------|
| **ClimateEvents** | Next Sprint | `Climate`, `Planet` |
| **ResourceEvents** | Next Sprint | `Resource`, `ResourceNetwork` |
| **UniverseEvents** | Q1 2025 | `Universe`, `Faction` |

---

## ⚡ **Performance & Configuration**

### **Performance Monitoring**
```csharp
using PerAspera.GameAPI.Events;

// Monitor event system performance
var stats = EnhancedEvents.GetStats();

LogAspera.Info($"Event Types: {stats.EventTypeCount}");
LogAspera.Info($"Active Handlers: {stats.TotalHandlers}");
LogAspera.Info($"Avg Conversion Time: {stats.AverageConversionTimeMs}ms");
LogAspera.Info($"Conversion Success Rate: {stats.ConversionSuccessRate}%");

// Performance alerts
if (stats.AverageConversionTimeMs > 1.0)
{
    LogAspera.Warning("Event conversion overhead high - check wrapper cache");
}
```

### **Configuration Options**
```csharp
// Enable/disable auto-conversion (enabled by default)
EnhancedEvents.SetWrapperConversion(true);

// Check conversion status
bool autoConversionEnabled = EnhancedEvents.IsAutoConversionEnabled();

// Manual conversion for custom scenarios
var building = WrapperFactory.ConvertToWrapper<Building>(nativeInstance);
```

---

## 🔄 **Backward Compatibility**

**100% Backward Compatible**: Existing event code continues to work without modification. The Enhanced System automatically converts objects to wrappers behind the scenes.

```csharp
// Old code still works (but receives wrappers now)
EventSystem.Subscribe(NativeEventConstants.BuildingSpawned, (object data) => {
    var evt = (BuildingSpawnedNativeEvent)data;
    var building = evt.Building; // Now automatically a Building wrapper!
    
    // Old reflection code still works, but wrapper methods better
    var energyProperty = building.GetType().GetProperty("energyProduction");
    // OR use new wrapper methods directly:
    var energy = building.GetEnergyProduction(); // Recommended
});
```

---

## 🛡️ **Error Prevention**

The Enhanced Event System eliminates common IL2CPP errors:

### **Before: Common Errors** ❌
```csharp
// NullReferenceException on IL2CPP objects
var energy = building.energyProduction; // Crash!

// InvalidCastException
var strongBuilding = (Building)building; // Crash!

// MethodAccessException
var methods = building.GetType().GetMethods(); // Crash!
```

### **After: Safe Wrapper Access** ✅
```csharp
// Null-safe wrapper methods
var energy = building.GetEnergyProduction(); // Safe!

// Type-safe casting (building is already Building wrapper)
var buildingWrapper = building; // No cast needed!

// Safe API access through wrappers
var buildingType = building.GetBuildingType(); // Safe!
```

---

## 📚 **Complete API Reference**

### **Enhanced Event Subscription Methods**
```csharp
// Generic type-safe subscription
EnhancedEvents.Subscribe<T>(string eventType, Action<T> handler) where T : class

// Object-based subscription (legacy compatible)
EnhancedEvents.Subscribe(string eventType, Action<object> handler)

// Event publishing
EnhancedEvents.Publish(string eventType, object eventData)

// Configuration
EnhancedEvents.SetWrapperConversion(bool enabled)
EnhancedEvents.IsAutoConversionEnabled()

// Monitoring
EnhancedEvents.GetStats()
```

### **Wrapper Factory Methods**
```csharp
// Convert native object to wrapper
T ConvertToWrapper<T>(object nativeInstance) where T : class

// Auto-detect wrapper type
object ConvertToWrapper(object nativeInstance, Type targetWrapperType = null)

// Register custom converters
void RegisterConverter<T>(Func<object, T> converter) where T : class
```

---

## 🎯 **Best Practices**

### **1. Use Type-Safe Subscriptions**
```csharp
// ✅ Recommended
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(eventType, evt => { ... });

// ❌ Legacy (works but less optimal)
EventSystem.Subscribe(eventType, (object data) => { ... });
```

### **2. Cache Wrapper References**
```csharp
private Building? _cachedBuilding;

EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(eventType, evt =>
{
    _cachedBuilding = evt.Building; // Cache for repeated access
    ProcessBuilding(_cachedBuilding);
});
```

### **3. Handle Null Values Gracefully**
```csharp
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(eventType, evt =>
{
    var building = evt.Building;
    if (building == null)
    {
        LogAspera.Warning("Building wrapper is null in event");
        return;
    }
    
    ProcessBuilding(building);
});
```

### **🚨 4. CRITICAL: Event Creation Patterns (For SDK Contributors)**

#### **✅ CORRECT Event Creation Pattern**
```csharp
using PerAspera.GameAPI.Wrappers; // ⚠️ MANDATORY

private static BuildingSpawnedNativeEvent? CreateBuildingSpawnedEvent(object nativeData)
{
    try
    {
        var evt = new BuildingSpawnedNativeEvent();
        
        // ✅ STEP 1: Extract native instance from payload
        var nativeBuildingInstance = ExtractBuildingFromPayload(nativeData);
        
        // ✅ STEP 2: Convert to wrapper if not null
        if (nativeBuildingInstance != null)
        {
            // ✅ STEP 3: Use short type name (Building, not PerAspera.GameAPI.Wrappers.Building)
            evt.Building = WrapperFactory.ConvertToWrapper<Building>(nativeBuildingInstance);
        }
        
        return evt;
    }
    catch (Exception ex)
    {
        _logger.LogError($"Failed to create BuildingSpawnedEvent: {ex.Message}");
        return null;
    }
}
```

#### **✅ CORRECT Payload Extraction Pattern**
```csharp
private static object? ExtractFromPayload(object payload, string fieldName)
{
    try
    {
        var payloadType = payload.GetType();
        
        // ✅ CORRECT: Handle fields and properties separately
        var field = payloadType.GetField(fieldName);
        if (field != null)
        {
            return field.GetValue(payload);
        }
        
        var property = payloadType.GetProperty(fieldName);
        if (property != null)
        {
            return property.GetValue(payload);
        }
        
        return null;
    }
    catch
    {
        return null;
    }
}
```

#### **❌ FORBIDDEN Anti-Patterns**
```csharp
// ❌ Double wrapping - NEVER DO THIS
evt.Building = (Building)ExtractBuildingFromPayload(new Building(nativeData));

// ❌ Full namespace causing CS0029 error - NEVER DO THIS
evt.Building = WrapperFactory.ConvertToWrapper<PerAspera.GameAPI.Wrappers.Building>(instance);

// ❌ Mixed reflection types causing CS0019 error - NEVER DO THIS
var member = payloadType.GetField(fieldName) ?? payloadType.GetProperty(fieldName);

// ❌ Direct casting causing runtime crash - NEVER DO THIS
evt.Building = (Building)nativeInstance;
```

---

## 🔗 **Related Documentation**

- **[Per-Aspera-Events-Reference.md](./Per-Aspera-Events-Reference.md)** - Complete event catalog
- **[Event-System-Quick-Reference.md](./Event-System-Quick-Reference.md)** - Event type reference  
- **[F:\ModPeraspera\Internal_doc\SDK\Events\Enhanced-Event-System-Architecture.md](../../../Internal_doc/SDK/Events/Enhanced-Event-System-Architecture.md)** - Technical architecture
- **[F:\ModPeraspera\Internal_doc\SDK\Events\Enhanced-Event-System-Implementation-Guide.md](../../../Internal_doc/SDK/Events/Enhanced-Event-System-Implementation-Guide.md)** - Implementation details

---

**Status**: 🚀 **Enhanced Event System v2.0 - Production Ready**  
**Migration**: **Recommended** for all new development, **Optional** for existing mods (backward compatible)