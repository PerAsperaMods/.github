# 🌌 Universe Class Reference

The `Universe` wrapper provides access to game-wide state, time management, pause control, and multi-planet coordination. This is your interface to the overall game simulation.

## 📖 Overview

```csharp
using PerAspera.GameAPI.Wrappers;

// Access universe
var universe = SDK.Game.Universe;

if (universe != null)
{
    Logger.LogInfo($"Current Sol: {universe.CurrentSol}");
    Logger.LogInfo($"Game Speed: {universe.GameSpeed}x");
    Logger.LogInfo($"Paused: {universe.IsPaused}");
}
```

---

## ⏰ Time Management

### CurrentSol
```csharp
public int CurrentSol { get; }
```

**Description:** Total Martian days (sols) passed since game start.

**Usage:**
```csharp
int days = universe.CurrentSol;
Logger.LogInfo($"Mission Day: {days}");

// Check milestones
if (days == 100)
{
    Logger.LogInfo("🎉 100 sols milestone reached!");
}

// Periodic events
if (days % 10 == 0)
{
    Logger.LogInfo($"Decade checkpoint: Sol {days}");
}
```

**Note:** Sol = Martian day (~24.6 Earth hours).

---

### GameSpeed
```csharp
public float GameSpeed { get; set; }
```

**Description:** Current game simulation speed multiplier.

**Values:**
- `1.0f` - Normal speed
- `2.0f` - 2x speed
- `3.0f` - 3x speed
- `0.5f` - Half speed

**Usage:**
```csharp
// Get current speed
float speed = universe.GameSpeed;

// Set speed
universe.GameSpeed = 2.0f;  // Fast-forward
Logger.LogInfo($"Game speed set to {universe.GameSpeed}x");

// Dynamic speed based on events
SDK.Events.BuildingSpawned += (data) =>
{
    // Slow down when important events occur
    universe.GameSpeed = 1.0f;
};
```

**Limits:** Game enforces min/max speed limits internally.

---

### IsPaused
```csharp
public bool IsPaused { get; }
```

**Description:** Check if game simulation is paused.

**Usage:**
```csharp
if (universe.IsPaused)
{
    Logger.LogInfo("⏸️ Game is paused");
}
else
{
    Logger.LogInfo("▶️ Game is running");
}

// Conditional logic
if (!universe.IsPaused && universe.GameSpeed > 1.0f)
{
    Logger.LogInfo("Fast-forwarding...");
}
```

---

## 🎮 Game Control

### Pause()
```csharp
public void Pause()
```

**Description:** Pause the game simulation.

**Usage:**
```csharp
// Pause on critical events
SDK.Events.BuildingSpawned += (data) =>
{
    if (data.BuildingName == "Colony Hub")
    {
        universe.Pause();
        Logger.LogInfo("⏸️ Game paused: Colony Hub built!");
    }
};

// Emergency pause
if (planet.WaterStock < 10f)
{
    universe.Pause();
    Logger.LogError("🚨 CRITICAL: Water shortage! Game paused.");
}
```

---

### Unpause()
```csharp
public void Unpause()
```

**Description:** Resume game simulation.

**Usage:**
```csharp
// Resume after condition met
if (planet.HasSufficientWater && universe.IsPaused)
{
    universe.Unpause();
    Logger.LogInfo("▶️ Water restored, resuming...");
}

// Auto-resume after delay
SDK.Events.BuildingSpawned += async (data) =>
{
    universe.Pause();
    Logger.LogInfo("Paused for 5 seconds...");
    
    await Task.Delay(5000);
    
    universe.Unpause();
    Logger.LogInfo("Resumed");
};
```

---

### TogglePause()
```csharp
public void TogglePause()
```

**Description:** Toggle pause state (pause if running, unpause if paused).

**Usage:**
```csharp
// Hotkey-style toggling
void OnUserInput()
{
    universe.TogglePause();
    Logger.LogInfo($"Pause toggled: {universe.IsPaused}");
}

// Periodic pause/unpause
SDK.Events.MartianDayChanged += (data) =>
{
    if (data.TotalDays % 50 == 0)
    {
        universe.TogglePause();
        Logger.LogInfo("Milestone pause");
    }
};
```

---

## 🌍 Game Object Access

### GetPlanet()
```csharp
public object? GetPlanet()
```

**Description:** Get the native planet object (use Planet wrapper instead).

**Usage:**
```csharp
// ❌ Advanced/internal use only
var nativePlanet = universe.GetPlanet();

// ✅ Prefer SDK wrapper
var planet = SDK.Game.CurrentPlanet;
```

**Note:** Use `SDK.Game.CurrentPlanet` for type-safe access.

---

### GetBaseGame()
```csharp
public object? GetBaseGame()
```

**Description:** Get the native BaseGame singleton (use SDK wrapper instead).

**Usage:**
```csharp
// ❌ Advanced/internal use only  
var nativeBase = universe.GetBaseGame();

// ✅ Prefer SDK wrapper
var baseGame = SDK.Game.BaseGameInstance;
```

**Note:** Use `SDK.Game.BaseGameInstance` for type-safe access.

---

## 💡 Practical Examples

### Mission Timer

```csharp
[BepInPlugin("com.example.timer", "Mission Timer", "1.0.0")]
public class MissionTimer : PerAsperaSDKPlugin
{
    private int _startSol;
    
    protected override void OnSDKReady()
    {
        SDK.Events.GameFullyLoaded += () =>
        {
            _startSol = SDK.Game.Universe.CurrentSol;
            Logger.LogInfo($"Mission started at Sol {_startSol}");
        };
        
        SDK.Events.MartianDayChanged += OnDayChanged;
    }
    
    private void OnDayChanged(MartianDayEventData data)
    {
        int elapsed = SDK.Game.Universe.CurrentSol - _startSol;
        
        Logger.LogInfo($"📅 Mission Day {elapsed}");
        
        // Milestone notifications
        if (elapsed % 100 == 0)
        {
            Logger.LogInfo($"🎉 {elapsed} sols completed!");
        }
    }
}
```

### Auto-Speed Controller

```csharp
[BepInPlugin("com.example.autospeed", "Auto Speed", "1.0.0")]
public class AutoSpeed : PerAsperaSDKPlugin
{
    private const float NORMAL_SPEED = 1.0f;
    private const float FAST_SPEED = 3.0f;
    
    protected override void OnSDKReady()
    {
        SDK.Events.BuildingSpawned += OnBuildingEvent;
        SDK.Events.BuildingDespawned += OnBuildingEvent;
        
        SDK.Events.MartianDayChanged += OnDayChanged;
    }
    
    private void OnBuildingEvent(BuildingEventData data)
    {
        // Slow down for important events
        SDK.Game.Universe.GameSpeed = NORMAL_SPEED;
        Logger.LogInfo($"⚡ Speed reduced: {data.BuildingName} event");
    }
    
    private void OnDayChanged(MartianDayEventData data)
    {
        // Speed up if nothing happening
        var buildings = SDK.Game.CurrentPlanet?.Buildings;
        if (buildings != null && buildings.All(b => b.IsBuilt))
        {
            SDK.Game.Universe.GameSpeed = FAST_SPEED;
            Logger.LogInfo("⚡ Speed increased: All construction complete");
        }
    }
}
```

### Emergency Pause System

```csharp
[BepInPlugin("com.example.emergency", "Emergency Pause", "1.0.0")]
public class EmergencyPause : PerAsperaSDKPlugin
{
    private bool _emergencyActive = false;
    
    protected override void OnSDKReady()
    {
        SDK.Events.ClimateAnalysisComplete += CheckEmergency;
        SDK.Events.MartianDayChanged += CheckResources;
    }
    
    private void CheckEmergency(ClimateEventData data)
    {
        var universe = SDK.Game.Universe;
        
        // Temperature emergency
        if (data.Temperature < 200f || data.Temperature > 350f)
        {
            TriggerEmergency($"Temperature critical: {data.Temperature:F0}K");
        }
        
        // Pressure emergency
        if (data.Pressure < 100f)
        {
            TriggerEmergency($"Pressure critically low: {data.Pressure:F0} Pa");
        }
    }
    
    private void CheckResources(MartianDayEventData data)
    {
        var planet = SDK.Game.CurrentPlanet;
        if (planet == null) return;
        
        // Water emergency
        if (planet.WaterStock < 10f)
        {
            TriggerEmergency($"Water critical: {planet.WaterStock:F0}");
        }
    }
    
    private void TriggerEmergency(string reason)
    {
        if (_emergencyActive) return;
        
        _emergencyActive = true;
        SDK.Game.Universe.Pause();
        
        Logger.LogError("🚨 EMERGENCY PAUSE 🚨");
        Logger.LogError($"   Reason: {reason}");
        Logger.LogError("   Review situation before continuing!");
    }
}
```

### Sol Milestone Tracker

```csharp
SDK.Events.MartianDayChanged += (data) =>
{
    var universe = SDK.Game.Universe;
    int sol = universe.CurrentSol;
    
    // Major milestones
    Dictionary<int, string> milestones = new()
    {
        [10] = "First decade",
        [50] = "50 sols - Early settlement",
        [100] = "100 sols - Established colony",
        [365] = "One Martian year",
        [500] = "500 sols - Major milestone",
        [1000] = "1000 sols - Thriving colony"
    };
    
    if (milestones.TryGetValue(sol, out string message))
    {
        universe.Pause();
        Logger.LogInfo("═══════════════════════════════");
        Logger.LogInfo($"🎉 MILESTONE: Sol {sol}");
        Logger.LogInfo($"   {message}");
        Logger.LogInfo("═══════════════════════════════");
    }
};
```

---

## ⚠️ Important Notes

### Performance Impact

**Pause/unpause can affect mod timing:**
```csharp
// ❌ Don't rely on real-time intervals when paused
private float _lastTime;
void Update()
{
    if (Time.time - _lastTime > 1.0f) { }  // Won't work when paused!
}

// ✅ Use game events instead
SDK.Events.MartianDayChanged += (data) =>
{
    // Fires based on game time, not real time
};
```

### Speed Limits

The game enforces internal speed limits:
- Minimum: Usually 1.0x (can't go slower)
- Maximum: Usually 3.0x (can't go faster)

**Setting values outside limits will be clamped:**
```csharp
universe.GameSpeed = 10.0f;  // Will be clamped to max (3.0x)
Logger.LogInfo($"Actual speed: {universe.GameSpeed}x");  // 3.0x
```

### Null Safety

**Always check universe exists:**
```csharp
var universe = SDK.Game.Universe;
if (universe == null)
{
    Logger.LogWarning("Universe not initialized");
    return;
}

// Safe to use
universe.Pause();
```

---

## 🔗 Related Classes

- **[Planet](Planet.md)** - Planetary systems and resources
- **[BaseGame](BaseGame.md)** - Global game singleton
- **[Building](Building.md)** - Infrastructure management

---

## 📚 Related Documentation

- **[Time Events](../sdk/Events.md#time-events)** - Day/season event handling
- **[Game Lifecycle](../sdk/Events.md#game-lifecycle-events)** - Game state events
- **[SDK Overview](../sdk/Overview.md)** - Complete SDK reference

---

**Last Updated:** December 17, 2025  
**SDK Version:** 1.1.0
