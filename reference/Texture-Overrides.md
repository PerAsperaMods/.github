# Texture Overrides Documentation

## 📚 Introduction

This document covers **Texture Overrides** system from the official ISA modding manual v2.0. This system allows mods to replace building textures with custom graphics.

**⚠️ Critical Warning:** This documentation is from the **official ISA manual but may contain obsolete information**. Texture override system and Unity asset handling may have changed significantly.

**Sources:**
- ISA Quantum AI Laboratory Modding Manual v2.0 (official but potentially obsolete)  
- Texture Override examples from official manual

---

## 🚨 Validation Status

**❌ UNVALIDATED:** All texture override information comes from official manual but **requires testing against current game version**.

---

## 🎨 Texture Override Concept (Official Manual)

According to the official manual, `textureOverrides` allows:
- **Replace building textures** with custom images
- **Multiple texture sets** per building based on conditions
- **Dynamic texture switching** based on game state
- **Asset bundle integration** for complex graphics

**⚠️ Critical Questions:**
1. Does `textureOverrides` property actually exist?
2. Are texture files loaded from mod folders?
3. Is Unity asset bundle system accessible to mods?

---

## 📋 Basic Syntax (Official Manual)

### Simple Texture Override
```yaml
# ⚠️ WARNING: Unvalidated official example
building_custom_solar:
  name: "Custom Solar Panel"
  description: "Solar panel with custom graphics"
  category: !buildingCategory energy
  cost:
    - !resource metal: 100
  
  # Texture override system (unvalidated)
  textureOverrides:
    - targetTexture: "building_main_texture"
      replacementPath: "textures/my_solar_panel.png"
    - targetTexture: "building_detail_texture"  
      replacementPath: "textures/my_solar_details.png"
```

### Conditional Texture Overrides
```yaml
# ⚠️ WARNING: Complex unvalidated example
building_adaptive:
  name: "Adaptive Building"
  textureOverrides:
    - condition: "planet_temperature > 50"
      targetTexture: "building_main_texture"
      replacementPath: "textures/hot_planet_version.png"
    - condition: "planet_temperature < 0"
      targetTexture: "building_main_texture" 
      replacementPath: "textures/cold_planet_version.png"
    - condition: "default"
      targetTexture: "building_main_texture"
      replacementPath: "textures/normal_version.png"
```

---

## 🖼️ Texture Properties (Official)

### Core Properties

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `targetTexture` | String | Original texture name to replace | ⚠️ Unvalidated |
| `replacementPath` | String | Path to new texture file | ⚠️ Unvalidated |
| `condition` | String | When to apply this override | ⚠️ Unvalidated |
| `priority` | Number | Override priority (higher wins) | ⚠️ Unvalidated |

### Advanced Properties

| Property | Type | Description | Status |
|----------|------|-------------|--------|
| `textureType` | String | Type: diffuse, normal, specular, etc. | ⚠️ Unvalidated |
| `tiling` | Array | UV tiling [x, y] | ⚠️ Unvalidated |
| `offset` | Array | UV offset [x, y] | ⚠️ Unvalidated |
| `filterMode` | String | Texture filtering: point, bilinear, trilinear | ⚠️ Unvalidated |
| `wrapMode` | String | Texture wrapping: repeat, clamp, mirror | ⚠️ Unvalidated |

---

## 📂 File Organization (Official)

According to manual, texture files should be organized as:

```
your_mod/
├── manifest.yaml
├── buildings/
│   └── custom_buildings.yaml
├── textures/
│   ├── diffuse/
│   │   ├── solar_panel_main.png
│   │   └── factory_walls.jpg  
│   ├── normal/
│   │   ├── solar_panel_normal.png
│   │   └── factory_normal.jpg
│   └── specular/
│       ├── solar_panel_spec.png
│       └── factory_spec.jpg
└── models/          # If custom 3D models supported
    └── custom_building.fbx
```

**⚠️ Warning:** File structure may be incorrect or game may not load textures from mod folders.

---

## 🎯 Texture Types (Official)

### Supported Texture Types
Official manual lists these texture slots:

| Type | Purpose | File Format | Status |
|------|---------|-------------|--------|
| `diffuse` | Base color/albedo | PNG, JPG | ⚠️ Unvalidated |
| `normal` | Normal/bump mapping | PNG (normal map) | ⚠️ Unvalidated |
| `specular` | Specular/metallic | PNG, JPG | ⚠️ Unvalidated |
| `emission` | Emissive glow | PNG | ⚠️ Unvalidated |
| `occlusion` | Ambient occlusion | PNG | ⚠️ Unvalidated |
| `height` | Height/displacement | PNG | ⚠️ Unvalidated |

### ⚠️ Official Example
```yaml
# Complex texture override (completely unvalidated)
building_advanced_factory:
  textureOverrides:
    - targetTexture: "building_main"
      textureType: "diffuse"
      replacementPath: "textures/diffuse/factory_main.png"
      filterMode: "trilinear"
      
    - targetTexture: "building_main_normal"
      textureType: "normal"
      replacementPath: "textures/normal/factory_normal.png"
      
    - targetTexture: "building_main_spec"
      textureType: "specular"  
      replacementPath: "textures/specular/factory_spec.png"
      
    - targetTexture: "building_glow"
      textureType: "emission"
      replacementPath: "textures/emission/factory_glow.png"
      condition: "building_powered == true"
```

---

## 🔧 Advanced Features (Official)

### Conditional Overrides
Manual suggests these condition types:

| Condition Type | Example | Status |
|----------------|---------|--------|
| Temperature | `planet_temperature > 30` | ⚠️ Unvalidated |
| Atmosphere | `oxygen_level > 0.5` | ⚠️ Unvalidated |
| Power State | `building_powered == true` | ⚠️ Unvalidated |
| Resource Level | `stored_energy > 100` | ⚠️ Unvalidated |
| Time of Day | `solar_angle > 0` | ⚠️ Unvalidated |
| Season | `planet_season == "winter"` | ⚠️ Unvalidated |

### Animation Support
```yaml
# ⚠️ WARNING: Completely speculative example
building_animated:
  textureOverrides:
    - targetTexture: "building_main"
      animation:
        type: "sequence"
        frames:
          - "textures/anim/frame_01.png"
          - "textures/anim/frame_02.png" 
          - "textures/anim/frame_03.png"
        frameRate: 10
        looping: true
```

---

## 🖥️ Technical Requirements (Official)

### Texture Specifications
According to manual:

| Specification | Requirement | Status |
|---------------|-------------|--------|
| **Format** | PNG, JPG, TGA | ⚠️ Unvalidated |
| **Resolution** | Power of 2 (256x256, 512x512, 1024x1024) | ⚠️ Unvalidated |
| **Color Depth** | 24-bit (RGB) or 32-bit (RGBA) | ⚠️ Unvalidated |
| **Compression** | DXT1 (RGB) or DXT5 (RGBA) for performance | ⚠️ Unvalidated |
| **Max Size** | 2048x2048 (larger may cause performance issues) | ⚠️ Unvalidated |

### Normal Maps
```yaml
# Normal map specific properties (unvalidated)
building_detailed:
  textureOverrides:
    - targetTexture: "building_normal"
      textureType: "normal"
      replacementPath: "textures/building_normal.png"
      normalMapIntensity: 1.0     # Strength of normal effect
      normalMapSpace: "tangent"   # tangent or object space
```

---

## 🚨 Major Concerns

### Unity Asset System Complexity
**Texture overrides involve complex Unity systems:**
- Material property access
- Shader compatibility
- Runtime texture loading
- Memory management
- Asset bundle system

### High Probability of Obsolescence
**Multiple red flags suggest this system may not work:**
- No evidence in current mod examples
- Complex Unity integration unlikely in IL2CPP
- Texture file paths from mod folders questionable
- Conditional system very sophisticated

---

## 🔍 Validation Tests Required

### Basic Functionality
1. **Test texture property existence** - Does `textureOverrides` do anything?
2. **Check file loading** - Can game load textures from mod folders?
3. **Verify texture formats** - Which file formats actually work?

### Advanced Features  
1. **Conditional overrides** - Do condition strings work?
2. **Multiple texture types** - Normal maps, specular, etc.
3. **Animation support** - Frame sequences, timing
4. **Performance impact** - Memory usage, loading times

### Current Game Investigation
```bash
# Search for texture-related code
grep -r "texture" CleanedScriptAssemblyClass/ | head -20
grep -r "material" CleanedScriptAssemblyClass/ | head -20
grep -r "override" Internal_doc/

# Check for texture files in game
find "F:\SteamLibrary\steamapps\common\Per Aspera" -name "*.png" | head -10
find "F:\SteamLibrary\steamapps\common\Per Aspera" -name "*.jpg" | head -10
```

---

## 🔬 Alternative Approaches

### If Texture Overrides Don't Work

#### BepInX Texture Replacement
```csharp
// Possible BepInX approach for texture modding
[HarmonyPatch(typeof(Renderer), "material", MethodType.Setter)]
public static bool ReplaceTexture(Renderer __instance, Material value)
{
    if (value.mainTexture.name == "target_texture")
    {
        // Load custom texture and replace
        value.mainTexture = LoadCustomTexture("mod_texture.png");
    }
    return true;
}
```

#### Unity Asset Bundle Mods
```csharp
// Asset bundle loading approach
public static void LoadCustomAssets()
{
    var bundle = AssetBundle.LoadFromFile("mod_assets.bundle");
    var customTexture = bundle.LoadAsset<Texture2D>("custom_building_texture");
    // Apply to materials...
}
```

---

## 🔗 Related Documentation

- **[Building YAML Properties](Building-YAML-Properties.md)** - Other visual properties
- **[Manifest YAML](Manifest-YAML.md)** - Mod structure and asset files
- **[BepInX Agent](../agents/per-aspera-bepinx-core.md)** - Runtime texture modification

---

## ⚡ Immediate Action Required

### High Priority Validation
1. **Test minimal texture override** - Single texture replacement
2. **Check Unity shader compatibility** - Standard vs custom shaders
3. **Verify asset loading paths** - Can mods load external files?
4. **Document current limitations** - What actually works

### Investigation Strategy
```yaml
# Minimal test mod for texture validation
test_building:
  name: "Texture Test Building"
  category: !buildingCategory energy
  cost:
    - !resource metal: 10
  
  # Test if this property exists at all
  textureOverrides:
    - targetTexture: "test"
      replacementPath: "test.png"
```

### If System Is Obsolete
1. **Document current texture modding methods** (BepInX, asset bundles)
2. **Create working examples** with validated approaches
3. **Update with realistic expectations** for texture modding
4. **Provide alternative solutions** for visual customization

---

**Last Updated:** December 17, 2025  
**Source:** ISA Quantum AI Laboratory Modding Manual v2.0  
**Status:** ❌ **COMPLETELY UNVALIDATED** - Complex Unity system likely obsolete  
**Priority:** MEDIUM - Texture modding important but not critical for basic mods  
**Risk Level:** VERY HIGH - May be completely non-functional system