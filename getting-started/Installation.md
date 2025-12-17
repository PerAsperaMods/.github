# 🔧 Installation & Setup Guide

Complete guide to setting up the Per Aspera modding development environment.

## 📋 Prerequisites

### Required Software

| Software | Version | Purpose |
|----------|---------|---------|
| **Visual Studio 2022** | Community or higher | C# development & debugging |
| **Per Aspera** | 1.5.x | Base game (Steam) |
| **BepInEx 6.x IL2CPP** | Latest | Modding framework |
| **.NET 6.0 SDK** | Latest | Build system |
| **Git** | Latest | Version control (optional) |

### System Requirements
- **OS:** Windows 10/11 (64-bit)
- **RAM:** 8 GB minimum, 16 GB recommended
- **Storage:** 5 GB free space for SDK and tools

---

## 🚀 Step 1: Install Visual Studio 2022

1. **Download** Visual Studio 2022 Community Edition:
   - Visit: https://visualstudio.microsoft.com/downloads/
   
2. **Select Workloads** during installation:
   - ✅ `.NET desktop development`
   - ✅ `Game development with Unity` (for IL2CPP debugging)
   
3. **Individual Components** (optional but recommended):
   - ✅ `Git for Windows`
   - ✅ `GitHub Extension for Visual Studio`

---

## 🎮 Step 2: Locate Per Aspera Game Files

1. **Find your Steam installation**:
   ```
   Default: C:\Program Files (x86)\Steam\steamapps\common\Per Aspera
   ```

2. **Important directories**:
   ```
   Per Aspera/
   ├── BepInEx/                  # Modding framework (after BepInEx install)
   │   ├── plugins/              # Your compiled mods go here
   │   ├── config/               # Mod configuration files
   │   └── LogOutput.log         # Debug output
   ├── PerAspera_Data/
   │   ├── Managed/              # Game assemblies (IL2CPP)
   │   ├── il2cpp_data/          # IL2CPP runtime
   │   └── datamodel/            # YAML game data
   └── PerAspera.exe             # Game executable
   ```

---

## 📦 Step 3: Install BepInEx 6 IL2CPP

### Automatic Installation (Recommended)

1. **Download** BepInEx 6.x IL2CPP Unity:
   - Visit: https://github.com/BepInEx/BepInEx/releases
   - Get: `BepInEx_UnityIL2CPP_x64_[version].zip`

2. **Extract** to game directory:
   ```bash
   # Extract everything to:
   C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\
   ```

3. **Run game once** to generate configuration:
   - Launch Per Aspera from Steam
   - Wait for main menu to load
   - Close game
   - Verify `BepInEx/LogOutput.log` exists

### Verify Installation

Check that these folders were created:
```
Per Aspera/
├── BepInEx/
│   ├── config/
│   ├── core/
│   ├── plugins/
│   ├── interop/          # ← Important: IL2CPP assemblies
│   └── unity-libs/
├── doorstop_config.ini   # ← BepInEx loader config
└── winhttp.dll           # ← BepInEx entry point
```

---

## 🛠️ Step 4: Install Per Aspera ModSDK

### Option A: Using Git (Recommended)

```bash
# Clone the SDK repository
cd F:\ModPeraspera  # Or your preferred location
git clone https://github.com/PerAsperaMods/PerAspera-SDK.git SDK

# Navigate to SDK folder
cd SDK

# Build the SDK
.\Build-SDK.ps1
```

### Option B: Download ZIP

1. Download SDK from: https://github.com/PerAsperaMods/PerAspera-SDK/releases
2. Extract to: `F:\ModPeraspera\SDK\`
3. Open PowerShell in SDK folder
4. Run: `.\Build-SDK.ps1`

### Verify SDK Installation

After building, you should have:
```
SDK/
├── bin/
│   └── Debug/
│       ├── PerAspera.ModSDK.dll          # Main SDK
│       ├── PerAspera.Core.dll            # Core utilities
│       ├── PerAspera.GameAPI.*.dll       # API packages
│       └── ...
└── PerAspera.SDK.sln                     # Solution file
```

---

## 🔧 Step 5: Configure Development Environment

### 1. Set Game Path

Create `modding-config.json` in your project root:

```json
{
  "gamePath": "C:\\Program Files (x86)\\Steam\\steamapps\\common\\Per Aspera",
  "bepInExPluginsPath": "C:\\Program Files (x86)\\Steam\\steamapps\\common\\Per Aspera\\BepInEx\\plugins",
  "sdkVersion": "1.1.0",
  "autoDeployOnBuild": true
}
```

### 2. Visual Studio Settings

Open Visual Studio 2022:
1. **Tools** → **Options** → **NuGet Package Manager**
2. Add package source:
   - Name: `Per Aspera Local`
   - Source: `F:\ModPeraspera\LocalNuget`
   
3. **Build** → **Output**:
   - Set verbosity to `Detailed` for better debugging

### 3. Configure BepInEx Logging

Edit `BepInEx/config/BepInEx.cfg`:

```ini
[Logging.Console]
Enabled = true

[Logging.Disk]
Enabled = true
LogLevels = All

[Preloader]
ApplyRuntimePatches = true
```

---

## ✅ Step 6: Verify Installation

### Quick Test Mod

1. **Create new project**:
   ```bash
   cd F:\ModPeraspera
   dotnet new classlib -n TestMod
   cd TestMod
   ```

2. **Edit TestMod.csproj**:
   ```xml
   <Project Sdk="Microsoft.NET.Sdk">
     <PropertyGroup>
       <TargetFramework>net6.0</TargetFramework>
       <AssemblyName>TestMod</AssemblyName>
       <RootNamespace>TestMod</RootNamespace>
     </PropertyGroup>
     
     <ItemGroup>
       <PackageReference Include="BepInEx.Unity.IL2CPP" Version="6.0.0-*" />
       <PackageReference Include="PerAspera.ModSDK" Version="1.1.0" />
     </ItemGroup>
   </Project>
   ```

3. **Create TestMod.cs**:
   ```csharp
   using BepInEx;
   using BepInEx.Logging;
   using PerAspera.ModSDK;

   namespace TestMod;

   [BepInPlugin("com.example.testmod", "Test Mod", "1.0.0")]
   public class TestModPlugin : PerAsperaSDKPlugin
   {
       protected override void OnSDKReady()
       {
           Logger.LogInfo("✅ Test Mod loaded successfully!");
           
           SDK.Events.GameFullyLoaded += () =>
           {
               Logger.LogInfo("🎮 Game fully loaded!");
           };
       }
   }
   ```

4. **Build**:
   ```bash
   dotnet build
   ```

5. **Deploy**:
   ```bash
   copy bin\Debug\net6.0\TestMod.dll "C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\BepInEx\plugins\"
   ```

6. **Test**:
   - Launch Per Aspera
   - Check `BepInEx/LogOutput.log`
   - Look for: `✅ Test Mod loaded successfully!`

---

## 🐛 Troubleshooting

### BepInEx Not Loading

**Symptom:** No `BepInEx` folder created after running game

**Solutions:**
1. Verify `winhttp.dll` and `doorstop_config.ini` exist in game root
2. Run game as Administrator
3. Check antivirus isn't blocking BepInEx
4. Verify correct IL2CPP version (not Mono)

### DLL Not Loading

**Symptom:** Mod DLL copied but not appearing in log

**Solutions:**
1. Check DLL is in `BepInEx/plugins/` (not subdirectory)
2. Verify all dependencies are present
3. Check `BepInEx/LogOutput.log` for errors
4. Ensure DLL is built for `.NET 6.0` or `.NET Framework 4.7.2`

### Build Errors

**Symptom:** `Cannot find assembly reference`

**Solutions:**
1. Verify NuGet package sources configured
2. Restore packages: `dotnet restore`
3. Check `modding-config.json` paths are correct
4. Rebuild SDK: `cd SDK && .\Build-SDK.ps1`

### IL2CPP Type Errors

**Symptom:** `Il2CppSystem.* not found` or type conversion errors

**Solutions:**
1. Check `BepInEx/interop/` folder exists and contains assemblies
2. Add reference: `<PackageReference Include="Il2CppInterop.Runtime" Version="1.*" />`
3. Use proper IL2CPP type wrappers from SDK

---

## 🎯 Next Steps

✅ **Installation complete!** Ready to create your first mod?

- **[Your First Mod](First-Mod.md)** - 15-minute tutorial
- **[Project Structure](Project-Structure.md)** - Understanding the framework
- **[SDK Overview](../sdk/Overview.md)** - Learn the high-level API
- **[Common Tasks](../tutorials/Buildings.md)** - Practical examples

---

## 📚 Additional Resources

- **[BepInEx Documentation](https://docs.bepinex.dev/)**
- **[IL2CPP Interop Guide](../advanced/IL2CPP-Interop.md)**
- **[SDK API Reference](../sdk/Overview.md)**
- **[Troubleshooting Guide](../troubleshooting/Common-Errors.md)**

---

**Need help?** Check our [FAQ](../community/FAQ.md) or [Troubleshooting](../troubleshooting/Common-Errors.md) sections.
