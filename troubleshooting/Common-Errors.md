# 🐛 Common Errors & Quick Fixes

Quick solutions to the most frequent Per Aspera modding issues. Find your error, get the fix, back to modding!

## 🔍 Quick Error Lookup

**Jump to your error:**
- [Mod Not Loading](#mod-not-loading)
- [NullReferenceException](#nullreferenceexception)
- [IL2CPP Type Conversion Errors](#il2cpp-type-conversion-errors)
- [BepInEx Not Starting](#bepinex-not-starting)
- [Build/Compilation Errors](#buildcompilation-errors)
- [Game Crashes on Startup](#game-crashes-on-startup)
- [Events Not Firing](#events-not-firing)
- [DLL Dependencies Missing](#dll-dependencies-missing)

---

## Mod Not Loading

### Symptom
```
BepInEx/plugins/MyMod.dll exists
But no logs from MyMod in LogOutput.log
```

### Quick Diagnosis

**1. Check DLL location**
```powershell
# DLL must be directly in plugins/ (not subdirectory)
❌ BepInEx/plugins/MyMod/MyMod.dll          # WRONG
✅ BepInEx/plugins/MyMod.dll                # CORRECT
```

**2. Verify BepInPlugin attribute**
```csharp
// Must have this attribute!
[BepInPlugin("com.yourname.modname", "Mod Name", "1.0.0")]
public class MyModPlugin : PerAsperaSDKPlugin  // or BasePlugin
```

**3. Check LogOutput.log for errors**
```
[Error  :   BepInEx] Could not load [MyMod] because it could not be found
[Error  :   BepInEx] Could not load [MyMod] because of missing dependency [X]
```

### Common Causes & Fixes

| Cause | Fix |
|-------|-----|
| **Wrong .NET version** | Build for `.NET 6.0` or `.NET Framework 4.7.2` |
| **Missing dependency** | Copy SDK DLLs to `plugins/` folder |
| **Wrong base class** | Use `PerAsperaSDKPlugin` or `BasePlugin` |
| **No BepInPlugin attribute** | Add `[BepInPlugin(...)]` above class |
| **DLL in subdirectory** | Move to root `plugins/` folder |
| **Antivirus blocking** | Add exception for BepInEx folder |

### Verification Steps

1. **Check BepInEx loaded:**
```
[Message:   BepInEx] BepInEx 6.0.0 - Per Aspera (12/17/2025)
```

2. **Check plugin discovery:**
```
[Info   :   BepInEx] Loading [MyMod 1.0.0]
```

3. **Check plugin start:**
```
[Info   :    MyMod] Plugin loaded!  # Your log message
```

---

## NullReferenceException

### Symptom
```
NullReferenceException: Object reference not set to an instance of an object
  at MyMod.OnGameLoaded() in MyMod.cs:line 42
```

### Common Causes

**1. Accessing game objects before initialization**
```csharp
❌ WRONG - Too early!
public override void Load()
{
    var planet = SDK.Game.CurrentPlanet;  // NULL - game not loaded yet!
}

✅ CORRECT - Wait for game ready
protected override void OnSDKReady()
{
    SDK.Events.GameFullyLoaded += () => 
    {
        var planet = SDK.Game.CurrentPlanet;  // ✅ Safe now
    };
}
```

**2. Missing null checks**
```csharp
❌ WRONG
var buildings = SDK.Game.CurrentPlanet.Buildings;  // CurrentPlanet might be null!

✅ CORRECT
var planet = SDK.Game.CurrentPlanet;
if (planet == null)
{
    Logger.LogWarning("Planet not loaded yet");
    return;
}
var buildings = planet.Buildings;
```

**3. IL2CPP wrapper not initialized**
```csharp
❌ WRONG
Building building = someNativeObject;  // Direct cast fails

✅ CORRECT
Building building = new Building(someNativeObject);  // Wrap it
```

### Quick Fix Pattern

**Always use this pattern:**
```csharp
protected override void OnSDKReady()
{
    SDK.Events.GameFullyLoaded += () =>
    {
        try
        {
            var planet = SDK.Game.CurrentPlanet;
            if (planet == null)
            {
                Logger.LogError("Planet is null!");
                return;
            }
            
            // Safe to access planet now
            var buildings = planet.Buildings;
            
        }
        catch (Exception ex)
        {
            Logger.LogError($"Error: {ex.Message}\n{ex.StackTrace}");
        }
    };
}
```

---

## IL2CPP Type Conversion Errors

### Symptom
```
InvalidCastException: Cannot cast from source type to destination type
  at MyMod.ProcessBuilding()
  
or

ArgumentException: Value does not fall within the expected range
```

### Common IL2CPP Issues

**1. String conversions**
```csharp
❌ WRONG
string name = nativeString;  // IL2CPP uses Il2CppSystem.String

✅ CORRECT
using Il2CppSystem;
String il2cppStr = nativeString;
string managedStr = il2cppStr.ToString();  // Convert to C# string
```

**2. Array conversions**
```csharp
❌ WRONG
var items = nativeArray.ToArray();  // Not directly compatible

✅ CORRECT
using Il2CppSystem.Collections.Generic;
List<Item> items = new List<Item>();
for (int i = 0; i < nativeList.Count; i++)
{
    items.Add(nativeList[i]);
}
```

**3. Type confusion - CRITICAL!**
```csharp
❌ WRONG
Type buildingType = typeof(Building);  // Ambiguous - which Type?

✅ CORRECT
System.Type buildingType = typeof(Building);  // Always specify System.Type
```

**See also:** [IL2CPP Interop Guide](IL2CPP-Errors.md) for comprehensive type mapping.

---

## BepInEx Not Starting

### Symptom
```
Game launches normally
No BepInEx folder created
No LogOutput.log file
```

### Quick Diagnosis

**1. Check BepInEx files present:**
```
Per Aspera/
├── winhttp.dll              # ✅ Must exist
├── doorstop_config.ini      # ✅ Must exist
└── BepInEx/
    └── core/                # ✅ Must have files
```

**2. Verify correct version:**
- Download: **BepInEx 6.x IL2CPP** (NOT Mono!)
- For: **x64 Unity**
- Link: https://github.com/BepInEx/BepInEx/releases

**3. Check doorstop_config.ini:**
```ini
[UnityDoorstop]
enabled=true                          # Must be true!
targetAssembly=BepInEx\core\BepInEx.IL2CPP.dll
```

### Common Causes & Fixes

| Cause | Fix |
|-------|-----|
| **Wrong BepInEx version** | Download IL2CPP version (not Mono) |
| **Missing winhttp.dll** | Re-extract BepInEx completely |
| **Antivirus deleted files** | Add game folder to exclusions, re-install |
| **Game updated** | Re-install BepInEx after game updates |
| **Doorstop disabled** | Set `enabled=true` in doorstop_config.ini |

### Force BepInEx to Run

```powershell
# Run from Per Aspera folder
.\PerAspera.exe --doorstop-enable true
```

Check console output for errors.

---

## Build/Compilation Errors

### Error: "Cannot find type or namespace"

```
error CS0246: The type or namespace name 'PerAspera' could not be found
```

**Cause:** Missing SDK references

**Fix:** Add SDK references to `.csproj`:
```xml
<ItemGroup>
  <Reference Include="PerAspera.ModSDK">
    <HintPath>..\..\SDK\bin\Debug\net6.0\PerAspera.ModSDK.dll</HintPath>
  </Reference>
</ItemGroup>
```

### Error: "Assembly reference missing"

```
error CS0012: The type 'BasePlugin' is defined in an assembly that is not referenced
```

**Cause:** Missing BepInEx package

**Fix:** Add NuGet package:
```xml
<PackageReference Include="BepInEx.Unity.IL2CPP" Version="6.0.0-*" />
```

Then restore:
```powershell
dotnet restore
```

### Error: "Method not found"

```
error CS0103: The name 'SDK' does not exist in the current context
```

**Cause:** Not inheriting from `PerAsperaSDKPlugin`

**Fix:**
```csharp
❌ WRONG
public class MyMod : BasePlugin

✅ CORRECT
public class MyMod : PerAsperaSDKPlugin
```

### Error: "Could not copy DLL"

```
error MSB3021: Unable to copy file "bin\Debug\MyMod.dll" to "BepInEx\plugins\MyMod.dll"
```

**Cause:** Game is running or DLL in use

**Fix:**
1. Close Per Aspera
2. Clean build: `dotnet clean`
3. Rebuild: `dotnet build`

---

## Game Crashes on Startup

### Symptom
```
Game starts loading
Black screen or immediate crash
No error message visible
```

### Quick Diagnosis

**1. Check BepInEx/LogOutput.log (last lines):**
```
[Fatal  :   Unity] NullReferenceException
[Fatal  :BepInEx] Fatal error in plugin [MyMod]
```

**2. Try safe mode:**
```powershell
# Rename plugins folder temporarily
cd "C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx"
ren plugins plugins_backup

# Launch game - if it works, problem is in a mod
# Restore folder and test mods one-by-one
ren plugins_backup plugins
```

### Common Causes

| Cause | Fix |
|-------|-----|
| **Harmony patch error** | Check patch targets exist in game version |
| **Mod initialization crash** | Add try/catch in Load() and OnSDKReady() |
| **Infinite loop in Update()** | Remove Update() loops, use events instead |
| **Conflicting mods** | Test each mod individually |
| **Outdated mod** | Update for current game version |

### Crash in Harmony Patch

```csharp
// Add safety checks
[HarmonyPatch(typeof(Building), "UpdateProduction")]
[HarmonyPrefix]
public static bool PatchPrefix(Building __instance)
{
    try
    {
        if (__instance == null)
        {
            LogAspera.LogWarning("Building instance is null");
            return true;  // Skip patch
        }
        
        // Your patch code
        
        return true;
    }
    catch (Exception ex)
    {
        LogAspera.LogError($"Patch error: {ex.Message}");
        return true;  // Let original method run
    }
}
```

---

## Events Not Firing

### Symptom
```
Event subscribed but handler never called
No logs from event handler
```

### Quick Diagnosis

**1. Check subscription timing:**
```csharp
❌ TOO EARLY
public override void Load()
{
    SDK.Events.BuildingSpawned += Handler;  // SDK not ready!
}

✅ CORRECT
protected override void OnSDKReady()
{
    SDK.Events.BuildingSpawned += Handler;  // SDK initialized
}
```

**2. Verify event name:**
```csharp
❌ WRONG
SDK.Events.OnBuildingSpawned += Handler;  // No "On" prefix!

✅ CORRECT
SDK.Events.BuildingSpawned += Handler;
```

**3. Check handler signature:**
```csharp
❌ WRONG - Wrong parameter type
private void Handler(object data) { }

✅ CORRECT - Matches event signature
private void Handler(BuildingEventData data) { }
```

### Common Event Mistakes

```csharp
// ❌ WRONG - Subscribes but immediately unsubscribes!
SDK.Events.GameFullyLoaded += OnGameLoaded;
SDK.Events.GameFullyLoaded -= OnGameLoaded;  // Oops!

// ❌ WRONG - Handler signature doesn't match
SDK.Events.MartianDayChanged += () => { };  // Expects MartianDayEventData parameter!

// ✅ CORRECT
SDK.Events.MartianDayChanged += (data) => 
{
    Logger.LogInfo($"Day {data.TotalDays}");
};

// ✅ ALSO CORRECT
SDK.Events.MartianDayChanged += OnDayChanged;

private void OnDayChanged(MartianDayEventData data)
{
    Logger.LogInfo($"Day {data.TotalDays}");
}
```

### Debug Events

```csharp
protected override void OnSDKReady()
{
    Logger.LogInfo("Subscribing to events...");
    
    SDK.Events.BuildingSpawned += (data) =>
    {
        Logger.LogInfo($"✅ Event fired: BuildingSpawned - {data.BuildingName}");
    };
    
    Logger.LogInfo("Event subscription complete");
}
```

---

## DLL Dependencies Missing

### Symptom
```
[Error: BepInEx] Could not load [MyMod] because dependency [X] could not be found
```

### Quick Fix

**Copy ALL SDK DLLs to game:**
```powershell
# From SDK bin folder
copy F:\ModPeraspera\SDK\bin\Debug\net6.0\*.dll ^
  "C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx\plugins\"
```

**Required SDK DLLs:**
- `PerAspera.ModSDK.dll`
- `PerAspera.Core.dll`
- `PerAspera.GameAPI.dll`
- `PerAspera.GameAPI.Wrappers.dll`
- `PerAspera.GameAPI.Events.dll`
- (Any other PerAspera.* DLLs your mod uses)

### Verify Dependencies

```powershell
# Check what your DLL references
# Install: dotnet tool install -g ilspy-cli
ilspy MyMod.dll --referencedassemblies
```

---

## 🆘 Still Stuck?

### Debug Logging

Add comprehensive logging:
```csharp
protected override void OnSDKReady()
{
    Logger.LogInfo("=== MOD STARTUP DEBUG ===");
    Logger.LogInfo($"SDK Version: {SDK.Version}");
    Logger.LogInfo($"Game Version: {Application.version}");
    
    try
    {
        Logger.LogInfo("Subscribing to events...");
        SDK.Events.GameFullyLoaded += () =>
        {
            Logger.LogInfo("Game loaded event fired!");
            
            try
            {
                var planet = SDK.Game.CurrentPlanet;
                Logger.LogInfo($"Planet: {planet?.Name ?? "NULL"}");
            }
            catch (Exception ex)
            {
                Logger.LogError($"Planet access error: {ex}");
            }
        };
        
        Logger.LogInfo("Event subscription complete");
    }
    catch (Exception ex)
    {
        Logger.LogError($"Startup error: {ex}");
    }
    
    Logger.LogInfo("=== MOD STARTUP COMPLETE ===");
}
```

### Get Help

1. **Check logs:** `BepInEx/LogOutput.log` (last 50 lines)
2. **Enable verbose logging:** BepInEx/config/BepInEx.cfg → `LogLevels = All`
3. **Search documentation:** This wiki + [BepInEx Docs](https://docs.bepinex.dev)
4. **Ask agent:** Paste error message with context

---

## 📚 Related Guides

- **[Build Problems](Build-Problems.md)** - Compilation and reference errors in detail
- **[Runtime Issues](Runtime-Issues.md)** - Game crashes and performance problems
- **[IL2CPP Errors](IL2CPP-Errors.md)** - Complete IL2CPP type conversion guide
- **[Debugging Guide](../advanced/Debugging.md)** - Advanced debugging techniques

---

**Pro tip:** 90% of issues are fixed by:
1. ✅ Correct DLL location (root `plugins/`)
2. ✅ Proper SDK inheritance (`PerAsperaSDKPlugin`)
3. ✅ Event subscription in `OnSDKReady()`
4. ✅ Null checks before game object access
5. ✅ All SDK DLLs copied to game

Check these first!
