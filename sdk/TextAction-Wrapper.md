# TextAction Wrapper - SDK Implementation

🎯 **Complete TextAction wrapper with factory methods and IL2CPP integration**

## 📋 Overview

The TextAction wrapper provides safe access to the native TextAction class used by InteractionManager.DispatchAction. It includes factory methods, native type discovery, and proper IL2CPP integration.

## 🏗️ Class Structure

```csharp
public class TextAction : WrapperBase
{
    // Properties
    public float DaysDelay { get; set; }
    public string Command { get; set; }  
    public List<string> Arguments { get; set; }
    public bool ShowInFrontend { get; set; }
    
    // Factory methods
    public static TextAction? Create(string command, params string[] arguments)
    public static TextAction? FromTabbedString(string tabbedString)
    public static TextAction? CreateAddResource(string resourceName, int amount, float delay = 0f)
    
    // Native access
    public object GetNativeObject() => NativeObject;
}
```

## 🔧 Factory Methods

### Create Method

```csharp
/// <summary>
/// Create new TextAction with command and arguments
/// </summary>
public static TextAction? Create(string command, params string[] arguments)
{
    // Uses ReflectionHelpers.FindType("TextAction") for safe type discovery
    // Creates native instance with constructor(string command, params string[] arguments)
    return new TextAction(nativeInstance);
}
```

### FromTabbedString Method (Recommended)

```csharp
/// <summary>
/// Parse tabbed string format into TextAction
/// Format: "Command\tArg1\tArg2\tArg3"
/// Example: "FactionAddResourceDistributed\tIce\t1000"
/// </summary>
public static TextAction? FromTabbedString(string tabbedString)
{
    var parts = tabbedString.Split('\t');
    var command = parts[0];
    var arguments = parts.Skip(1).ToArray();
    return Create(command, arguments);
}
```

### CreateAddResource Convenience Method

```csharp
/// <summary>
/// Create TextAction for adding resources to faction
/// </summary>
public static TextAction? CreateAddResource(string resourceName, int amount, float delay = 0f)
{
    var textAction = Create("FactionAddResourceDistributed", resourceName, amount.ToString());
    if (textAction != null)
        textAction.DaysDelay = delay;
    return textAction;
}
```

## 🎯 Property Access

### Field Mapping

Properties map to native TextAction fields via IL2CPP extensions:

```csharp
// Native fields (from ScriptsAssembly/TextAction.cs):
public float daysDelay;        → DaysDelay property  
public string command;         → Command property
public List<string> arguments; → Arguments property
public bool showInFrontend;    → ShowInFrontend property
```

### Implementation

```csharp
public float DaysDelay
{
    get => this.GetFieldValue<float>("daysDelay");
    set => this.SetFieldValue("daysDelay", value);
}

public string Command  
{
    get => this.GetFieldValue<string>("command") ?? "";
    set => this.SetFieldValue("command", value);
}
```

## 🔗 Native Integration

### Type Discovery

Uses `ReflectionHelpers.FindType()` instead of `AppDomain.GetAssemblies()` to avoid Unity type loading issues:

```csharp
// Safe type discovery (avoids Unity CoreModule corruption)
var textActionType = PerAspera.Core.IL2CPP.ReflectionHelpers.FindType("TextAction");
```

### IL2CPP Extensions

Properties use `PerAspera.Core.IL2CPP` extension methods:

- `this.GetFieldValue<T>(fieldName)` - Safe field reading
- `this.SetFieldValue(fieldName, value)` - Safe field writing  
- `this.InvokeMethod<T>(methodName)` - Safe method invocation

### ToString Implementation

```csharp
public override string ToString()
{
    // Try native ToTabbedString method first
    var nativeToString = this.InvokeMethod<string>("ToTabbedString");
    if (!string.IsNullOrEmpty(nativeToString))
        return nativeToString;
        
    // Fallback to manual formatting
    var command = this.GetFieldValue<string>("command") ?? "UnknownCommand";
    var arguments = this.GetFieldValue<List<string>>("arguments") ?? new List<string>();
    return $"{command}\t{string.Join("\t", arguments)}";
}
```

## 🎮 Usage Examples

### Basic Command Creation

```csharp
// Create import resource command
var textAction = TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0");

if (textAction != null)
{
    // Modify properties
    textAction.DaysDelay = 1.0f;
    textAction.ShowInFrontend = true;
    
    // Use in DispatchAction
    InteractionManager.DispatchAction(faction, eventBus, textAction.GetNativeObject(), "MyMod");
}
```

### Batch Command Creation

```csharp
var commands = new List<TextAction>
{
    TextAction.FromTabbedString("ImportResource\tPlayerFaction\tIce\t10.0"),
    TextAction.FromTabbedString("ImportResource\tPlayerFaction\tCHG\t15.0"),
    TextAction.FromTabbedString("ImportResource\tPlayerFaction\tOXYGEN\t5.0")
};

// Convert to native objects for batch execution
var nativeActions = commands.Select(cmd => cmd.GetNativeObject()).ToList();
```

## 🛠️ Error Handling

### Type Discovery Failures

```csharp
if (textActionType == null)
{
    Log.Error("[TextAction] Native TextAction type not found via ReflectionHelpers");
    return null;
}
```

### Field Access Failures

```csharp
catch (Exception ex)
{
    Log.Warning($"[TextAction] ToString failed: {ex.Message}");
    return "TextAction[FieldAccessFailed]";
}
```

### Constructor Failures

```csharp
var nativeInstance = Activator.CreateInstance(textActionType, command, arguments);
if (nativeInstance == null)
{
    Log.Error("[TextAction] Failed to create native TextAction instance");
    return null;
}
```

## 🔍 Native Class Reference

### Constructors Available

```csharp
public TextAction()                                    // Empty constructor
public TextAction(string command, params string[] arguments) // Primary constructor  
public TextAction(TextAction other)                   // Copy constructor
```

### Native Methods

```csharp
public override string ToString()     // Basic string representation
public string ToTabbedString()        // Tabbed format (command\targ1\targ2...)
```

## 📚 Related Classes

### TextActionWidget (Native UI)

Validates our wrapper approach - native UI creates TextAction objects exactly as our wrapper does:

```csharp
// TextActionWidget confirms TextAction object usage in native UI
// Our wrapper mirrors this native construction pattern
```

### InteractionManager Integration

```csharp
// TextAction objects are designed for InteractionManager.DispatchAction
public static void DispatchAction(IHandleable asSender, GameEventBus bus, TextAction textAction, string context)
```

## 🎯 Performance Considerations

### Object Lifecycle

- **Creation**: One-time reflection lookup per factory call
- **Field Access**: Direct IL2CPP field access via extensions  
- **Method Invocation**: Cached method lookup in extensions
- **Memory**: Minimal wrapper overhead, native object lifecycle managed

### Best Practices

1. **Use FromTabbedString()** - Most reliable parsing method
2. **Cache TextActions** - Reuse for repeated commands
3. **Batch operations** - Use DispatchActions for multiple commands
4. **Error handling** - Always check for null returns

---

**Implementation:** `F:\ModPeraspera\SDK\PerAspera.GameAPI.Wrappers\TextAction.cs`  
**Dependencies:** PerAspera.Core.IL2CPP, ReflectionHelpers  
**Last Updated:** December 20, 2025