# has
*Auto-generated from IL2CPP decompiled code*

## Properties

*No properties found*

## Fields

- `GameEventBus _gameEventBus`
- `System.Type? _textActionType` [static]
- `Type? _textActionType` [static]
- `string Command`
- `float daysDelay`
- `string command`
- `List<string> arguments`
- `bool showInFrontend`
- `Keeper _keeper`

## Methods

### `ConvertHandleToIHandleable`

```csharp
static object? ConvertHandleToIHandleable(object handle)
```

### `DispatchAction`

```csharp
static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)
```

```csharp
static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)
```

```csharp
static void DispatchAction(IHandleable asSender, GameEventBus bus, GameEvent action)
```

### `DispatchActions`

```csharp
static void DispatchActions(IHandleable asSender, GameEventBus bus, List<TextAction> actions, string context)
```

### `GetCommandKeeperMap`

```csharp
static KeeperMapWrapper? GetCommandKeeperMap()
```

### `GetGameEventBus`

```csharp
object? GetGameEventBus()
```

### `GetKeeperSystemStatus`

```csharp
static string GetKeeperSystemStatus()
```

### `GetUniverseKeeper`

```csharp
static KeeperWrapper? GetUniverseKeeper()
```

### `ToTabbedString`

```csharp
string ToTabbedString()
```

