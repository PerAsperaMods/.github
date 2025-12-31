# 🎮 Your First Mod - 15 Minute Tutorial

Create a working Per Aspera mod from scratch! This tutorial teaches fundamental concepts while building a practical mod that logs game events and displays player statistics.

## 🎯 What You'll Build

**"Mars Statistics Mod"** - Displays real-time Mars terraforming progress:
- Track atmosphere composition
- Monitor temperature changes
- Log daily progress
- Display building count

**Skills learned:**
- ✅ SDK event system
- ✅ Game API access
- ✅ Logging and debugging
- ✅ Type-safe wrappers

---

## 📋 Prerequisites

- [x] Visual Studio 2022 installed
- [x] Per Aspera installed via Steam
- [x] BepInEx 6.x IL2CPP configured
- [x] ModSDK built and ready

**Not done?** → [Installation Guide](Installation.md)

---

## 🚀 Step 1: Create Project

### Using Command Line

```bash
# Navigate to your modding workspace
cd F:\ModPeraspera\Individual-Mods

# Create new project
dotnet new classlib -n MarsStatistics
cd MarsStatistics
```

### Using Visual Studio

1. **File** → **New** → **Project**
2. Select **Class Library (.NET 6)**
3. Name: `MarsStatistics`
4. Location: `F:\ModPeraspera\Individual-Mods\`
5. Click **Create**

---

## 🔧 Step 2: Configure Project

Replace `MarsStatistics.csproj` content:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <AssemblyName>MarsStatistics</AssemblyName>
    <RootNamespace>MarsStatistics</RootNamespace>
    <LangVersion>latest</LangVersion>
    <Nullable>enable</Nullable>
  </PropertyGroup>

  <!-- 🎯 SDK-ONLY TEMPLATE - SINGLE DEPENDENCY -->
  <!-- Import Per Aspera SDK (includes ALL dependencies automatically) -->
  <Import Project="$(MSBuildThisFileDirectory)..\..\SDK\sdkDLL.props" />

  <!-- Auto-deploy to game on build -->
  <Target Name="DeployToGame" AfterTargets="Build">
    <Copy 
      SourceFiles="$(TargetPath)" 
      DestinationFolder="C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx\plugins\" 
      SkipUnchangedFiles="true" />
  </Target>

</Project>
```

**🚀 SDK-Only Architecture Benefits:**
- ✅ **Zero duplication** - No manual DLL references needed
- ✅ **Auto-updates** - SDK changes propagate automatically  
- ✅ **Simplified maintenance** - Single source of truth
- ✅ **Performance optimized** - Unified dependency management

### 💡 About sdkDLL.props (Updated Architecture)

The `Import Project="$(MSBuildThisFileDirectory)..\..\SDK\sdkDLL.props"` line automatically includes ALL Per Aspera modding dependencies:

✅ **SDK Components (9 DLLs included automatically):**
- `PerAspera.Core.dll` - Core utilities and LogAspera
- `PerAspera.Core.IL2CppExtensions.dll` - IL2CPP type extensions  
- `PerAspera.GameAPI.dll` - Main game API access
- `PerAspera.GameAPI.Wrappers.dll` - Safe type wrappers (Planet, Universe, BaseGame)
- `PerAspera.GameAPI.Events.dll` - Enhanced Events v2.0 system
- `PerAspera.GameAPI.Climate.dll` - Climate and atmosphere systems
- `PerAspera.GameAPI.Commands.dll` - Command system integration
- `PerAspera.GameAPI.Overrides.dll` - Runtime modification helpers
- `PerAspera.ModSDK.dll` - Complete mod SDK

✅ **Framework Dependencies (included automatically):**
- **Unity Engine 2020.3.49 LTS** - Complete Unity API access
- **BepInEx 6 IL2CPP** - Plugin framework and lifecycle
- **HarmonyLib** - Runtime patching capabilities  
- **IL2CPP Interop** - Native type conversion

**🎯 Migration Note:** This replaces the old GameLibs approach - no manual DLL references needed!
- `PerAspera.GameAPI.Commands.dll` - Command system integration
- `PerAspera.GameAPI.Events.dll` - Event subscription system
- `PerAspera.GameAPI.Overrides.dll` - Runtime value modification
- `PerAspera.GameAPI.Wrappers.dll` - Safe type wrappers
- `PerAspera.ModSDK.dll` - Complete SDK framework

✅ **Benefits:**
- **Simplified setup** - One line vs manual references
- **Auto-updated** - Always uses current SDK version  
- **Consistent** - Same configuration across all mods
- **Maintainable** - Central SDK management

---

## 📝 Step 3: Write the Plugin

Delete `Class1.cs` and create `MarsStatisticsPlugin.cs`:

```csharp
using BepInEx;
using BepInEx.Unity.IL2CPP;
using BepInEx.Logging;
using PerAspera.ModSDK;
using PerAspera.GameAPI.Wrappers;
using PerAspera.GameAPI.Events.SDK;
using PerAspera.GameAPI.Events.Data;

namespace MarsStatistics;

/// <summary>
/// Tracks and displays Mars terraforming statistics in real-time.
/// </summary>
[BepInPlugin(MyPluginInfo.PLUGIN_GUID, MyPluginInfo.PLUGIN_NAME, MyPluginInfo.PLUGIN_VERSION)]
public class MarsStatisticsPlugin : PerAsperaSDKPlugin
{
    // Statistics tracking
    private int _totalDaysPassed = 0;
    private float _currentTemperature = 0f;
    private int _totalBuildings = 0;

    protected override void OnSDKReady()
    {
        Logger.LogInfo("🚀 Mars Statistics Mod loaded!");
        Logger.LogInfo("Starting terraforming monitoring...");

        // Subscribe to game initialization using Enhanced Events v2.0
        EnhancedEvents.Subscribe<GameFullyLoadedEvent>(SDKEventConstants.GameFullyLoaded, OnGameLoaded);
        
        // Subscribe to daily events
        EnhancedEvents.Subscribe<MartianDayEvent>(SDKEventConstants.MartianDayChanged, OnDayChanged);
        
        // Subscribe to climate updates
        EnhancedEvents.Subscribe<ClimateAnalysisEvent>(SDKEventConstants.ClimateAnalysisComplete, OnClimateUpdate);
        
        // Subscribe to building events
        EnhancedEvents.Subscribe<BuildingSpawnedEvent>(SDKEventConstants.BuildingSpawned, OnBuildingSpawned);
        EnhancedEvents.Subscribe<BuildingDespawnedEvent>(SDKEventConstants.BuildingDespawned, OnBuildingDespawned);
    }

    private void OnGameLoaded(GameFullyLoadedEvent eventData)
    {
        Logger.LogInfo("========================");
        Logger.LogInfo("🌍 Game Loaded - Initializing Statistics");
        Logger.LogInfo("========================");
        
        // Initialize statistics
        _totalDaysPassed = 0;
        _totalBuildings = CountBuildings();
        
        Logger.LogInfo($"📊 Starting Buildings: {_totalBuildings}");
    }

    private void OnDayChanged(MartianDayEvent eventData)
    {
        _totalDaysPassed++;
        
        Logger.LogInfo("========================");
        Logger.LogInfo($"📅 Martian Day #{eventData.TotalDays} Complete");
        Logger.LogInfo($"   Season: {eventData.Season}");
        Logger.LogInfo($"   Year: {eventData.Year}, Day: {eventData.DayOfYear}");
        Logger.LogInfo("========================");
        
        // Every 10 days, show full statistics
        if (_totalDaysPassed % 10 == 0)
        {
            DisplayFullStatistics();
        }
    }

    private void OnClimateUpdate(ClimateAnalysisEvent eventData)
    {
        _currentTemperature = eventData.Temperature;
        
        Logger.LogInfo("🌡️ Climate Update:");
        Logger.LogInfo($"   Temperature: {eventData.Temperature:F2}°C");
        Logger.LogInfo($"   Pressure: {eventData.Pressure:F2} Pa");
        Logger.LogInfo($"   Oxygen: {eventData.OxygenLevel:F2}%");
        
        // Check terraforming milestones
        CheckTerraformingMilestones(eventData);
    }

    private void OnBuildingSpawned(BuildingSpawnedEvent eventData)
    {
        _totalBuildings++;
        Logger.LogInfo($"🏗️ Building Constructed: {eventData.BuildingName}");
        Logger.LogInfo($"   Total Buildings: {_totalBuildings}");
    }

    private void OnBuildingDespawned(BuildingDespawnedEvent eventData)
    {
        _totalBuildings--;
        Logger.LogInfo($"💥 Building Removed: {eventData.BuildingName}");
        Logger.LogInfo($"   Total Buildings: {_totalBuildings}");
    }

    private void DisplayFullStatistics()
    {
        Logger.LogInfo("════════════════════════════════");
        Logger.LogInfo("📊 MARS TERRAFORMING STATISTICS");
        Logger.LogInfo("════════════════════════════════");
        Logger.LogInfo($"🕐 Days Passed: {_totalDaysPassed}");
        Logger.LogInfo($"🏗️ Total Buildings: {_totalBuildings}");
        Logger.LogInfo($"🌡️ Current Temperature: {_currentTemperature:F2}°C");
        
        // Access planet data directly via SDK
        try
        {
            var planet = SDK.Game.CurrentPlanet;
            if (planet != null)
            {
                var atmosphere = planet.GetAtmosphereData();
                Logger.LogInfo($"🌫️ Atmosphere Composition:");
                Logger.LogInfo($"   - Pressure: {atmosphere.Pressure:F2} Pa");
                Logger.LogInfo($"   - Temperature: {atmosphere.Temperature:F2}°C");
                
                foreach (var gas in atmosphere.Gases)
                {
                    Logger.LogInfo($"   - {gas.Name}: {gas.Percentage:F2}%");
                }
            }
        }
        catch (System.Exception ex)
        {
            Logger.LogWarning($"⚠️ Could not retrieve planet data: {ex.Message}");
        }
        
        Logger.LogInfo("════════════════════════════════");
    }

    private void CheckTerraformingMilestones(ClimateEventData data)
    {
        // Water melting point
        if (data.Temperature > 0f && data.Temperature < 1f)
        {
            Logger.LogInfo("🎉 MILESTONE: Water can now exist in liquid form!");
        }
        
        // Breathable atmosphere threshold
        if (data.OxygenLevel > 20f && data.OxygenLevel < 21f)
        {
            Logger.LogInfo("🎉 MILESTONE: Atmosphere approaching breathable oxygen levels!");
        }
        
        // Earth-like pressure
        if (data.Pressure > 101000f && data.Pressure < 102000f)
        {
            Logger.LogInfo("🎉 MILESTONE: Atmospheric pressure reaching Earth-like levels!");
        }
    }

    private int CountBuildings()
    {
        try
        {
            var planet = SDK.Game.CurrentPlanet;
            return planet?.Buildings.Count() ?? 0;
        }
        catch
        {
            return 0;
        }
    }

    public override bool Unload()
    {
        Logger.LogInfo("👋 Mars Statistics Mod unloading...");
        
        // Unsubscribe from events
        SDK.Events.GameFullyLoaded -= OnGameLoaded;
        SDK.Events.MartianDayChanged -= OnDayChanged;
        SDK.Events.ClimateAnalysisComplete -= OnClimateUpdate;
        SDK.Events.BuildingSpawned -= OnBuildingSpawned;
        SDK.Events.BuildingDespawned -= OnBuildingDespawned;
        
        return base.Unload();
    }
}
```

---

## 🔨 Step 4: Build the Mod

### Visual Studio

1. **Build** → **Build Solution** (or `Ctrl+Shift+B`)
2. Check **Output** window for success message
3. DLL auto-deploys to `BepInEx/plugins/`

### Command Line

```bash
dotnet build
```

**Expected output:**
```
Build succeeded.
    0 Warning(s)
    0 Error(s)
Copying MarsStatistics.dll to game plugins folder...
```

---

## 🎮 Step 5: Test the Mod

### Launch Game

1. **Start Per Aspera** from Steam
2. **Wait** for main menu
3. **Load** or start a new game

### Check Logs

Open `BepInEx/LogOutput.log`:

```
[Info   : Mars Statistics] 🚀 Mars Statistics Mod loaded!
[Info   : Mars Statistics] Starting terraforming monitoring...
[Info   : Mars Statistics] ========================
[Info   : Mars Statistics] 🌍 Game Loaded - Initializing Statistics
[Info   : Mars Statistics] ========================
[Info   : Mars Statistics] 📊 Starting Buildings: 1
```

### Watch Events

As you play:
- **Build structures** → See building count updates
- **Fast-forward time** → See daily reports every 10 days
- **Terraform** → Watch climate milestone notifications

---

## 🎯 Understanding the Code

### Key SDK Concepts

#### 1. Plugin Base Class
```csharp
public class MarsStatisticsPlugin : PerAsperaSDKPlugin
```
- Inherit from `PerAsperaSDKPlugin` for automatic SDK initialization
- Override `OnSDKReady()` to start your mod logic

#### 2. Event Subscription
```csharp
SDK.Events.MartianDayChanged += OnDayChanged;
```
- Subscribe to typed events with lambda or method
- Automatic event cleanup on mod unload
- Type-safe event data structures

#### 3. Game API Access
```csharp
var planet = SDK.Game.CurrentPlanet;
var atmosphere = planet.GetAtmosphereData();
```
- Type-safe wrappers around IL2CPP game objects
- IntelliSense support in Visual Studio
- Graceful null handling

#### 4. Logging
```csharp
Logger.LogInfo("Message");    // Informational
Logger.LogWarning("Warning"); // Warnings
Logger.LogError("Error");     // Errors
```
- Automatic formatting in BepInEx log
- Filterable by log level
- Performance-optimized

---

## 🚀 Next Steps

### Enhance Your Mod

**Easy additions:**
1. **Add more statistics**:
   ```csharp
   SDK.Events.ResourceChanged += OnResourceChanged;
   ```

2. **Create CSV export**:
   ```csharp
   private void ExportStatistics()
   {
       File.AppendAllText("statistics.csv", $"{_totalDays},{_temperature}\n");
   }
   ```

3. **Add configuration**:
   ```csharp
   private ConfigEntry<int> _reportInterval;
   
   protected override void OnSDKReady()
   {
       _reportInterval = Config.Bind("General", "ReportInterval", 10, 
           "Days between full statistics reports");
   }
   ```

### Learn More

- **[Event System Guide](../sdk/Events.md)** - All available events
- **[Game Wrappers](../sdk/Wrappers.md)** - Access more game data
- **[Building Tutorial](../tutorials/Buildings.md)** - Modify buildings
- **[Configuration](../advanced/Configuration.md)** - Add user settings

---

## 🐛 Common Issues

### Mod Not Loading

**Check:**
1. DLL copied to `BepInEx/plugins/`?
2. BepInEx log shows plugin GUID?
3. All SDK DLLs present?

### Events Not Firing

**Check:**
1. Subscribed in `OnSDKReady()`?
2. Game fully loaded before subscribing?
3. Event names spelled correctly?

### NullReferenceException

**Check:**
1. Null-check SDK objects: `planet != null`
2. Game state loaded before accessing data?
3. Event data valid before use?

---

## ✅ Congratulations!

You've created your first Per Aspera mod! 🎉

**You learned:**
- ✅ Project setup and configuration
- ✅ SDK event system
- ✅ Game API access patterns
- ✅ Logging and debugging
- ✅ Auto-deployment workflow

**Next tutorials:**
- **[Modifying Buildings](../tutorials/Buildings.md)** - Change building behavior
- **[YAML Datamodel](../content/YAML-Datamodel.md)** - Edit game data files
- **[Advanced Events](../sdk/Events.md)** - Deep dive into event system

---

**Questions?** Check our [FAQ](../community/FAQ.md) or [troubleshooting guide](../troubleshooting/Common-Errors.md).
