---
title: MkAspera Custom Modifiers Guide
date: 2026-06-11
target_audience: MkAspera plugin developers
skill_reference: per-aspera-custom-modifiers
---

# MkAspera Custom Enhancement Modifiers

## Context

You have enhancements defined in `F:\ModPeraspera\Yaml-Mods\MkAspera\enhancements.yaml` that use modifiers like:

```yaml
enhancement_building_shield_1:
  modifiers:
    - "worker_decay: -0.05"
```

Some modifiers are **native** (the game handles them), but **custom modifiers** (ones your plugin creates) require you to:
1. Declare them in YAML
2. Register a C# handler that runs when the enhancement is unlocked

---

## System Overview

```
┌─────────────────┐
│  enhancements   │
│     .yaml       │
│                 │
│ modifiers:      │
│ - "key: value"  │
└────────┬────────┘
         │
         │ (during player unlock)
         ▼
┌─────────────────────────┐
│ Enhancements.Enable()   │
│ (Native game code)      │
└─────────────┬───────────┘
              │
              ▼
    ┌──────────────────────┐
    │ EnhancementsEnable   │
    │ Patch (HarmonyX)     │
    │                      │
    │ Finds custom keys    │
    │ Invokes handlers ←─┐  │
    │                  └──┘ │
    └──────────────────────┘
              │
              ▼
    ┌──────────────────────┐
    │ Your C# Handler Code │
    │ Runs with (name,     │
    │           delta)     │
    └──────────────────────┘
```

---

## Adding a Custom Modifier

### 1. Define in YAML

File: `F:\ModPeraspera\Yaml-Mods\MkAspera\enhancements.yaml`

```yaml
enhancement_ami_research_boost:
  name: enhancement_ami_research_boost_name
  description: enhancement_ami_research_boost_desc
  iconName: Sprite/ICO_AMI.png
  modifiers:
    - "ami_research_speed: 0.25"      # ← Your custom key here
    - "building_limit: 50"             # ← Native key (game handles)
```

**Rules:**
- Modifier format: `"key: value"` (colon + space)
- `value` is parsed as float: `0.25`, `50`, `-0.1`, `true`, `false`
- Use lowercase with underscores: `my_custom_key`

### 2. Register Handler in C#

In your plugin's `Load()` method (e.g., `MkAsperaPlugin.cs`):

```csharp
using PerAspera.GameAPI.Commands;

public class MkAsperaPlugin : BaseUnityPlugin
{
    private void Load()
    {
        // Register handler for your custom modifier
        Commands.RegisterModifier("ami_research_speed", OnAmiResearchBoost);
    }

    private static void OnAmiResearchBoost(string modifierName, float deltaValue)
    {
        // This runs when the enhancement is unlocked
        // deltaValue = 0.25 (from YAML)
        
        Logger.LogInfo($"AMI Research boost applied: +{deltaValue * 100}%");
        ResearchManager.GlobalSpeedMultiplier += deltaValue;
    }
}
```

Or as inline lambda:

```csharp
Commands.RegisterModifier("ami_research_speed", (name, delta) =>
{
    Logger.LogInfo($"✅ Research +{delta * 100}%");
    ResearchManager.GlobalSpeedMultiplier += delta;
});
```

### 3. Build & Deploy

```powershell
# Build the plugin
dotnet build Individual-Mods\MkAsperaPlugin\MkAsperaPlugin.csproj -c Release

# Deploy both plugin DLL + YAML mod
Copy-Item "Individual-Mods\MkAsperaPlugin\bin\Release\MkAsperaPlugin.dll" `
         "D:\SteamLibrary\steamapps\common\Per Aspera\BepInEx\plugins\"

# YAML mods are auto-loaded from StreamingAssets
```

---

## Native vs Custom Modifiers

### Native Modifiers (No Handler Needed)

These are built into the game. Declare and forget:

```yaml
modifiers:
  - "building_limit: 100"        # ✅ Game applies automatically
  - "spaceport_limit: 2"         # ✅ Game applies automatically
  - "extraction_time: -0.2"      # ✅ Game applies automatically
  - "worker_decay: -0.05"        # ✅ Game applies automatically (maybe)
```

**The game's native `Enhancements.Enable()` handles these.**

### Custom Modifiers (Handler Required)

These are **only** applied if you register a handler:

```yaml
modifiers:
  - "ami_research_speed: 0.25"        # ⚠️ Needs handler in C#
  - "custom_farming_yield: 0.15"      # ⚠️ Needs handler in C#
  - "drone_hop_capacity: 2"           # ⚠️ Needs handler in C#
```

**If no handler → modifier is silently ignored. No error, no warning.**

---

## Examples for MkAspera

### Example 1: AMI Story Progression

**Goal:** Unlock faster story progression when enhancement is earned.

**YAML:**
```yaml
enhancement_ami_narrative_phase_1:
  name: enhancement_ami_narrative_phase_1_name
  description: enhancement_ami_narrative_phase_1_desc
  iconName: Sprite/ICO_Narrative.png
  modifiers:
    - "ami_story_speed: 0.5"      # +50% story progression
```

**C# Handler:**
```csharp
Commands.RegisterModifier("ami_story_speed", (name, delta) =>
{
    AmiNarrativeSystem.ProgressionSpeed *= (1f + delta);
});
```

### Example 2: Drone Enhancement

**Goal:** Unlock enhanced drone movement when researched.

**YAML:**
```yaml
enhancement_drone_mobility:
  name: enhancement_drone_mobility_name
  description: enhancement_drone_mobility_desc
  modifiers:
    - "drone_hop_bonus: 3"        # +3 extra hops per task
```

**C# Handler:**
```csharp
Commands.RegisterModifier("drone_hop_bonus", (name, delta) =>
{
    RoutingPatch.ExtraHopCapacity = (int)delta;
    Logger.LogInfo($"Drone hop capacity: +{(int)delta}");
});
```

### Example 3: Resource Extraction Boost

**Goal:** Boost production of rare resources.

**YAML:**
```yaml
enhancement_rare_ore_extraction:
  name: enhancement_rare_ore_extraction_name
  description: enhancement_rare_ore_extraction_desc
  modifiers:
    - "rare_ore_bonus: 0.2"       # +20% production
```

**C# Handler:**
```csharp
Commands.RegisterModifier("rare_ore_bonus", (name, delta) =>
{
    var resourceType = /* get "Rare Ore" from game */;
    ProductionMultipliers[resourceType] = 1f + delta;
});
```

---

## Testing Your Custom Modifier

1. **In-game testing:**
   - Load the save with the enhancement
   - Unlock the enhancement (console command or progression)
   - Check BepInEx log for handler invocation

2. **Debug log message:**
   ```csharp
   // Add to your handler
   Logger.LogInfo($"[MkAspera] Custom modifier '{modifierName}' = {deltaValue}");
   ```

3. **Verify registration (optional):**
   ```csharp
   // In a debug command or plugin startup
   var isRegistered = Commands.IsModifierRegistered("ami_research_speed");
   Logger.LogInfo($"Handler registered? {isRegistered}");
   ```

---

## API Reference

### Commands.RegisterModifier()

```csharp
/// <summary>
/// Register a C# handler for a custom YAML modifier key.
/// </summary>
/// <param name="modifierKey">The key from YAML (case-insensitive)</param>
/// <param name="handler">Lambda or method: (string name, float delta) => {...}</param>
public static void RegisterModifier(string modifierKey, CustomModifierHandler handler)
```

**Parameters:**
- `modifierKey`: String from YAML modifier (e.g., `"ami_research_speed"`)
- `handler`: Delegate that receives the key name and parsed float value

**Example:**
```csharp
Commands.RegisterModifier("drone_hop_bonus", (name, delta) =>
{
    // name = "drone_hop_bonus"
    // delta = value from YAML (parsed as float)
});
```

### Commands.IsModifierRegistered()

```csharp
public static bool IsModifierRegistered(string modifierKey)
```

Returns `true` if a handler is already registered for this key.

### Commands.RegisteredModifiers

```csharp
public static IReadOnlyCollection<string> RegisteredModifiers
```

Returns all currently registered custom modifier keys.

---

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| Handler never fires | Modifier key spelling doesn't match YAML | Check case: `"ami_research_speed"` vs `"ami_research_Speed"` |
| Value is 0 | YAML parsing failed | Ensure format: `"key: value"` with space after colon |
| Type casting error | Trying to cast float as wrong type | Use `(int)delta` for integers, `delta` for floats |
| Log shows "ignored" | Handler not registered in Load() | Add `Commands.RegisterModifier()` call to `Load()` |
| Enhancement unlocks but nothing happens | Plugin not deployed | Copy DLL to `BepInEx/plugins/` |

---

## Checklist: Adding a Custom Modifier

- [ ] **YAML:** Declared modifier in `enhancements.yaml`: `"my_key: value"`
- [ ] **C# Code:** Called `Commands.RegisterModifier("my_key", handler)` in `Load()`
- [ ] **Handler:** Lambda or method accepts `(string, float)` parameters
- [ ] **Logic:** Handler code does what you intend
- [ ] **Build:** `dotnet build` succeeds, no compile errors
- [ ] **Deploy:** Plugin DLL in `BepInEx/plugins/`, YAML in mods folder
- [ ] **Test:** Unlock enhancement in-game
- [ ] **Verify:** BepInEx log shows handler invocation

---

## Resources

- **Full API docs:** [per-aspera-custom-modifiers SKILL](/memories/repo/skills/per-aspera-custom-modifiers/)
- **Commands SDK:** [per-aspera-commands-sdk](/memories/repo/skills/per-aspera-commands-sdk/)
- **YAML reference:** [per-aspera-yaml-modding](/memories/repo/skills/per-aspera-yaml-modding/)

