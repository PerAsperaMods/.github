# 🔧 SDK DLL Configuration - sdkDLL.props Guide

This guide explains how to use the `sdkDLL.props` file for simplified Per Aspera SDK integration in your mod projects.

## 🎯 What is sdkDLL.props?

The `sdkDLL.props` file is a **MSBuild properties file** that automatically configures SDK references for your mod projects. Instead of manually adding each SDK DLL reference, you simply import this file.

## 📦 Included SDK Components

When you import `sdkDLL.props`, you automatically get access to **all** Per Aspera SDK components:

### Core Components
- **`PerAspera.Core.dll`** - Base utilities, logging, configuration
- **`PerAspera.Core.IL2CppExtensions.dll`** - IL2CPP type conversion helpers

### Game API Components  
- **`PerAspera.GameAPI.dll`** - Main game data access (planets, buildings, resources)
- **`PerAspera.GameAPI.Climate.dll`** - Atmosphere, temperature, weather systems
- **`PerAspera.GameAPI.Commands.dll`** - Command registration, hotkey integration
- **`PerAspera.GameAPI.Events.dll`** - Event subscription system (building spawned, day changed, etc.)
- **`PerAspera.GameAPI.Overrides.dll`** - Runtime value modification (production rates, costs, etc.)
- **`PerAspera.GameAPI.Wrappers.dll`** - Safe type wrappers, null protection

### Unified SDK
- **`PerAspera.ModSDK.dll`** - Complete SDK framework, plugin base classes

## 🚀 How to Use sdkDLL.props

### Step 1: Add Import to Your Project

In your mod's `.csproj` file, add this single line:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  
  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <!-- Your other properties -->
  </PropertyGroup>

  <!-- BepInEx References -->
  <ItemGroup>
    <PackageReference Include="BepInEx.Unity.IL2CPP" Version="6.0.0-*" />
    <PackageReference Include="BepInEx.PluginInfoProps" Version="2.*" />
  </ItemGroup>

  <!-- ✅ Import Per Aspera SDK - Single line replaces all manual references -->
  <Import Project="..\..\SDK_DLL\sdkDLL.props" />

</Project>
```

### Step 2: That's It!

No need to manually add references to individual SDK DLLs. The `.props` file handles everything automatically.

## 💡 Benefits vs Manual References

### ❌ Old Way (Manual References)
```xml
<!-- DON'T DO THIS ANYMORE -->
<ItemGroup>
  <Reference Include="PerAspera.ModSDK">
    <HintPath>..\..\SDK\bin\Debug\net6.0\PerAspera.ModSDK.dll</HintPath>
  </Reference>
  <Reference Include="PerAspera.Core">
    <HintPath>..\..\SDK\bin\Debug\net6.0\PerAspera.Core.dll</HintPath>
  </Reference>
  <Reference Include="PerAspera.GameAPI">
    <HintPath>..\..\SDK\bin\Debug\net6.0\PerAspera.GameAPI.dll</HintPath>
  </Reference>
  <!-- ... 6 more references to add manually ... -->
</ItemGroup>
```

### ✅ New Way (sdkDLL.props)
```xml
<!-- DO THIS INSTEAD -->
<Import Project="..\..\SDK_DLL\sdkDLL.props" />
```

### Advantages
1. **Simplified Setup** - One line vs 9+ manual references
2. **Auto-Updated** - Always uses current SDK version from SDK_DLL folder
3. **Consistent** - Same configuration across all mods
4. **Maintainable** - Central management of SDK references
5. **Error-Resistant** - No risk of missing or incorrect DLL paths
6. **Future-Proof** - New SDK components automatically included

## 🔍 Technical Details

### How It Works

The `sdkDLL.props` file:
1. **Defines** `$(PerAsperaSdkDllDir)` variable pointing to SDK_DLL folder
2. **Lists** all available SDK DLLs in `<PerAsperaSdkDlls>` item group
3. **Injects** references automatically before assembly resolution
4. **Sets** `Private=False` to prevent DLL copying (BepInX handles loading)

### File Structure
```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="15.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <PerAsperaSdkDllDir>$(MSBuildThisFileDirectory)</PerAsperaSdkDllDir>
  </PropertyGroup>

  <ItemGroup>
    <PerAsperaSdkDlls Include="$(PerAsperaSdkDllDir)PerAspera.Core.dll;$(PerAsperaSdkDllDir)PerAspera.GameAPI.dll;..." />
  </ItemGroup>

  <Target Name="AddPerAsperaSdkDllReferences" BeforeTargets="ResolveAssemblyReferences">
    <!-- Auto-injection logic -->
  </Target>
</Project>
```

## 📁 File Location

The `sdkDLL.props` file is located at:
```
F:\ModPeraspera\
  └── SDK_DLL\
      ├── sdkDLL.props          ← This file
      ├── PerAspera.Core.dll
      ├── PerAspera.GameAPI.dll
      └── ... (all other SDK DLLs)
```

## 🔧 Path Configuration

### Standard Layout
If your mod follows the standard Individual-Mods layout:
```
F:\ModPeraspera\
  ├── SDK_DLL\sdkDLL.props
  └── Individual-Mods\
      └── YourMod\
          └── YourMod.csproj   ← Use: ..\..\SDK_DLL\sdkDLL.props
```

### Custom Layout
If your mod is in a different location, adjust the path accordingly:
```xml
<!-- Example for mod in different folder structure -->
<Import Project="..\..\..\ModPeraspera\SDK_DLL\sdkDLL.props" />
```

## 🛠️ Troubleshooting

### Common Issues

**Build Error: Cannot import file**
- ✅ Check that `sdkDLL.props` exists at specified path
- ✅ Verify relative path is correct from your .csproj location

**Missing SDK References**
- ✅ Ensure all SDK DLLs are present in `SDK_DLL\` folder  
- ✅ Rebuild SDK if DLLs are missing: run build task "Build: SDK Only"

**Old Manual References Conflict**
- ✅ Remove all manual `<Reference Include="PerAspera.*">` entries
- ✅ Keep only the `<Import Project="...">` line

## 🔄 Migration Guide

### From Manual References to sdkDLL.props

1. **Backup** your current `.csproj` file
2. **Remove** all manual PerAspera SDK references:
   ```xml
   <!-- DELETE THESE SECTIONS -->
   <ItemGroup>
     <Reference Include="PerAspera.ModSDK">...</Reference>
     <Reference Include="PerAspera.Core">...</Reference>
     <!-- ... etc -->
   </ItemGroup>
   ```
3. **Add** the props import:
   ```xml
   <!-- ADD THIS LINE -->
   <Import Project="..\..\SDK_DLL\sdkDLL.props" />
   ```
4. **Build** and test your mod

## 🎯 Best Practices

1. **Always use sdkDLL.props** for new mods
2. **Migrate existing mods** when convenient  
3. **Keep path relative** to SDK_DLL folder
4. **Don't mix** manual references with props import
5. **Update documentation** to reference this approach

---

**📚 Related Documentation:**
- [Your First Mod Tutorial](getting-started/First-Mod.md) - Uses sdkDLL.props
- [Building Tutorial](tutorials/Buildings.md) - SDK integration examples
- [SDK Architecture](architecture/SDK-Components.md) - Complete SDK overview