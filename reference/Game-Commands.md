# Game Commands Reference

## 📚 Introduction

This reference documents all **55 validated game commands** that can be executed in Per Aspera. These commands are verified from the actual game runtime and can be used for:

- **Mod development** - Triggering game events from code
- **Console commands** - Direct execution via command system  
- **Scripting** - Automated testing and gameplay scenarios
- **Debugging** - State manipulation during development

**⚠️ Important:** This list is extracted from the actual game and represents the **complete, validated command set**. Do not assume other commands exist.

---

## 🎮 Command Categories

### 🏗️ Building & Infrastructure

| Command | Parameters | Description |
|---------|------------|-------------|
| `UnlockBuilding` | `<Faction> <BuildingType>` | Unlocks a specific building type for the faction |
| `AdditionalBuilding` | `<Faction> <BuildingType>` | Grants additional building of specified type |
| `UnlockWayType` | `<Faction> <WayType>` | Unlocks transportation way types (roads, tubes, etc.) |
| `UnlockEnhancement` | `<Faction> <EnhancementType>` | Enables building enhancement/upgrade |
| `DisableEnhancement` | `<Faction> <EnhancementType>` | Disables building enhancement/upgrade |

**Example:**
```csharp
// Unlock solar panels for player faction
SDK.Commands.Execute("UnlockBuilding", playerFaction, "SolarPanel");

// Allow tube transportation
SDK.Commands.Execute("UnlockWayType", playerFaction, "Tube");
```

### 🧪 Research & Knowledge

| Command | Parameters | Description |
|---------|------------|-------------|
| `UnlockKnowledge` | `<Faction> <KnowledgeType>` | Grants specific knowledge to faction |
| `UnlockSpecialProject` | `<Faction> <SpecialProjectType>` | Unlocks major terraforming projects |
| `ResearchTechnology` | `<Faction> <TechnologyType>` | Instantly completes technology research |
| `BeginQuest` | `<Faction> <QuestType>` | Starts a specific quest/mission |
| `AbortQuest` | `<Faction> <QuestType>` | Cancels active quest/mission |

**Example:**
```csharp
// Grant basic chemistry knowledge
SDK.Commands.Execute("UnlockKnowledge", playerFaction, "BasicChemistry");

// Research atmospheric processing
SDK.Commands.Execute("ResearchTechnology", playerFaction, "AtmosphericProcessing");
```

### 🌍 Resources & Environment

| Command | Parameters | Description |
|---------|------------|-------------|
| `RevealResourceVein` | `<Faction> <ResourceType>` | Reveals resource deposits on the map |
| `RevealSpecialSite` | `<Faction> <SpecialSite>` | Reveals special locations (caves, sites) |
| `SpawnResourceVein` | `<Faction> <ResourceType> <Number> <Number>` | Creates new resource vein at coordinates |
| `SpawnSpecialSite` | `<Faction> <SpecialSite>` | Creates special site on the map |
| `ImportResource` | `<Faction> <ImportMaterialType> <Number>` | Imports resources from Earth |

**Example:**
```csharp
// Reveal water ice deposits
SDK.Commands.Execute("RevealResourceVein", playerFaction, "WaterIce");

// Import 1000 units of rare metals
SDK.Commands.Execute("ImportResource", playerFaction, "RareMetals", 1000);
```

### 🌪️ Hazards & Disasters

| Command | Parameters | Description |
|---------|------------|-------------|
| `CreateSandstormHazard` | `<HazardSandstormType>` | Triggers sandstorm weather event |
| `CreateDevilHazard` | `<HazardDevilType>` | Creates dust devil weather phenomenon |
| `CreateAsteroidHazard` | `<HazardAsteroidType>` | Triggers asteroid impact event |
| `PolarNuclearDetonation` | `<Faction> <Number>` | Nuclear explosion at polar caps |
| `CometDown` | `<Faction> <Number>` | Comet impact event |
| `DeimosDown` | `<Faction> <Number>` | Deimos (moon) crash event |
| `BlackPolarDust` | `<Faction> <Number>` | Polar dust storm event |
| `AerobrakeAsteroid` | `<Faction> <AsteroidType> <Number>` | Controlled asteroid capture |

**Example:**
```csharp
// Create sandstorm in region
SDK.Commands.Execute("CreateSandstormHazard", "RegionalSandstorm");

// Trigger comet impact for terraforming
SDK.Commands.Execute("CometDown", playerFaction, 1);
```

### 🚀 Factions & Diplomacy

| Command | Parameters | Description |
|---------|------------|-------------|
| `Sabotage` | `<Faction>` | Perform sabotage action against faction |
| `SwitchSides` | `<Faction>` | Change faction allegiance |
| `ObliterateRivalBase` | `<Faction>` | Destroy rival faction base |
| `SpawnRivalBase` | `<Faction> <AIPlayerType>` | Create new AI faction base |
| `IncomingShip` | `<Faction> <Integer>` | Ship arrival from Earth |
| `DepartingShip` | `<Faction>` | Ship departure to Earth |

**Example:**
```csharp
// Spawn competitive AI faction
SDK.Commands.Execute("SpawnRivalBase", playerFaction, "Scientific");

// Incoming supply ship
SDK.Commands.Execute("IncomingShip", playerFaction, 3);
```

### 🗺️ Territory & Access

| Command | Parameters | Description |
|---------|------------|-------------|
| `GrantSectorPermission` | `<Faction> <Sector>` | Grants access to map sector |
| `UnlockSector` | `<Faction> <Sector>` | Fully unlocks sector for exploration |

**Example:**
```csharp
// Grant access to northern sector
SDK.Commands.Execute("GrantSectorPermission", playerFaction, "North");
```

### 🎭 Dialogue & Story

| Command | Parameters | Description |
|---------|------------|-------------|
| `StartDialogue` | `<Faction> <Person> <Dialogue>` | Begin conversation with character |
| `NotifyDialogue` | `<Faction> <Person> <Dialogue>` | Show optional dialogue notification |
| `NotifyMandatoryDialogue` | `<Faction> <Person> <Dialogue>` | Force mandatory dialogue |
| `DialogueSetPortrait` | `<Faction> <Person>` | Set character portrait display |

**Example:**
```csharp
// Start conversation with mission control
SDK.Commands.Execute("StartDialogue", playerFaction, "MissionControl", "Welcome");
```

### 💾 Game State & Variables

| Command | Parameters | Description |
|---------|------------|-------------|
| `SetBlackboardVariable` | `<Faction> <BlackboardKey> <Any>` | Set generic game variable |
| `SetBlackboardNumber` | `<Faction> <BlackboardKey> <Number>` | Set numeric game variable |
| `SetBlackboardBool` | `<Faction> <BlackboardKey> <Bool>` | Set boolean game variable |
| `SetBlackboardString` | `<Faction> <BlackboardKey> <Any>` | Set string game variable |

**Example:**
```csharp
// Set custom game state flag
SDK.Commands.Execute("SetBlackboardBool", playerFaction, "CustomModActive", true);

// Store numeric progress value
SDK.Commands.Execute("SetBlackboardNumber", playerFaction, "TerraformingProgress", 75);
```

### 🎮 UI & Game Control

| Command | Parameters | Description |
|---------|------------|-------------|
| `OpenPopup` | `<PopupType>` | Display popup window |
| `OpenAlert` | `<AlertType> <Bool>` | Show alert notification |
| `CloseAlert` | `<AlertType>` | Dismiss alert notification |
| `ChangeGameSpeed` | `<ChangeGameSpeed>` | Modify game simulation speed |
| `StartTutorial` | `<TutorialType>` | Begin tutorial sequence |

**Example:**
```csharp
// Show critical alert
SDK.Commands.Execute("OpenAlert", "CriticalAlert", true);

// Change to fast game speed
SDK.Commands.Execute("ChangeGameSpeed", "Fast");
```

### 🎬 Special Moments & Scenes

| Command | Parameters | Description |
|---------|------------|-------------|
| `EnterSpecialMoment` | `<SpecialMoment>` | Begin cinematic/special sequence |
| `ExitSpecialMoment` | `<Integer>` | End special sequence by ID |
| `SPLoadPrefab` | `<Any>` | Load special prefab object |
| `SPSetActiveRoot` | `<Any> <Bool>` | Activate/deactivate prefab root |

**Example:**
```csharp
// Trigger victory celebration sequence
SDK.Commands.Execute("EnterSpecialMoment", "Victory");
```

### ⚠️ System & Debug

| Command | Parameters | Description |
|---------|------------|-------------|
| `GameOver` | `<Faction> <GameOverType> <EndType>` | Trigger game end condition |
| `KillSwitchMode` | `<KillSwitchType>` | Emergency game state override |
| `SetOverride` | `<string> <float>` | Set gameplay value override |
| `DisableOverride` | `<string>` | Remove gameplay override |

**Example:**
```csharp
// Set energy production override (debug)
SDK.Commands.Execute("SetOverride", "EnergyMultiplier", 2.0f);

// Trigger successful game ending
SDK.Commands.Execute("GameOver", playerFaction, "Victory", "Terraforming");
```

---

## 🔧 Implementation Examples

### Basic Command Execution

```csharp
using PerAspera.ModSDK;

public class CommandExample : BasePlugin
{
    public override void Load()
    {
        // Get player faction reference
        var playerFaction = SDK.Universe.PlayerFaction;
        
        // Unlock solar panels
        SDK.Commands.Execute("UnlockBuilding", playerFaction, "SolarPanel");
        
        // Grant basic knowledge
        SDK.Commands.Execute("UnlockKnowledge", playerFaction, "BasicChemistry");
        
        SDK.Logger.Info("Commands executed successfully");
    }
}
```

### Command with Parameters

```csharp
public void GrantStarterPackage()
{
    var player = SDK.Universe.PlayerFaction;
    
    // Unlock essential buildings
    SDK.Commands.Execute("UnlockBuilding", player, "SolarPanel");
    SDK.Commands.Execute("UnlockBuilding", player, "Processor");
    SDK.Commands.Execute("UnlockBuilding", player, "Extractor");
    
    // Grant basic knowledge
    SDK.Commands.Execute("UnlockKnowledge", player, "BasicChemistry");
    SDK.Commands.Execute("UnlockKnowledge", player, "BasicPhysics");
    
    // Import starting resources
    SDK.Commands.Execute("ImportResource", player, "RareMetals", 500);
    SDK.Commands.Execute("ImportResource", player, "Electronics", 200);
    
    // Reveal nearby resources
    SDK.Commands.Execute("RevealResourceVein", player, "WaterIce");
    SDK.Commands.Execute("RevealResourceVein", player, "Iron");
    
    SDK.Logger.Info("Starter package granted");
}
```

### Event-Driven Command Execution

```csharp
public class EventCommandMod : BasePlugin
{
    public override void Load()
    {
        // Execute commands based on game events
        SDK.Events.OnBuildingPlaced += OnBuildingPlaced;
        SDK.Events.OnTechnologyUnlocked += OnTechnologyUnlocked;
    }
    
    private void OnBuildingPlaced(BuildingEventData data)
    {
        if (data.Building.TypeKey == "PowerCore")
        {
            // Power core built - trigger special event
            SDK.Commands.Execute("EnterSpecialMoment", "PowerAchievement");
            SDK.Commands.Execute("OpenAlert", "PowerMilestone", true);
        }
    }
    
    private void OnTechnologyUnlocked(TechnologyEventData data)
    {
        if (data.Technology == "AtmosphericProcessing")
        {
            // Atmospheric tech - reveal gas deposits
            SDK.Commands.Execute("RevealResourceVein", data.Faction, "CarbonDioxide");
            SDK.Commands.Execute("RevealResourceVein", data.Faction, "Nitrogen");
        }
    }
}
```

---

## ⚠️ Important Notes

### Parameter Types

- **`<Faction>`** - Faction object or faction ID string
- **`<Integer>`** - Whole numbers (1, 2, 3, etc.)
- **`<Number>`** - Decimal numbers (1.5, 2.0, etc.)
- **`<Bool>`** - Boolean values (true, false)
- **`<Any>`** - Any string value
- **`<TypeName>`** - Specific game type (BuildingType, ResourceType, etc.)

### Error Handling

```csharp
try 
{
    var result = SDK.Commands.Execute("UnlockBuilding", faction, "SolarPanel");
    if (!result.Success)
    {
        SDK.Logger.Error($"Command failed: {result.ErrorMessage}");
    }
}
catch (Exception ex)
{
    SDK.Logger.Error($"Command execution error: {ex.Message}");
}
```

### Command Validation

```csharp
// Check if command exists before execution
var availableCommands = SDK.Commands.GetAvailableCommands();
if (availableCommands.Contains("UnlockBuilding"))
{
    SDK.Commands.Execute("UnlockBuilding", faction, buildingType);
}
```

---

## 📚 Related Documentation

- **[SDK Overview](../sdk/Overview.md)** - General SDK concepts
- **[GameAPI.Commands](../architecture/SDK-Components.md#peraspera-gameapi-commands)** - Command system architecture
- **[Event System](../reference/Events.md)** - Game events reference
- **[Building Types](../reference/Building-Types.md)** - Available building types
- **[Resource Types](../reference/Resource-Types.md)** - Available resource types

---

**Last Updated:** December 17, 2025  
**Command Count:** 55 verified commands  
**Source:** Game runtime extraction via GetterOverrideSystem