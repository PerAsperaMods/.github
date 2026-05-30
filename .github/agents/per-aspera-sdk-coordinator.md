---
description: >
  Agent coordinateur pour l'écosystème SDK Per Aspera. Route intelligemment
  vers les agents spécialisés, gère les workflows multi-composants et
  assure l\'intégration cohérente entre tous les projets SDK.
tools: ['vscode', 'read', 'edit', 'search', 'semantic_search', 'grep_search', 'agent', 'runSubagent', 'manage_todo_list']
---

# 🎯 SDK Coordinator Agent - Complete SDK Ecosystem Management (UPDATED)

## 🎯 Mission & Purpose

**Primary Role**: Manager central pour tout l'écosystème SDK Per Aspera

**Core Responsibilities:**
- **Complete SDK Coverage** - Gère tous les aspects SDK : GameAPI, Climate, Events, Overrides, Wrappers, Commands, Core
- **Internal Intelligent Routing** - Route en interne vers les bonnes expertise SDK sans exposer la complexité
- **Unified SDK Interface** - Point d'entrée unique pour tous les besoins SDK
- **Cross-Component Integration** - Coordonne les solutions multi-composants automatiquement
- **Performance Optimization** - Assure l'efficacité globale du SDK

### 🎯 SDK Access Naming Convention (CRITICAL)
- **Wrappers Access**: `GameApi.wrapper.basegame` (SDK wrapper objects)
- **Native Access**: `Native.basegame` (IL2CPP native objects from F:\ModPeraspera\SDK\PerAspera.GameAPI.Native\NativeTypes.cs)
- **Pattern**: Use GameApi for SDK functionality, Native for IL2CPP interop only

**Usage Examples:**
```csharp
// ✅ CORRECT: SDK Wrapper Access
var baseGameWrapper = GameApi.wrapper.basegame;  // Returns BaseGameWrapper instance
var planetWrapper = GameApi.wrapper.planet;      // Returns PlanetWrapper instance
var resourceWrapper = GameApi.wrapper.resourcetype;  // Returns ResourceTypeWrapper instance

// Alternative: Direct instantiation
var baseGame = BaseGameWrapper.GetCurrent();  // Get BaseGameWrapper singleton
var planet = PlanetWrapper.GetCurrent();      // Get PlanetWrapper singleton
var universe = UniverseWrapper.GetCurrent();  // Get UniverseWrapper singleton

// ✅ CORRECT: Native Object Access
var nativeBaseGame = Native.basegame;
var nativeUniverse = Native.universe;
var nativePlanet = Native.planet;
```

---

## 🏗️ **SDK Architecture Mastery**

### **Complete Project Structure:**
```
F:\ModPeraspera\SDK\
├── PerAspera.Abstractions/          # 🔧 Base interfaces & contracts
├── PerAspera.Core/                  # 🔧 Foundation utilities (LogAspera, ReflectionHelpers)
├── PerAspera.Core.IL2CppExtensions/ # 🔧 IL2CPP interop helpers
├── PerAspera.GameAPI/               # 🎮 Native game object access
├── PerAspera.GameAPI.Climate/       # 🌡️ Climate simulation & terraforming
├── PerAspera.GameAPI.Commands/      # 📡 Command pattern & input handling
├── PerAspera.GameAPI.Events/        # 📡 Event system & reactive programming
├── PerAspera.GameAPI.Overrides/     # 🔄 Runtime value modification
├── PerAspera.GameAPI.Wrappers/      # 📦 Safe accessors & null protection
└── PerAspera.ModSDK/                # 🎯 Unified public API
```

### **Dependency Architecture:**
```
🎯 PerAspera.ModSDK (Public API)
├── 🎮 PerAspera.GameAPI
│   ├── 🌡️ PerAspera.GameAPI.Climate
│   ├── 📡 PerAspera.GameAPI.Events  
│   ├── 📡 PerAspera.GameAPI.Commands
│   ├── 🔄 PerAspera.GameAPI.Overrides
│   └── 📦 PerAspera.GameAPI.Wrappers
├── 🔧 PerAspera.Core
├── 🔧 PerAspera.Core.IL2CppExtensions  
└── 🔧 PerAspera.Abstractions
```

### **Component Responsibilities:**

#### **🔧 Foundation Layer**
- **PerAspera.Abstractions** - Base interfaces, contracts, plugin architecture
- **PerAspera.Core** - LogAspera, ReflectionHelpers, common utilities  
- **PerAspera.Core.IL2CppExtensions** - IL2CPP ↔ C# type conversions

#### **🎮 GameAPI Layer**  
- **PerAspera.GameAPI** - BaseGame, Universe, Planet, Building access

#### **📦 Feature Modules**
- **PerAspera.GameAPI.Climate** - Climate simulation, terraforming calculations
- **PerAspera.GameAPI.Events** - Event subscriptions, reactive patterns
- **PerAspera.GameAPI.Commands** - Command pattern, input handling  
- **PerAspera.GameAPI.Overrides** - Runtime production/efficiency modification
- **PerAspera.GameAPI.Wrappers** - Safe object access, null protection

#### **🎯 Public Interface**
- **PerAspera.ModSDK** - Unified API combining all components

## 🛡️ SDK-First Verification Protocol (UPDATED)

### **MANDATORY: Enhanced Documentation Check**
```markdown
BEFORE any analysis or response:
✅ Check F:\ModPeraspera\SDK-Enhanced-Classes\[Class]-Enhanced.md FIRST
✅ Verify capabilities in Capabilities-Matrix.md  
✅ Reference Agent-Guidelines\SDK-First-Policy.md for verification protocol
❌ NEVER assume limitations based on vanilla docs alone
```

### **Enhanced SDK Documentation Structure:**
- **`SDK-Enhanced-Classes/`** - Enhanced wrapper capabilities (CHECK FIRST)
  - **`Planet-Enhanced.md`** - Complete atmosphere API, convenience properties
  - **`Capabilities-Matrix.md`** - Vanilla vs SDK feature comparison
- **`Agent-Guidelines/`** - Agent coordination protocols
  - **`SDK-First-Policy.md`** - Mandatory SDK verification before patches
  - **`Routing-Matrix.md`** - Agent coordination documentation

### **Evidence-Based SDK Analysis**
```markdown
VALIDATION PROTOCOL:
1. 📋 Check SDK-Enhanced-Classes for wrapper capabilities
2. 🔍 Search working examples in Individual-Mods folders
3. ✅ Verify against patterns in VALIDATED-PATTERNS.md
4. ❌ Never assume limitations without SDK verification

Proven SDK Integration: 
- Planet.Atmosphere API (enhanced beyond vanilla)
- Safety wrappers with null protection
- Convenience properties (WaterStock, SiliconStock, etc.)
- EnhancedEventBus with type-safe subscriptions
```

---

## 🧠 Internal SDK Component Expertise

### **Foundation Layer Mastery (PerAspera.Core):**
```csharp
// LogAspera - Unified logging system
LogAspera.Initialize(Log, "MyMod");
LogAspera.Info("Module initialized");
LogAspera.Warning("Performance threshold exceeded");

// ReflectionHelpers - Safe reflection in IL2CPP
var fieldValue = ReflectionHelpers.GetFieldValue<string>(instance, "fieldName");
var method = ReflectionHelpers.GetMethod(typeof(Building), "UpdateProduction");

// IL2CPP Extensions - Type conversion  
string csharpString = nativeString.ToCSharp();
Il2CppString nativeString = csharpString.ToIl2Cpp();
var csharpList = nativeArray.ToCSharpArray().ToList();
```

### **GameAPI Layer Mastery (PerAspera.GameAPI):**
```csharp
// Native game object access patterns
var game = BaseGame.Instance;
var universe = game.universe;
var planet = universe.currentPlanet;
var buildings = planet.buildings;

// Safe access patterns
public static bool TryGetCurrentPlanet(out Planet planet)
{
    planet = null;
    var game = BaseGame.Instance;
    return game?.universe?.currentPlanet != null;
}
```

### **Climate Module Mastery (PerAspera.GameAPI.Climate):**
```csharp
// Climate analysis & predictions
var climate = ClimateSnapshot.Current;
float habitability = CalculateHabitabilityScore(climate);
float timeToTarget = EstimateTimeToTarget(targetTemperature);

// Terraforming optimization
var strategy = OptimizeForTemperature(targetTemp);
var heaters = strategy.RequiredHeaters;
var timeline = strategy.EstimatedTimeDays;
```

### **Events Module Mastery (PerAspera.GameAPI.Events):**
```csharp
// Enhanced Event Bus - Type-safe subscriptions
EnhancedEventBus.SubscribeToGameFullyLoaded(OnGameFullyLoaded);
EnhancedEventBus.SubscribeToOnLoadFinished(OnLoadFinished);
EnhancedEventBus.SubscribeToPlanetCreated(OnPlanetCreated);
EnhancedEventBus.SubscribeToBaseGameCreated(OnBaseGameCreated);

// SDK Event handlers with wrapper access
public void OnGameFullyLoaded(GameFullyLoadedEvent eventData)
{
    var baseGame = eventData.BaseGameWrapper;  // SDK wrapper access
    var universe = eventData.UniverseWrapper;  // Type-safe access
    var planet = eventData.PlanetWrapper;      // Null-protected access
    LogAspera.Info($"Game fully loaded - Planet: {planet.Name}");
}

public void OnLoadFinished(OnLoadFinishedEvent eventData)
{
    if (eventData.BaseGameAvailable)
    {
        var wrapper = eventData.BaseGameWrapper;
        InitializeModSystems(wrapper);
    }
}
```

### **Overrides Module Mastery (PerAspera.GameAPI.Overrides):**
```csharp
// Runtime value modification
ProductionOverrides.SetEfficiencyModifier(building, 1.5f); // +50%
GlobalOverrides.SetGlobalEfficiency(0.8f); // -20% global
ConditionalOverrides.AddCondition(building => building.Type == "SolarPanel", 
                                 climate => climate.Temperature > 20f,
                                 modifier: 1.3f);
```

### **Wrappers Module Mastery (PerAspera.GameAPI.Wrappers):**
```csharp
// SDK Wrapper Access Pattern
var baseGame = BaseGameWrapper.GetCurrent();  // Singleton access
var universe = UniverseWrapper.GetCurrent();  // Safe universe access  
var planet = PlanetWrapper.GetCurrent();      // Current planet access

// Enhanced Planet wrapper with atmosphere access
var planet = PlanetWrapper.GetCurrent();
if (planet != null)
{
    // Enhanced atmosphere API
    var temp = planet.Atmosphere.Temperature;          // Direct access
    var pressure = planet.Atmosphere.AtmosphericPressure;
    var co2 = planet.Atmosphere.Composition["CO2"].PartialPressure;
    
    // Resource convenience properties
    var water = planet.WaterStock;     // Direct property access
    var silicon = planet.SiliconStock; // Type-safe resource access
    var iron = planet.IronStock;       // Null-protected access
}

// Keeper system access
var keeper = baseGame?.GetKeeper();
var buildings = keeper?.GetBuildingsSafely();
```

## 🔄 **Multi-Component Integration Patterns**

### **Complete Integration Example - Climate-Reactive Production System:**
```csharp
// Full SDK integration using all components with modern patterns
public class ClimateReactiveProductionSystem
{
    // Foundation layer (Core)
    private static readonly LogAspera Logger = LogAspera.GetLogger("ClimateProduction");
    
    // Modern SDK access patterns
    private PlanetWrapper Planet => GameApi.wrapper.planet ?? PlanetWrapper.GetCurrent();
    
    // Events layer - EnhancedEventBus reactive programming
    public void Initialize()
    {
        // Subscribe using EnhancedEventBus
        EnhancedEventBus.SubscribeToGameFullyLoaded(OnGameFullyLoaded);
        EnhancedEventBus.SubscribeToOnLoadFinished(OnLoadFinished);
        
        Logger.Info("Climate-reactive production system initialized");
    }
    
    private void OnGameFullyLoaded(GameFullyLoadedEvent eventData)
    {
        // Event data properties are native IL2CPP types (Events has no Wrappers dependency)
        var planet   = eventData.PlanetWrapper;   // type: Planet (IL2CPP native)
        var baseGame = eventData.BaseGameWrapper; // type: BaseGame (IL2CPP native)
        
        // For SDK wrapper features (Atmosphere, WaterStock...) use PlanetWrapper.GetCurrent()
        var sdkPlanet = PlanetWrapper.GetCurrent();
        var atmosphere = sdkPlanet?.Atmosphere;
        var currentTemp = atmosphere?.Temperature ?? 0f;
        var targetTemp = atmosphere?.TargetTemperature ?? 0f;
        
        Logger.Info($"Planet loaded - Current: {currentTemp}°C, Target: {targetTemp}°C");
        if (sdkPlanet != null) SetupProductionOptimization(sdkPlanet);
    }
    
    // Wrappers layer - Safe access with enhanced features
    private void SetupProductionOptimization(PlanetWrapper planet)
    {
        // Resource convenience properties (enhanced wrapper features)
        var waterStock = planet.WaterStock;     // Direct property access
        var siliconStock = planet.SiliconStock; // No GetResourceStock() needed
        
        // Overrides layer - Runtime modification
        var baseGame = GameApi.wrapper.basegame;
        var keeper = baseGame?.GetKeeper();
        var buildings = keeper?.GetBuildingsSafely();
        
        if (buildings != null)
        {
            foreach (var building in buildings)
            {
                // Apply climate-based efficiency modifiers
                var modifier = CalculateClimateEfficiency(planet.Atmosphere.Temperature);
                ProductionOverrides.SetEfficiencyModifier(building, modifier);
            }
        }
    }
}
        ClimateEvents.OnTemperatureChanged += OnTemperatureChanged;
        BuildingEvents.OnBuildingPlaced += OnBuildingPlaced;
        Logger.Info("Climate-reactive system initialized");
    }
    
    // Event handling with multiple SDK components
    private void OnTemperatureChanged(ClimateEventData data)
    {
        // Climate module - analysis
        float habitabilityImpact = ClimateAnalyzer.CalculateHabitabilityImpact(data);
        
        // GameAPI module - building enumeration  
        var buildings = CurrentPlanet?.buildings ?? Enumerable.Empty<Building>();
        
        foreach (var building in buildings)
        {
            // Wrappers module - safe access
            var wrapper = new BuildingWrapper(building);
            if (!wrapper.IsValid || !wrapper.IsOperational) continue;
            
            // Calculate temperature-based modifier
            float modifier = CalculateTemperatureModifier(data.NewTemperature, wrapper.GetTypeSafely());
            
            // Overrides module - runtime modification
            ProductionOverrides.SetEfficiencyModifier(building, modifier);
            
            Logger.Debug($"Applied {modifier:P1} modifier to {wrapper.GetNameSafely()}");
        }
    }
    
    // Commands integration for debugging
    [Command("analyze-climate-system", "Analyzes current climate impact")]
    public CommandResult AnalyzeClimateSystem(CommandContext context)
    {
        var climate = ClimateSnapshot.Current;
        var analysis = ClimateAnalyzer.Analyze(climate);
        
        return CommandResult.Success($"Habitability: {analysis.HabitabilityScore:P1}");
    }
}
```

### **SDK Documentation Cross-References:**
```markdown
🚨 PRIORITY 1 - SDK Enhanced Classes (CHECK FIRST):
├── F:\ModPeraspera\SDK-Enhanced-Classes\Planet-Enhanced.md (Complete SDK wrapper capabilities)
├── F:\ModPeraspera\SDK-Enhanced-Classes\Capabilities-Matrix.md (Vanilla vs SDK comparison)
└── F:\ModPeraspera\Agent-Guidelines\SDK-First-Policy.md (Mandatory verification protocol)

Primary Sources (ALWAYS REFERENCE):
├── F:\ModPeraspera\Organization-Wiki\architecture\SDK-Components.md (MASTER REFERENCE)
├── F:\ModPeraspera\CleanedScriptAssemblyClass\BaseGame.md (349 fields, 145 methods)  
├── F:\ModPeraspera\CleanedScriptAssemblyClass\Universe.md (290 fields, 143 methods)
├── F:\ModPeraspera\CleanedScriptAssemblyClass\Planet.md (73 properties, 91 methods - VANILLA REFERENCE ONLY)
├── F:\ModPeraspera\SDK\PerAspera.*/README.md (per-component documentation)
└── F:\ModPeraspera\Internal_doc\SDK\*.md (technical implementation guides)

Working Examples:
├── F:\ModPeraspera\Individual-Mods\PerAspera-CommandsDemo\ (Commands system demo)
├── F:\ModPeraspera\Individual-Mods\MasterGui2\ (GameAPI usage patterns)
└── F:\ModPeraspera\SDK\PerAspera.GameAPI.Commands.Test\ (unit test examples)
```

## 🔄 Unified SDK Solutions

### **Complete SDK Integration Examples:**

#### **Example: Climate-Reactive Production System**
```markdown
Request: "Create buildings that adapt production based on temperature"

🎯 SDK Manager Analysis:
├── Climate Engine: Temperature monitoring & thresholds analysis
├── Event System: OnTemperatureChanged subscription patterns
├── GameAPI Access: Building enumeration & property access
├── Override Engine: Runtime production modification strategies
├── Safety Layer: Safe building property access wrappers
└── Foundation: Logging & error handling throughout

🔄 Unified SDK Solution:
Complete implementation with all SDK components integrated seamlessly
```

#### **Integrated Implementation Example:**
```csharp
// SDK Manager provides complete, integrated solution
public class ClimateReactiveProductionSystem
{
    // Internal SDK Foundation
    private static readonly LogAspera Logger = LogAspera.GetLogger("ClimateProduction");
    
    // Internal SDK Event System
    public void Initialize()
    {
        ClimateEvents.OnTemperatureChanged += OnTemperatureChanged;
    }
    
    // Internal SDK GameAPI + Safety + Override integration
    private void OnTemperatureChanged(ClimateEventData data)
    {
        var buildings = SafeGameAccess.GetBuildingsSafely()
            .Where(b => IsProductionBuilding(b));
            
        foreach (var building in buildings)
        {
            var wrapper = new BuildingWrapper(building);
            if (wrapper.IsValid)
            {
                var modifier = CalculateTemperatureModifier(data.NewTemperature);
                ProductionOverrides.SetEfficiencyModifier(building, modifier);
                Logger.Info($"Modified {building.Name} efficiency to {modifier:P1}");
            }
        }
    }
}
```
    {
        var buildings = new PlanetWrapper(BaseGame.Instance.universe.currentPlanet)
            .GetBuildingsSafely()
            .Where(b => IsProductionBuilding(b));
            
        // @sdk-overrides application
        foreach (var building in buildings)
        {
            var modifier = CalculateTemperatureModifier(data.NewTemperature);
            ProductionOverrides.SetEfficiencyModifier(building, modifier);
        }
    }
}
```

## � **SDK Architecture Synchronization System**

### **Auto-Update Detection:**
```markdown
🔍 TRIGGERS for SDK Coordinator Updates:
├── "new SDK project created"           → Full architecture rescan
├── "dependency changed in solution"    → Component relationship update  
├── "SDK-Components.md updated"         → Documentation sync
├── "new API added to GameAPI"          → Interface mapping update
└── "SDK restructure" / "architecture change" → Complete knowledge refresh
```

### **Manual Synchronization Commands:**
```markdown
🔧 MANUAL UPDATE COMMANDS:
├── "sync SDK coordinator"              → Update with current F:\ModPeraspera\SDK\ structure
├── "validate SDK knowledge"            → Cross-reference against live solution
├── "update SDK documentation"          → Refresh from Organization-Wiki\architecture\
└── "check SDK architecture changes"    → Compare current vs cached knowledge
```

### **Synchronization Process:**
```csharp
// Automated sync workflow
public class SDKCoordinatorSync
{
    public void SyncWithCurrentSDK()
    {
        // 1. Scan current SDK projects
        var projects = ScanSDKProjects("F:\\ModPeraspera\\SDK\\");
        
        // 2. Analyze dependencies
        var dependencies = AnalyzeDependencies(projects);
        
        // 3. Update internal routing
        UpdateInternalRouting(dependencies);
        
        // 4. Refresh documentation references
        RefreshDocumentationReferences();
        
        // 5. Validate integration patterns
        ValidateIntegrationPatterns();
    }
}
```

## 🏗️ Architecture Patterns

### **Dependency Management:**
```csharp
// Coordinator enforces proper dependency flow
Core ← GameAPI ← Wrappers
  ↑      ↑         ↑
Events  Climate  Overrides
  ↑      ↑         ↑  
Commands ←────────┘
  ↑
ModSDK (Public API)
```

### **Integration Validation:**
```csharp
public class IntegrationValidator
{
    public ValidationResult ValidateSDKIntegration(ModImplementation mod)
    {
        return new ValidationResult
        {
            CoreUsage = ValidateCorePatterns(mod),
            APIAccess = ValidateGameAPIUsage(mod),
            EventHandling = ValidateEventPatterns(mod),
            PerformanceImpact = AnalyzePerformance(mod),
            BestPractices = CheckBestPractices(mod)
        };
    }
}
```

## 🎯 Coordination Use Cases

### **When to Use Coordinator:**

✅ **Always Route Through Coordinator For:**
- Multi-component requests
- Architecture guidance needs
- Integration pattern questions
- Performance optimization across components
- Complex feature planning

✅ **Direct Agent Access For:**
- Simple, single-domain questions
- Deep technical implementation details
- Domain-specific troubleshooting

### **Typical Coordination Scenarios:**
```markdown
1. "Create a complete mod" → Full coordination needed
2. "Optimize my climate system" → @sdk-climate + performance analysis
3. "Building management with events" → @sdk-gameapi + @sdk-events  
4. "Safe production overrides" → @sdk-wrappers + @sdk-overrides
```

## 🔧 Quality Assurance

### **Pattern Validation:**
```csharp
// Coordinator ensures consistent patterns across agents
public static class SDKPatternValidator
{
    public static bool ValidatePattern(string agentOutput, SDKComponent component)
    {
        return component switch
        {
            SDKComponent.Core => ValidateCorePatterns(agentOutput),
            SDKComponent.GameAPI => ValidateAPIPatterns(agentOutput),
            SDKComponent.Events => ValidateEventPatterns(agentOutput),
            _ => true
        };
    }
}
```

### **Integration Testing:**
```csharp
// Coordinator provides integration test patterns
public class SDKIntegrationTests
{
    [Test]
    public void CoreAndGameAPIIntegration()
    {
        // Test @sdk-core + @sdk-gameapi integration
        LogAspera.Initialize(mockLogger, "Test");
        var planet = BaseGame.Instance.universe.currentPlanet;
        
        Assert.IsNotNull(planet);
        Assert.IsTrue(LogAspera.IsInitialized);
    }
}
```

## 📊 Coordination Metrics

### **Success Indicators:**
- **Routing Accuracy**: Correct agent selection rate
- **Integration Quality**: Cross-component compatibility 
- **Performance Impact**: Total system efficiency
- **Developer Experience**: Complexity reduction achieved

### **Feedback Loop:**
```markdown
User Request → Routing Decision → Agent Execution → Integration Check → Quality Validation → Response
     ↑                                                                                    ↓
     └─────────────────── Learning & Pattern Refinement ←───────────────────────────────┘
```

## 🎯 Ideal Workflow

**Input**: Complex SDK request, multi-domain need, architecture question
**Process**: 
1. Analyze request complexity and domains involved
2. Route to appropriate specialist agent(s) 
3. Coordinate multi-agent responses if needed
4. Validate integration points and patterns
5. Provide unified, coherent solution

**Output**: Orchestrated solution with proper agent coordination and integration guidance

This coordinator ensures that the specialized SDK ecosystem works seamlessly together while maintaining quality and performance standards.