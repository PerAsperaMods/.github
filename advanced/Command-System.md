# Command System - InteractionManager, TextAction & CommandBus

🎯 **Complete guide to Per Aspera command execution systems**

## 📋 Overview

Per Aspera provides two primary command execution systems:

1. **TextAction System** - High-level string-based commands via `InteractionManager.DispatchAction`
2. **CommandBus System** - Native IL2CPP command objects via reflection-based dispatch

Both systems integrate with the same underlying game mechanics but offer different levels of abstraction and performance characteristics.

## 🏗️ Core Architecture

### TextAction System (Legacy)

#### InteractionManager.DispatchAction Signatures

```csharp
// Primary method for TextAction commands
public static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)

// Alternative for GameEvent objects
public static void DispatchAction(IHandleable asSender, GameEventBus bus, GameEvent action)

// Batch execution
public static void DispatchActions(IHandleable asSender, GameEventBus bus, List<TextAction> actions, string context)
```

#### Required Components

1. **IHandleable sender** - Usually player faction converted via KeeperAccessHelper
2. **GameEventBus** - Event bus from faction (`faction.GetGameEventBus()`)
3. **TextAction** - Command object with tabbed string format
4. **Context string** - Identifier for command source (e.g., "CommandsDemo")

### CommandBus System (Native)

#### CommandBus.DispatchCommand Signature

```csharp
// Native command dispatch via reflection
public static object DispatchCommand(object command, object faction)

// Generic wrapper for type safety
public static TCommandResult DispatchCommand<TCommand, TCommandResult>(TCommand command, object faction)
where TCommand : class
```

#### Required Components

1. **Native Command Object** - IL2CPP command instance (e.g., `CmdFactionResourceAllocation`)
2. **Faction Object** - Native faction instance
3. **Type Safety** - Generic constraints for compile-time validation

## 🔧 TextAction Wrapper

### Factory Methods

```csharp
// Create from command and arguments
var textAction = TextAction.Create("ImportResource", "PlayerFaction", "Ice", "10.0");

// Parse tabbed string format (recommended)
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0");

// Convenience method for resource imports
var textAction = TextAction.CreateAddResource("Ice", 10.0f);
```

### Properties Access

```csharp
public float DaysDelay { get; set; }     // Execution delay
public string Command { get; set; }       // Command name
public List<string> Arguments { get; set; } // Command parameters
public bool ShowInFrontend { get; set; }  // Display in UI
```

## 🔧 CommandBus Generic Dispatch

### SDK Wrapper Implementation

```csharp
/// <summary>
/// Generic command dispatch utility for native CommandBus
/// Provides type-safe access to IL2CPP command execution
/// </summary>
public static class FactionWrapper
{
    /// <summary>
    /// Dispatch a command via CommandBus with reflection
    /// </summary>
    /// <typeparam name="TCommand">Command type (e.g., CmdFactionResourceAllocation)</typeparam>
    /// <param name="commandName">Command method name</param>
    /// <param name="args">Command arguments</param>
    /// <returns>Command result or null</returns>
    public static object? DispatchCommand<TCommand>(string commandName, params object[] args)
        where TCommand : class
    {
        try
        {
            // Get ScriptsAssembly and CommandBus
            var scriptsAssembly = Assembly.Load("Assembly-CSharp");
            var commandBusType = scriptsAssembly.GetType("CommandBus");
            var commandBusInstance = commandBusType.GetProperty("Instance").GetValue(null);

            // Create command instance
            var commandType = scriptsAssembly.GetType(typeof(TCommand).Name);
            var commandInstance = Activator.CreateInstance(commandType);

            // Set command properties from args
            // ... property mapping logic ...

            // Get current faction
            var universe = BaseGame.Instance.universe;
            var faction = universe.GetPlayerFaction().GetNativeObject();

            // Dispatch via CommandBus
            var dispatchMethod = commandBusType.GetMethod("DispatchCommand");
            return dispatchMethod.Invoke(commandBusInstance, new[] { commandInstance, faction });
        }
        catch (Exception ex)
        {
            LogAspera.Error($"Command dispatch failed: {ex.Message}");
            return null;
        }
    }
}
```

### Usage Examples

```csharp
// Resource allocation command
var result = FactionWrapper.DispatchCommand<CmdFactionResourceAllocation>(
    "AllocateResources",
    resourceType,
    amount,
    targetFaction
);

// Building construction
var result = FactionWrapper.DispatchCommand<CmdConstructBuilding>(
    "ConstructBuilding",
    buildingType,
    position,
    orientation
);
```

## 🎯 Command Examples

### ImportResource Command (TextAction)

**Syntax:** `ImportResource\tPlayerFaction\t[RESOURCE]\t[AMOUNT]`

**Supported Resources:** CHG, ICE, NITROGEN, OXYGEN (hard-coded limitation)

```csharp
// Import 10.0 units of Ice
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0");

// Import 25.0 units of CHG (methane/carbon)
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tCHG\t25.0");
```

### CmdFactionResourceAllocation (CommandBus)

**Native Command Structure:**
```csharp
public class CmdFactionResourceAllocation
{
    public object Faction { get; set; }
    public object ResourceType { get; set; }
    public float Amount { get; set; }
    public object TargetFaction { get; set; }
    public string Reason { get; set; }
}
```

**SDK Wrapper Usage:**
```csharp
// Allocate 100.0 units of water to player faction
FactionWrapper.DispatchCommand<CmdFactionResourceAllocation>(
    "AllocateResources",
    ResourceType.Water,
    100.0f,
    playerFaction,
    "Mod allocation"
);
```

## 🔗 Complete Execution Pipeline

### TextAction Pipeline

#### 1. Get Game Context
```csharp
var baseGame = BaseGame.GetCurrent();
var universe = baseGame.GetUniverse();
var faction = universe.GetPlayerFaction();
```

#### 2. Convert Faction Handle
```csharp
var factionNative = faction.GetNativeObject();
var factionHandle = factionNative.GetType().GetProperty("handle").GetValue(factionNative);
var playerFactionAsHandleable = KeeperAccessHelper.ConvertHandleToIHandleable(factionHandle);
```

#### 3. Get GameEventBus
```csharp
var gameEventBus = faction.GetGameEventBus();
```

#### 4. Create TextAction
```csharp
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0");
```

#### 5. Execute Command
```csharp
var dispatchMethod = interactionManagerType.GetMethod("DispatchAction", /* 4 parameters */);
var parameters = new object[] {
    playerFactionAsHandleable,    // IHandleable
    gameEventBus,                 // GameEventBus (critical!)
    textAction.GetNativeObject(), // TextAction native object
    "YourModName"                 // context
};

dispatchMethod.Invoke(null, parameters);
```

### CommandBus Pipeline

#### 1. Get Native Context
```csharp
var baseGame = BaseGame.Instance;  // SDK wrapper
var universe = baseGame.GetUniverse();
var faction = universe.GetPlayerFaction().GetNativeObject();  // Native object
```

#### 2. Create Command Instance
```csharp
// Via reflection
var scriptsAssembly = Assembly.Load("Assembly-CSharp");
var commandType = scriptsAssembly.GetType("CmdFactionResourceAllocation");
var command = Activator.CreateInstance(commandType);

// Set properties
commandType.GetProperty("Faction").SetValue(command, faction);
commandType.GetProperty("ResourceType").SetValue(command, resourceType);
commandType.GetProperty("Amount").SetValue(command, 100.0f);
```

#### 3. Get CommandBus
```csharp
var commandBusType = scriptsAssembly.GetType("CommandBus");
var commandBus = commandBusType.GetProperty("Instance").GetValue(null);
```

#### 4. Execute Command
```csharp
var dispatchMethod = commandBusType.GetMethod("DispatchCommand");
var result = dispatchMethod.Invoke(commandBus, new[] { command, faction });
```

## 🛠️ SDK Helper Classes

### KeeperAccessHelper

Handles distributed Keeper architecture access:

```csharp
// Get appropriate KeeperMap for commands
var keeperMap = KeeperAccessHelper.GetCommandKeeperMap();

// Convert Handle to IHandleable for DispatchAction
var handleable = KeeperAccessHelper.ConvertHandleToIHandleable(handle);

// System diagnostics
var status = KeeperAccessHelper.GetKeeperSystemStatus();
```

### Faction.GetGameEventBus()

New method to access the faction's `_gameEventBus` field:

```csharp
/// <summary>
/// Get the GameEventBus for this faction
/// Maps to: _gameEventBus protected field
/// Used for InteractionManager.DispatchAction calls
/// </summary>
public object? GetGameEventBus()
{
    return this.GetFieldValue<object>("_gameEventBus");
}
```

### EnhancedEventBus (Timing)

Provides event-driven initialization for command execution:

```csharp
// Subscribe to game load completion
EnhancedEventBus.SubscribeToOnLoadFinished(OnLoadFinished);

// Event handler
private void OnLoadFinished(OnLoadFinishedEvent evt)
{
    // Safe to execute commands now
    ExecuteCommands();
}
```

## 🎮 Working Examples

### Commands Demo (TextAction)
See `Individual-Mods/PerAspera-CommandsDemo/` for complete implementation:

- **F9** → Import 10.0 CHG
- **F10** → Import 10.0 Ice
- Complete error handling and logging
- SDK wrapper integration

### Test Command Dispatch (CommandBus)
See `Individual-Mods/TestCommandDispatch/` for native command implementation:

- **Automatic execution** on game load via `OnLoadFinishedEvent`
- **Reflection-based dispatch** for `CmdFactionResourceAllocation`
- **Dual validation** (TextAction + CommandBus methods)
- **Performance monitoring** and error handling

## 🔍 Debugging Tips

### Common Issues

#### TextAction Issues
1. **GameEventBus is null** - Faction not fully initialized
2. **Handle conversion fails** - Keeper system not ready
3. **TextAction creation fails** - Type discovery issues
4. **DispatchAction exception** - Invalid command parameters

#### CommandBus Issues
1. **Assembly load fails** - ScriptsAssembly not accessible
2. **Type resolution fails** - Command type name mismatch
3. **Property mapping fails** - IL2CPP property access issues
4. **CommandBus unavailable** - Called too early in game lifecycle

### Log Analysis

```
✅ TextAction Success:
[Info] Dispatch Action ImportResource PlayerFaction Ice 10.0

✅ CommandBus Success:
[Info] CommandBus dispatch completed for CmdFactionResourceAllocation

❌ Failure Pattern:
[Error] InteractionManager dispatch failed: Exception has been thrown by the target of an invocation.
[Error] CommandBus dispatch failed: Type resolution error
```

### Timing Considerations

**TextAction:** Can be called after faction initialization
**CommandBus:** Requires full game load (use `OnLoadFinishedEvent`)

## 📊 Performance Comparison

| Aspect | TextAction | CommandBus |
|--------|------------|------------|
| **Setup Complexity** | High (Keeper, handles, conversion) | Medium (reflection, types) |
| **Execution Speed** | Fast (direct dispatch) | Fast (native calls) |
| **Type Safety** | Runtime string validation | Compile-time generics |
| **IL2CPP Compatibility** | Good | Excellent |
| **Initialization Timing** | Early (post-faction) | Late (post-load) |
| **Error Handling** | Basic | Advanced |
| **SDK Integration** | Partial | Full |

## 🎯 Best Practices

### Choose the Right System

**Use TextAction when:**
- Simple command execution
- Console-style commands
- Early initialization needed
- String-based parameter passing

**Use CommandBus when:**
- Type safety required
- Native command objects available
- Performance critical
- Complex parameter structures
- Full game state available

### Implementation Pattern

```csharp
public class CommandExecutor : MonoBehaviour
{
    void Start()
    {
        // Subscribe to appropriate timing event
        EnhancedEventBus.SubscribeToOnLoadFinished(OnGameReady);
    }

    private void OnGameReady(OnLoadFinishedEvent evt)
    {
        // Prefer CommandBus for native operations
        if (UseNativeCommands)
        {
            ExecuteViaCommandBus();
        }
        else
        {
            ExecuteViaTextAction();
        }
    }

    private void ExecuteViaCommandBus()
    {
        // Type-safe, performant
        var result = FactionWrapper.DispatchCommand<CmdFactionResourceAllocation>(
            "AllocateResources", resourceType, amount, faction
        );
    }

    private void ExecuteViaTextAction()
    {
        // Flexible, early execution
        var textAction = TextAction.FromTabbedString(commandString);
        InteractionManager.DispatchAction(handleable, eventBus, textAction, context);
    }
}
```

## 📚 Technical References

### TextAction System
- **InteractionManager.cs** - Core command dispatcher (ScriptsAssembly)
- **TextAction.cs** - Command object structure (ScriptsAssembly)
- **GameEventBus.cs** - Event system infrastructure (ScriptsAssembly)
- **TextActionWidget.cs** - Native UI for command creation validation

### CommandBus System
- **CommandBus.cs** - Native command dispatcher (ScriptsAssembly)
- **Cmd*.cs** - Native command classes (ScriptsAssembly)
- **FactionWrapper.cs** - SDK dispatch utilities
- **EnhancedEventBus.cs** - Timing event system

### Integration Points
- **BaseGamePatches.cs** - Harmony patches for timing events
- **OnLoadFinishedEvent.cs** - Game load completion event
- **KeeperAccessHelper.cs** - Distributed system access

## 🎯 Next Steps

1. **Command Discovery** - Map all available native command types
2. **Parameter Validation** - Add runtime validation for both systems
3. **Result Handling** - Capture and process command execution results
4. **UI Integration** - Create command interfaces using native patterns
5. **Performance Optimization** - Cache reflection lookups for CommandBus
6. **Error Recovery** - Implement fallback mechanisms between systems

---

**Last Updated:** December 22, 2025
**SDK Version:** Current development version
**Game Version:** Per Aspera latest
**Tested Systems:** TextAction (F9/F10), CommandBus (OnLoadFinished)