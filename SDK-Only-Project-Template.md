# 📋 SDK-Only Project Template Guide

**Architecture**: Per Aspera SDK v2.0  
**Template Version**: 2025-12-18  
**Target**: Zero-dependency mod projects

---

## 🎯 **Quick Start Template**

### **1. Create New Mod Project**

```bash
# Navigate to mods directory
cd F:\ModPeraspera\Individual-Mods

# Create project
dotnet new classlib -n YourModName
cd YourModName

# Remove default Class1.cs
del Class1.cs
```

### **2. Replace .csproj with SDK-Only Template**

```xml
<Project Sdk="Microsoft.NET.Sdk">
  
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <AssemblyName>YourModName</AssemblyName>
    <RootNamespace>YourModName</RootNamespace>
    <LangVersion>latest</LangVersion>
    <Nullable>enable</Nullable>
    <ImplicitUsings>disable</ImplicitUsings>
  </PropertyGroup>

  <!-- 🎯 SINGLE DEPENDENCY - SDK PROVIDES ALL -->
  <Import Project="$(MSBuildThisFileDirectory)..\..\SDK\sdkDLL.props" />

  <!-- Optional: Auto-deploy to game -->
  <Target Name="DeployToGame" AfterTargets="Build" Condition="Exists('C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx\plugins\')">
    <Copy 
      SourceFiles="$(TargetPath)" 
      DestinationFolder="C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx\plugins\" 
      SkipUnchangedFiles="true" />
    <Message Text="✅ Deployed to Per Aspera: $(TargetFileName)" Importance="high" />
  </Target>

</Project>
```

### **3. Create Plugin Main Class**

```csharp
using BepInEx;
using BepInEx.Logging;
using PerAspera.Core;
using PerAspera.GameAPI.Events;
using PerAspera.GameAPI.Events.Constants;
using BaseGameWrapper = PerAspera.GameAPI.Wrappers.BaseGame;

namespace YourModName
{
    /// <summary>
    /// Your Mod - SDK v2.0 Template
    /// </summary>
    [BepInPlugin("com.yourname.yourmod", "Your Mod Name", "1.0.0")]
    public class YourModPlugin : BasePlugin
    {
        private LogAspera _log;

        public override void Load()
        {
            _log = new LogAspera("YourMod");
            _log.Info("🚀 YourMod loading...");

            // Subscribe to enhanced events
            EnhancedEvents.Subscribe<GameFullyLoadedEvent>(
                SDKEventConstants.GameFullyLoaded, 
                OnGameLoaded
            );

            _log.Info("✅ YourMod loaded successfully");
        }

        private void OnGameLoaded(GameFullyLoadedEvent evt)
        {
            _log.Info("🎮 Game fully loaded - initializing mod");

            // Access game systems via wrappers
            var baseGame = evt.BaseGameWrapper;
            var universe = evt.UniverseWrapper;  
            var planet = evt.PlanetWrapper;

            // Your mod logic here
            _log.Info($"Planet: {planet.Name}");
            _log.Info($"Buildings: {planet.GetBuildings()?.Count() ?? 0}");

            // Initialize your mod systems
            InitializeModSystems(planet);
        }

        private void InitializeModSystems(PlanetWrapper planet)
        {
            // Your mod initialization logic
            _log.Info("🔧 Mod systems initialized");
        }
    }
}
```

### **4. Common Patterns**

#### **Building Monitoring**
```csharp
EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
    SDKEventConstants.BuildingSpawned,
    evt =>
    {
        var building = evt.Building; // Building wrapper
        _log.Info($"Building spawned: {building.BuildingType.Name}");
    }
);
```

#### **Climate Monitoring** 
```csharp
// Monitor atmosphere changes
var planet = evt.PlanetWrapper;
var atmosphere = planet.Atmosphere;

_log.Info($"Temperature: {atmosphere.Temperature:F2}K");
_log.Info($"Pressure: {atmosphere.Pressure:F2} kPa");
```

#### **Harmony Patches (When SDK Insufficient)**
```csharp
using HarmonyLib;

[HarmonyPatch(typeof(BaseGameWrapper), nameof(BaseGameWrapper.OnFinishLoading))]
public static class GameLoadPatch
{
    [HarmonyPostfix]
    public static void Postfix()
    {
        // SDK-compatible patch
        YourModPlugin.OnGameLoadComplete();
    }
}
```

---

## 📋 **Dependencies Included Automatically**

The `sdkDLL.props` import provides **ALL** dependencies:

### **✅ SDK Components (9 DLLs)**
- `PerAspera.Core.dll` - LogAspera, utilities
- `PerAspera.Core.IL2CppExtensions.dll` - IL2CPP helpers
- `PerAspera.GameAPI.dll` - Main game APIs
- `PerAspera.GameAPI.Wrappers.dll` - Type-safe wrappers
- `PerAspera.GameAPI.Events.dll` - Enhanced Events v2.0
- `PerAspera.GameAPI.Climate.dll` - Atmosphere systems
- `PerAspera.GameAPI.Commands.dll` - Command framework
- `PerAspera.GameAPI.Overrides.dll` - Runtime modifications
- `PerAspera.ModSDK.dll` - Complete mod SDK

### **✅ Framework Dependencies**
- **Unity Engine 2020.3.49 LTS** - UnityEngine.*, Input, MonoBehaviour
- **BepInEx 6 IL2CPP** - BasePlugin, ManualLogSource, lifecycle
- **HarmonyLib** - Runtime patching ([HarmonyPatch], etc.)
- **IL2CPP Interop** - Il2CppSystem.*, native conversions

### **✅ NuGet Packages**
- **BepInEx.Unity.IL2CPP** (6.0.0+) - Core framework
- **UnityEngine.Modules** (2020.3.49) - Unity APIs

---

## 🚀 **Advanced Patterns**

### **Multi-Component Architecture**
```csharp
public class YourModPlugin : BasePlugin
{
    private BuildingManager _buildingManager;
    private ClimateMonitor _climateMonitor;
    private UIController _uiController;

    private void InitializeModSystems(PlanetWrapper planet)
    {
        _buildingManager = new BuildingManager(planet, _log);
        _climateMonitor = new ClimateMonitor(planet, _log); 
        _uiController = new UIController(_log);

        _log.Info("🔧 All subsystems initialized");
    }
}

// Separate component classes
public class BuildingManager
{
    private readonly PlanetWrapper _planet;
    private readonly LogAspera _log;

    public BuildingManager(PlanetWrapper planet, LogAspera log)
    {
        _planet = planet;
        _log = log;
        
        // Subscribe to building events
        EnhancedEvents.Subscribe<BuildingSpawnedNativeEvent>(
            SDKEventConstants.BuildingSpawned,
            OnBuildingSpawned
        );
    }

    private void OnBuildingSpawned(BuildingSpawnedNativeEvent evt)
    {
        var building = evt.Building;
        _log.Info($"Managing new building: {building.BuildingType.Name}");
    }
}
```

### **Configuration System**
```csharp
using BepInEx.Configuration;

public class YourModPlugin : BasePlugin
{
    private ConfigEntry<bool> _enableLogging;
    private ConfigEntry<float> _updateInterval;

    public override void Load()
    {
        // BepInEx configuration
        _enableLogging = Config.Bind("General", "EnableLogging", true, "Enable detailed logging");
        _updateInterval = Config.Bind("General", "UpdateInterval", 5.0f, "Update interval in seconds");

        _log.Info($"Config - Logging: {_enableLogging.Value}, Interval: {_updateInterval.Value}s");
    }
}
```

---

## ✅ **Validation Checklist**

Before building your mod, verify:

- [ ] ✅ **Single dependency**: Only `<Import Project="..\..\SDK\sdkDLL.props" />`
- [ ] ✅ **No manual DLL references**: No `<Reference Include="...">` entries
- [ ] ✅ **SDK namespaces**: Using `PerAspera.GameAPI.*` imports  
- [ ] ✅ **Enhanced Events**: Using `EnhancedEvents.Subscribe<T>()`
- [ ] ✅ **Wrapper types**: Using `BaseGameWrapper`, `PlanetWrapper`, etc.
- [ ] ✅ **LogAspera**: Using SDK logging instead of BepInEx directly
- [ ] ✅ **Harmony via wrappers**: Patching wrapper classes, not native

---

## 🎯 **Next Steps**

1. **Build and test**: `dotnet build -c Release`
2. **Deploy to game**: Should auto-deploy if configured
3. **Check logs**: Monitor BepInEx logs for any issues
4. **Iterate**: Use the "essuie-glace" development approach

### **Resources**
- **[First Mod Tutorial](getting-started/First-Mod.md)** - Complete walkthrough
- **[Wrapper Architecture](Wrapper-Architecture-Reference.md)** - Deep dive into wrappers
- **[Event System](Event-System-Quick-Reference.md)** - All available events
- **[Performance Guide](advanced/Performance.md)** - Optimization tips

---

**🚀 This template provides everything needed for modern Per Aspera modding with zero dependency conflicts!**