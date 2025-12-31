# SDK Development Discoveries - December 2025

🎯 **Key discoveries made during SDK development and command system implementation**

## 🚀 Recent Breakthroughs

### TextAction Wrapper Implementation

**Date:** December 20, 2025  
**Discovery:** Complete TextAction wrapper with factory methods and native integration

**Key Components:**
- Factory methods: `Create()`, `FromTabbedString()`, `CreateAddResource()`
- Property access: DaysDelay, Command, Arguments, ShowInFrontend  
- Native integration: `GetNativeObject()` for InteractionManager.DispatchAction
- Safe type discovery via ReflectionHelpers to avoid Unity type corruption

**Files:**
- `F:\ModPeraspera\SDK\PerAspera.GameAPI.Wrappers\TextAction.cs`
- Documentation: `Organization-Wiki/sdk/TextAction-Wrapper.md`

### InteractionManager Command System

**Date:** December 20, 2025  
**Discovery:** Complete command execution pipeline using InteractionManager.DispatchAction

**Signature Discovered:**
```csharp
public static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)
```

**Critical Requirements:**
1. **IHandleable sender** - Faction converted via KeeperAccessHelper.ConvertHandleToIHandleable()
2. **GameEventBus** - Must be real bus from faction.GetGameEventBus() (NOT null!)
3. **TextAction native object** - Via textAction.GetNativeObject()
4. **Context string** - Source identifier

**Working Commands:**
- `ImportResource\tPlayerFaction\tIce\t10.0` ✅
- `ImportResource\tPlayerFaction\tCHG\t10.0` ✅  
- `ImportResource\tPlayerFaction\tNITROGEN\t10.0` ✅
- `ImportResource\tPlayerFaction\tOXYGEN\t10.0` ✅

### GameEventBus Access in Faction

**Date:** December 20, 2025  
**Discovery:** Faction class has protected `_gameEventBus` field accessible via reflection

**Native Field:**
```csharp
[FieldOffset(Offset = "0x290")]
[Token(Token = "0x40005D0")]
[FinishInject]
[DontSave]
protected GameEventBus _gameEventBus;
```

**SDK Wrapper Method:**
```csharp
public object? GetGameEventBus()
{
    return this.GetFieldValue<object>("_gameEventBus");
}
```

### KeeperAccessHelper - Distributed Architecture

**Date:** December 20, 2025  
**Discovery:** Keeper system is distributed across Universe, BaseGame, specialized systems

**Key Methods:**
```csharp
// Primary for faction commands
public static KeeperWrapper? GetUniverseKeeper()

// Handle→IHandleable conversion for DispatchAction
public static object? ConvertHandleToIHandleable(object handle)

// Command-optimized KeeperMap selection  
public static KeeperMapWrapper? GetCommandKeeperMap()

// System diagnostics
public static string GetKeeperSystemStatus()
```

**Architecture Discovery:**
- Universe.keeper → Factions, planets, resources (PRIMARY)
- BaseGame.keeper → Core game state  
- GameEventBus._keeper → Event system entities
- Swarm._keeper → Unit swarm management
- Way._keeper → Pathfinding entities

## 🔧 Technical Patterns Validated

### IL2CPP Type Safety

**Rule:** Always use `System.Type` instead of bare `Type`

```csharp
// ✅ CORRECT
private static System.Type? _textActionType;

// ❌ INCORRECT (conflicts between assemblies)
private static Type? _textActionType;
```

### Safe Type Discovery

**Pattern:** Use ReflectionHelpers instead of AppDomain iteration

```csharp
// ✅ SAFE (avoids Unity type corruption)
var type = ReflectionHelpers.FindType("TextAction");

// ❌ DANGEROUS (causes Unity CoreModule errors)
var type = AppDomain.CurrentDomain.GetAssemblies()
    .SelectMany(a => a.GetTypes())
    .FirstOrDefault(t => t.Name == "TextAction");
```

### IL2CPP Field Access

**Pattern:** Use Core.IL2CPP extensions for property access

```csharp
// Field reading
public string Command => this.GetFieldValue<string>("command") ?? "";

// Field writing  
public string Command 
{
    set => this.SetFieldValue("command", value);
}

// Method invocation with return value
var result = this.InvokeMethod<string>("ToTabbedString");
```

## 🎮 Working Examples

### Complete Command Execution

**Location:** `Individual-Mods/PerAspera-CommandsDemo/CleanCommandsDemo.cs`

**Pipeline:**
1. Get faction: `universe.GetPlayerFaction()`
2. Convert handle: `KeeperAccessHelper.ConvertHandleToIHandleable(handle)`
3. Get event bus: `faction.GetGameEventBus()`
4. Create command: `TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0")`
5. Execute: `InteractionManager.DispatchAction(handleable, eventBus, textAction, context)`

**Result:** Successfully imports resources into player faction!

### TextAction Factory Usage

```csharp
// Parse command string
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0");

// Create programmatically
var textAction = TextAction.Create("ImportResource", "PlayerFaction", "Ice", "10.0");

// Convenience method
var textAction = TextAction.CreateAddResource("Ice", 10.0f);
```

## 🔍 Native Class Mappings

### TextAction (ScriptsAssembly)

```csharp
public class TextAction
{
    public float daysDelay;        // Execution delay
    public string command;         // Command name
    public List<string> arguments; // Parameters
    public bool showInFrontend;    // UI display
    
    public TextAction(string command, params string[] arguments) // Constructor used
    public string ToTabbedString() // Native tabbed format
}
```

### InteractionManager (ScriptsAssembly)

```csharp
public class InteractionManager : IEventListener, IDisposable
{
    // Command execution methods
    public static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)
    public static void DispatchAction(IHandleable asSender, GameEventBus bus, GameEvent action)
    public static void DispatchActions(IHandleable asSender, GameEventBus bus, List<TextAction> actions, string context)
}
```

### GameEventBus (ScriptsAssembly)

```csharp
public class GameEventBus : IDisposable
{
    private Keeper _keeper;  // Event system keeper
    // Event handling infrastructure
}
```

## 📚 Documentation Created

1. **Command-System.md** - Complete guide to command execution
2. **TextAction-Wrapper.md** - Detailed TextAction wrapper documentation  
3. **SDK-Discoveries.md** - This file with all findings

## 🎯 Next Research Areas

### Command Discovery

- Research other available commands beyond ImportResource
- Map command parameter requirements and validation
- Document command permissions and restrictions

### Event System Integration

- Explore GameEventBus event subscription patterns
- Research custom event creation and dispatch
- Map event system integration with command execution

### UI Integration

- Study TextActionWidget native UI patterns
- Research custom UI creation using SDK wrappers
- Explore UnityEngine.GUI integration possibilities

### Performance Optimization

- Profile command execution performance
- Optimize factory method caching
- Research batch command execution patterns

---

**Contributors:** AI Assistant, SDK Development Team  
**Last Updated:** December 20, 2025  
**Status:** Active Development