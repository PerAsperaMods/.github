# Custom YAML Actions — IModTextAction

This guide explains how to create custom commands that can be triggered from YAML files in Per Aspera, just like the built-in `GiveSciencePoints`.

## What is a YAML action?

A **YAML action** is a command triggered by a Special Project (or other game event) when it launches, completes, or at a timed interval. Example:

```yaml
launchActions:
  - command: GiveSciencePoints
    arguments: ["500"]
    daysDelay: 0.0
```

The SDK lets you register your own commands that work identically.

---

## Architecture overview

```
YAML "command: MyAction"
  → InteractionManager.DispatchAction()        [native game]
  → NativeDispatchInterceptPatch (Harmony)      [SDK intercepts]
  → CustomCommandRegistry.TryHandle()           [routes to mod handler]
  → ModTextActionRegistry                       [finds IModTextAction]
  → MyAction.Execute(args, ctx)                 [your code runs]
```

The SDK installs all Harmony patches automatically when your plugin references `Commands` for the first time — no manual setup needed.

---

## Step 1 — Create your action class

Implement `IModTextAction` from `PerAspera.GameAPI.Commands.ModActions`:

```csharp
using PerAspera.Core;
using PerAspera.GameAPI.Commands.ModActions;
using PerAspera.GameAPI.Commands.ModActions.BuiltinActions;
using PerAspera.GameAPI.Events.SDK;

namespace MyMod.Actions
{
    /// <summary>
    /// Teleports the player colony ship to a target location.
    /// YAML: command: TeleportColony, arguments: ["target_zone_id"]
    /// </summary>
    public class TeleportColonyAction : IModTextAction
    {
        private static readonly LogAspera _log = new LogAspera("TeleportColony");

        // Must match exactly what you write in YAML under "command:"
        // Case-insensitive at runtime.
        public string CommandName => "TeleportColony";

        public bool Execute(string[] args, GameCommandsReadyEvent? ctx)
        {
            // 1. Parse arguments
            string zoneId = ActionContextHelper.GetOptionalString(args, 0, "zone_default");

            // 2. Get faction if needed
            if (!ActionContextHelper.TryGetFaction(ctx, out var faction, _log, CommandName))
                return false;

            // 3. Access other game objects via ctx
            var planet = ctx?.NativePlanet;
            var universe = ctx?.NativeUniverse;

            // 4. Your game logic here
            _log.Info($"[TeleportColony] Teleporting '{faction!.name}' to zone '{zoneId}'");

            // Return true = success, false = failure (logged as warning in SDK)
            return true;
        }
    }
}
```

---

## Step 2 — Register in plugin Load()

```csharp
// Note: the game has a native 'Commands' type in PerAspera.Commands —
// use an alias to avoid ambiguity when using the SDK Commands class.
using SdkCommands = PerAspera.GameAPI.Commands.Commands;

[BepInPlugin("com.mymods.mymod", "My Mod", "1.0.0")]
public class MyModPlugin : BasePlugin
{
    public override void Load()
    {
        // Register by type — simplest approach
        SdkCommands.RegisterAction<TeleportColonyAction>();

        // Alternatively, register an instance (useful if you need constructor args):
        // SdkCommands.RegisterAction(new TeleportColonyAction());

        Log.LogInfo("✅ Custom actions registered.");
    }
}
```

> **Important**: Register in `Load()`, before game data is parsed.
> The SDK automatically patches YAML load-time validation so unknown command names don't fail.

---

## Step 3 — Use in YAML

```yaml
# In your Special Project definition:
launchActions:
  - command: TeleportColony
    arguments:
      - north_pole_zone
    daysDelay: 0.0

# Multiple arguments:
launchActions:
  - command: MyAction
    arguments:
      - "arg0_value"
      - "42"
      - "true"
    daysDelay: 5.0
```

Arguments are always **strings** in YAML — parse them in `Execute()` using `ActionContextHelper`.

---

## ActionContextHelper — safe argument parsing

`ActionContextHelper` is a static utility class that handles argument parsing with automatic logging. Use it instead of manual parsing.

```csharp
// Required float
if (!ActionContextHelper.TryGetFloat(args, 0, out float speed, _log, CommandName))
    return false;

// Required positive float (> 0)
if (!ActionContextHelper.TryGetPositiveFloat(args, 0, out float amount, _log, CommandName))
    return false;

// Required integer
if (!ActionContextHelper.TryGetInt(args, 1, out int count, _log, CommandName))
    return false;

// Optional string with default
string mode = ActionContextHelper.GetOptionalString(args, 2, "normal");

// Player faction (logs warning if unavailable)
if (!ActionContextHelper.TryGetFaction(ctx, out var faction, _log, CommandName))
    return false;
```

---

## Game context — what you can access

`GameCommandsReadyEvent ctx` gives you access to native game objects:

| Property | Type | Description |
|---|---|---|
| `ctx.NativePlayerFaction` | `Faction?` | The player's faction |
| `ctx.NativePlanet` | `Planet?` | Current planet |
| `ctx.NativeUniverse` | `Universe?` | Top-level game manager |
| `ctx.NativeBaseGame` | `BaseGame?` | Base game instance |

All are nullable. Always null-check or use `ActionContextHelper.TryGetFaction()`.

---

## Built-in actions you can reference

| CommandName | Arguments | Effect |
|---|---|---|
| `GiveSciencePoints` | `[0]` amount (float) | Adds research points to active technology |
| `ShowMessage` | `[0]` message, `[1]` level | Logs a message (Info/Warning/Error) |

Their source code is in `F:\ModPeraspera\SDK\PerAspera.GameAPI.Commands\ModActions\BuiltinActions\` — use them as reference implementations.

---

## Full example — GiveResourcesAction

A complete, real-world example showing a custom action that adds resources:

```csharp
using PerAspera.Core;
using PerAspera.GameAPI.Commands.ModActions;
using PerAspera.GameAPI.Commands.ModActions.BuiltinActions;
using PerAspera.GameAPI.Events.SDK;

namespace MyMod.Actions
{
    /// <summary>
    /// Adds a resource to the player faction's stockpile.
    ///
    /// YAML:
    ///   command: GiveResources
    ///   arguments:
    ///     - water        # resource key
    ///     - "1000"       # amount (float)
    /// </summary>
    public class GiveResourcesAction : IModTextAction
    {
        private static readonly LogAspera _log = new LogAspera("GiveResources");

        public string CommandName => "GiveResources";

        public bool Execute(string[] args, GameCommandsReadyEvent? ctx)
        {
            string resourceKey = ActionContextHelper.GetOptionalString(args, 0, "");
            if (string.IsNullOrEmpty(resourceKey))
            {
                _log.Warning($"[{CommandName}] Missing resource key (args[0])");
                return false;
            }

            if (!ActionContextHelper.TryGetPositiveFloat(args, 1, out float amount, _log, CommandName))
                return false;

            if (!ActionContextHelper.TryGetFaction(ctx, out var faction, _log, CommandName))
                return false;

            // Use native faction API
            // faction.AddResource(resourceKey, amount);   ← your actual call here

            _log.Info($"[{CommandName}] Added {amount}x '{resourceKey}' to '{faction!.name}'");
            return true;
        }
    }
}
```

Registration:
```csharp
Commands.RegisterAction<GiveResourcesAction>();
```

YAML:
```yaml
launchActions:
  - command: GiveResources
    arguments:
      - water
      - "1000"
    daysDelay: 0.0
```

---

## Registry inspection

```csharp
// Verify registration
bool ok = ModTextActionRegistry.IsRegistered("TeleportColony");

// List all registered actions (built-ins + yours)
foreach (var name in ModTextActionRegistry.RegisteredActions)
    Log.LogInfo($"  Action: {name}");

// Unregister at runtime (e.g., when plugin unloads)
ModTextActionRegistry.Unregister("TeleportColony");
```

---

## Checklist for a new action

- [ ] Class implements `IModTextAction` with a public parameterless constructor
- [ ] `CommandName` is unique (check `ModTextActionRegistry.IsRegistered` at Load time)
- [ ] `Execute()` returns `true` on success, `false` on failure
- [ ] Arguments validated with `ActionContextHelper` (never access `args[n]` directly without bounds check)
- [ ] Registered via `Commands.RegisterAction<T>()` in plugin `Load()`
- [ ] YAML `command:` value matches `CommandName` exactly (case-insensitive)
- [ ] Tested in-game with `launchType: Continuous, requiredLaunches: 1` for quick iteration
