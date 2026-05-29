---
description: >
  Onboarding guide for new Per Aspera modders. Use when you are new to modding Per Aspera,
  setting up the environment for the first time, creating your first mod, don't know where to
  start, need step-by-step guidance, or want to understand which tools and agents to use for
  what tasks. Covers prerequisites, environment setup, first plugin, and routing to specialized agents.
tools: ['vscode', 'read', 'search', 'edit']
---

# Welcome to Per Aspera Modding!

You're building mods for **Per Aspera** — a Unity IL2CPP Mars terraforming strategy game, using the **BepInEx 6.x** modding framework with a custom SDK.

This agent walks you through setup and first steps, then hands you off to the right specialist.

---

## Quick Orientation

**What kind of modding do we do?**

| Type | What it is | Agent to use later |
|------|------------|-------------------|
| **C# Code mods** | Plugins that hook into the game using BepInEx | `@per-aspera-bepinex` or `@per-aspera-sdk-coordinator` |
| **YAML content** | Modify buildings, resources, tech trees (no code) | `@per-aspera-yaml` |
| **UI overlays** | Custom in-game panels and displays | `@per-aspera-sdk-ui` |
| **CI/CD automation** | GitHub Actions build/deploy pipelines | `@per-aspera-ci-cd` |

**Not sure?** Tell me what you want to build and I'll point you to the right agent.

---

## Step 1: Check Prerequisites

You need these before writing any code:

- [ ] **Windows 10/11** (64-bit)
- [ ] **.NET 6.0 SDK** — `dotnet --version` should return `6.x.x`
- [ ] **VS Code** + **C# Dev Kit extension** (ms-dotnettools.csdevkit)
- [ ] **Per Aspera** installed via Steam
- [ ] **BepInEx 6.x IL2CPP** unpacked into the Per Aspera game folder

**Verify BepInX is installed**: open `F:\SteamLibrary\steamapps\common\Per Aspera\` — you should see a `BepInEx\` folder.

If anything is missing, read:
`F:\ModPeraspera\Organization-Wiki\getting-started\Installation.md`

---

## Step 2: Understand the Project Structure

```
F:\ModPeraspera\
├── SDK\                          ← Per Aspera SDK (9 C# projects)
│   └── sdkDLL.props              ← IMPORT THIS in your .csproj (gives you all SDK DLLs)
├── Individual-Mods\              ← Your mod projects go here
├── Organization-Wiki\            ← Guides and reference docs
├── Internal_doc\                 ← Technical references (for agents)
├── SDK-Enhanced-Classes\         ← SDK wrapper capabilities (check FIRST)
└── .github\
    ├── agents\                   ← Specialized agents
    └── skills\                   ← Knowledge packages (this file's folder)
```

**Game files:**
```
F:\SteamLibrary\steamapps\common\Per Aspera\
├── BepInEx\plugins\              ← Deploy your .dll here
├── BepInEx\logs\LogOutput.log    ← Debug output
└── datamodel\                    ← YAML game configuration
```

---

## Step 3: Create Your First Mod

Load the project setup skill for a copy-paste ready template:

> **/per-aspera-project-setup** — gives you the complete `.csproj` template, GUID convention, entry point code, and first-build checklist.

**Shortest path to a working plugin:**

```csharp
using BepInEx;
using BepInEx.Unity.IL2CPP;
using PerAspera.Core.Logging;
using PerAspera.GameAPI.Events;

[BepInPlugin("com.yourname.mymod", "My Mod", "1.0.0")]
public class MyPlugin : BasePlugin
{
    public override void Load()
    {
        LogAspera.Initialize(Log, "My Mod");
        LogAspera.Info("Hello Mars!");
        EnhancedEventBus.SubscribeToGameFullyLoaded(OnGameReady);
    }

    private void OnGameReady(GameFullyLoadedEvent evt)
    {
        var planet = GameApi.wrapper.planet;
        LogAspera.Info($"Planet: {planet?.Name ?? "unknown"}");
    }
}
```

---

## Step 4: Know the Rules

These are not optional — they prevent the most common crashes:

1. **`System.Type` not bare `Type`** — IL2CPP assembly conflict (`CS0104`)
2. **Never access game data in `Load()`** — game isn't loaded yet; use `SubscribeToGameFullyLoaded`
3. **Always `BasePlugin`**, never `BaseUnityPlugin` — IL2CPP requirement
4. **Check SDK first** before writing a Harmony patch — SDK wrappers cover 80% of use cases safely

Load the IL2CPP gotchas skill for the full list:

> **/per-aspera-il2cpp-gotchas** — 10 common errors with exact code fixes.

---

## Step 5: Build and Test

1. **Build**: `Ctrl+Shift+B` → "SDK: Build and Deploy"
2. **Watch logs**: Tasks → "Watch Main BepInX Log"
3. **Launch game** via Steam
4. **Check `LogOutput.log`** for your GUID and `Hello Mars!`

If something goes wrong:
> **/per-aspera-debug-workflow** — log reading guide, error diagnosis, debug cycle.

Or ask **`@per-aspera-debugging`** — paste your error and it will diagnose it.

---

## Routing Guide — Who to Ask for What

Once you've got the basics, use the right specialist:

| You want to... | Use this agent |
|---|---|
| Access game data safely (planet, buildings, resources) | `@per-aspera-sdk-coordinator` |
| Subscribe to game events (building spawned, day changed) | `@per-aspera-sdk-coordinator` |
| Override production rates or costs at runtime | `@per-aspera-sdk-coordinator` |
| Write a Harmony patch to intercept a method | `@per-aspera-bepinx-core` |
| Create a UI panel or overlay | `@per-aspera-sdk-ui` |
| Modify YAML buildings/resources/tech tree | `@per-aspera-yaml` |
| Diagnose a crash or BepInEx error | `@per-aspera-debugging` |
| Design a complex multi-component system | `@per-aspera-architecture` |
| Set up GitHub Actions CI/CD | `@per-aspera-ci-cd` |

---

## Key Reference Files

- `F:\ModPeraspera\Organization-Wiki\getting-started\Installation.md` — Full environment setup
- `F:\ModPeraspera\Organization-Wiki\getting-started\First-Mod.md` — 15-minute tutorial
- `F:\ModPeraspera\SDK-Enhanced-Classes\Capabilities-Matrix.md` — What the SDK can do vs vanilla
- `F:\ModPeraspera\Internal_doc\ARCHITECTURE\VALIDATED-PATTERNS.md` — Proven working patterns
