---
name: per-aspera-events-sdk
description: >
  Per Aspera SDK Events system. Use when subscribing to game events (building constructed,
  temperature changed, resource produced, game tick…), using ModEventBus, EnhancedEventBus,
  NativeEventPatcher, writing event handlers, or fixing CS0029/CS0019 event compilation errors.
  Covers correct using statements, WrapperFactory patterns, and forbidden anti-patterns.
argument-hint: "décris l'event que tu veux subscribre ou l'erreur de compilation"
---

# Per Aspera SDK — Events Reference

## Required Using Statements (ALWAYS include all)

```csharp
using System;
using PerAspera.GameAPI.Events.Core;
using PerAspera.GameAPI.Events.Native;
using PerAspera.GameAPI.Events.Constants;
using PerAspera.GameAPI.Wrappers;   // 🚨 CRITICAL — missing = CS0029 errors
using PerAspera.Core;
```

---

## GameEvents — Event Name Constants

```csharp
public static class GameEvents
{
    // Game lifecycle
    public const string OnGameInitialized  = "GameInitialized";
    public const string OnGameTick         = "GameTick";
    public const string OnPlanetLoaded     = "PlanetLoaded";
    public const string OnUniverseReady    = "UniverseReady";

    // Climate
    public const string OnAtmosphereChanged = "AtmosphereChanged";
    public const string OnTemperatureChanged = "TemperatureChanged";
    public const string OnWaterLevelChanged  = "WaterLevelChanged";
    public const string OnPressureChanged    = "PressureChanged";

    // Buildings
    public const string OnBuildingConstructed   = "BuildingConstructed";
    public const string OnBuildingDestroyed     = "BuildingDestroyed";
    public const string OnBuildingStateChanged  = "BuildingStateChanged";

    // Resources
    public const string OnResourceProduced  = "ResourceProduced";
    public const string OnResourceConsumed  = "ResourceConsumed";
}
```

---

## ModEventBus — Subscribe / Publish

```csharp
// Subscribe
string token = ModEventBus.Subscribe<ClimateEventData>(
    GameEvents.OnTemperatureChanged,
    data => Log.LogInfo($"Temp changed: {data.NewValue}°C")
);

// Publish custom event
ModEventBus.Publish(GameEvents.OnTemperatureChanged, new ClimateEventData { NewValue = 25f });

// Unsubscribe
ModEventBus.Unsubscribe(token);

// Stats
Log.LogInfo(ModEventBus.GetStatistics());
```

---

## ✅ Correct Event Creation Pattern

```csharp
private static BuildingSpawnedNativeEvent? CreateBuildingSpawnedEvent(object nativeData)
{
    try
    {
        var evt = new BuildingSpawnedNativeEvent();

        var nativeBuildingInstance = ExtractBuildingFromPayload(nativeData);

        if (nativeBuildingInstance != null)
        {
            // ✅ SHORT type name — NOT full namespace
            evt.Building = WrapperFactory.ConvertToWrapper<Building>(nativeBuildingInstance);
        }

        evt.BuildingTypeKey = ExtractBuildingType(nativeData) ?? "Unknown";
        evt.OwnerFaction    = ExtractOwnerFaction(nativeData);
        return evt;
    }
    catch (Exception ex)
    {
        _logger.LogError($"Failed to create event: {ex.Message}");
        return null;
    }
}
```

---

## ✅ Correct Payload Extraction (No ?? on reflection)

```csharp
private static object? ExtractFromPayload(object payload, string fieldName)
{
    try
    {
        var t = payload.GetType();

        // ✅ Separate if — NEVER use ?? between FieldInfo and PropertyInfo (CS0019)
        var field = t.GetField(fieldName);
        if (field != null) return field.GetValue(payload);

        var prop = t.GetProperty(fieldName);
        if (prop != null) return prop.GetValue(payload);

        return null;
    }
    catch { return null; }
}
```

---

## ❌ Forbidden Patterns

```csharp
// ❌ CS0029 — Full namespace in ConvertToWrapper
evt.Building = WrapperFactory.ConvertToWrapper<PerAspera.GameAPI.Wrappers.Building>(instance);

// ❌ CS0029 — Double wrapping
evt.Building = WrapperFactory.ConvertToWrapper<Building>(new Building(nativeData));

// ❌ CS0029 — Direct cast
evt.Building = (Building)nativeInstance;

// ❌ CS0019 — ?? between incompatible reflection types
var member = t.GetField(name) ?? t.GetProperty(name);

// ❌ Missing null check before ConvertToWrapper
evt.Building = WrapperFactory.ConvertToWrapper<Building>(ExtractBuilding(data)); // may be null
```

---

## Climate Event Data Classes

```csharp
public class ClimateEventData
{
    public float NewValue     { get; set; }
    public float OldValue     { get; set; }
    public float Delta        { get; set; }
    public string Source      { get; set; }
    public DateTime Timestamp { get; set; }
}

public class AtmosphereChangedEvent : ClimateEventData
{
    public Atmosphere NewAtmosphere { get; set; }
    public Atmosphere OldAtmosphere { get; set; }
    public List<string> ChangedGases { get; set; }
}
```

---

## Full Example — Game Tick Handler

```csharp
[BepInPlugin("com.mymod.events", "Events Demo", "1.0.0")]
public class EventsDemoPlugin : BasePlugin
{
    private string? _tickToken;

    public override void Load()
    {
        _tickToken = ModEventBus.Subscribe<object>(
            GameEvents.OnGameTick,
            _ => OnGameTick()
        );
        Log.LogInfo("Events subscribed.");
    }

    private void OnGameTick()
    {
        var planet = GameApi.wrapper.planet;
        if (planet == null) return;
        // react to tick
    }

    // Clean up
    public override void Unload()
    {
        if (_tickToken != null) ModEventBus.Unsubscribe(_tickToken);
    }
}
```
