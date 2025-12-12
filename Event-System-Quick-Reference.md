# Event System Quick Reference

A comprehensive reference guide for all **122 GameEventType** events available in Per Aspera. Use these events in your YAML mods to trigger custom logic based on game state changes.

## What are Game Events?

Game events are signals fired by the game engine when specific actions occur. By listening to these events in your `rule-patch.yaml` files, you can create mods that react dynamically to gameplay without touching C# code.

**Key Concepts:**
- **Event Types**: Categorized by domain (Building, Faction, Universe, etc.)
- **Triggers**: When the game fires these events automatically
- **Criteria**: Conditions you can check when an event fires
- **Effects**: Actions your mod executes in response

## Event Categories

### 🏗️ Building Events (31 events)

Events related to structures, districts, and construction activities.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevBuildingBuilt` | Fires when a building is completed | Track construction, unlock rewards, count specific buildings |
| `GevBuildingSpawned` | Fires when a building appears on the map | Monitor building placement, trigger zone effects |
| `GevBuildingUpgradedTo` | Fires when a building upgrades to a new type | Achievement tracking, upgrade bonuses |
| `GevBuildingDestroyedByDamage` | Fires when a building is destroyed | Penalty systems, emergency responses |
| `GevBuildingOperativeChanged` | Fires when building active/inactive state changes | Power management mods, efficiency tracking |
| `GevBuildingOutOfPower` | Fires when a building loses power | Warning systems, power grid balancing |
| `GevBuildingAttacked` | Fires when a building takes combat damage | Defense mods, alert systems |
| `GevBuildingDamagedByAsteroid` | Fires when asteroid impacts a building | Disaster response, insurance systems |
| `GevBuildingStartedScrapping` | Fires when building demolition begins | Resource recovery mods, recycling bonuses |
| `GevBuildingFinishedScrapping` | Fires when building demolition completes | Cleanup tracking, material recovery |
| `GevBuildingCanceledScrapping` | Fires when demolition is cancelled | Decision tracking, UI mods |
| `GevBuildingToggledScrapping` | Fires when scrap mode is toggled on/off | State monitoring |
| `GevBuildingUpgradeStarted` | Fires when upgrade construction begins | Progress tracking, construction mods |
| `GevBuildingUpgradeCanceled` | Fires when upgrade is cancelled | Resource refunds, planning mods |
| `GevBuildingUpgradeToggled` | Fires when upgrade mode is toggled | UI state tracking |
| `GevBuildingStartedRebuild` | Fires when damaged building repair begins | Maintenance mods, repair prioritization |
| `GevBuildingAfterChangeBuildingType` | Fires after building type changes | Conversion tracking, upgrade chains |
| `GevBuildingBeforeChangeBuildingType` | Fires before building type changes | Pre-conversion hooks, validation |
| `GevBuildingDistrictChangedActive` | Fires when district activates/deactivates | District management, zoning mods |
| `GevBuildingExtendsClusterRangeChanged` | Fires when cluster radius changes | Network expansion mods |
| `GevBuildingCitizenBorn` | Fires when a colonist is born in habitat | Population tracking, growth bonuses |
| `GevBuildingCitizenDied` | Fires when a colonist dies | Mortality tracking, replacement systems |
| `GevBuildingCitizenStarving` | Fires when colonists are starving | Emergency food systems, alerts |
| `GevBuildingSelfDespawned` | Fires when building removes itself | Cleanup tracking |
| `GevBuildingInternalAdd` | Internal engine event - advanced use only | Low-level building initialization |
| `GevBuildingInternalAddNew` | Internal engine event - advanced use only | New building creation hooks |
| `GevBuildingInternalLoad` | Internal engine event - advanced use only | Save game loading |
| `GevBuildingInternalPreRemove` | Internal engine event - advanced use only | Pre-deletion hooks |
| `GevBuildingInternalRemove` | Internal engine event - advanced use only | Building deletion tracking |
| `GevBuildingAudioRelevantPropertyChanged` | Fires when audio-related property changes | Sound mods, ambient systems |
| `GevOnClusteringChanged` | Fires when network clustering state changes | Infrastructure mods, grid optimization |

### 👥 Faction Events (40 events)

Events related to player/AI factions, research, resources, and progression.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevFactionKnowledgeUnlocked` | Fires when new knowledge is discovered | Tech tree mods, achievement tracking |
| `GevFactionKnowledgeRead` | Fires when knowledge is accessed | Tutorial systems, help mods |
| `GevFactionBuildingTypeUnlocked` | Fires when building type unlocked | Unlock tracking, progression mods |
| `GevFactionBuildingTypeRemoved` | Fires when building type removed | Content mods, technology locks |
| `GevFactionTechnologyResearchStarted` | Fires when tech research begins | Research tracking, bonuses |
| `GevFactionTechnologyResearchFinished` | Fires when tech research completes | Tech tree modifications, unlocks |
| `GevFactionQuestUnlocked` | Fires when quest becomes available | Quest mods, story branching |
| `GevFactionQuestCompleted` | Fires when quest is finished | Reward systems, progression tracking |
| `GevFactionQuestAborted` | Fires when quest is abandoned | Penalty systems, quest management |
| `GevFactionResourceVeinRevealed` | Fires when resource deposit is discovered | Exploration mods, scanner bonuses |
| `GevFactionScannerTileRevealed` | Fires when map tile is scanned | Fog of war mods, exploration tracking |
| `GevFactionScannerTileChanged` | Fires when tile scan state changes | Map reveal systems |
| `GevFactionWayTypeUnlocked` | Fires when road/pipe type unlocked | Infrastructure progression |
| `GevFactionSpecialProjectAdded` | Fires when special project starts | Megaproject tracking, milestones |
| `GevFactionSpecialProjectStageStarted` | Fires when project stage begins | Progress monitoring |
| `GevFactionSpecialProjectCompleted` | Fires when special project finishes | Victory conditions, rewards |
| `GevFactionSpecialProjectLaunchPerformed` | Fires when project launches | Launch tracking, cinematics |
| `GevFactionSpecialProjectPortAdded` | Fires when project port added | Construction phase tracking |
| `GevFactionSpecialProjectPortRemoved` | Fires when project port removed | Deconstruction tracking |
| `GevFactionSpecialSiteRevealed` | Fires when special site discovered | Exploration mods, POI systems |
| `GevFactionSpecialSiteResearched` | Fires when special site researched | Knowledge acquisition, lore mods |
| `GevFactionSpecialSiteBuildingSpawned` | Fires when special site building appears | Unique structure tracking |
| `GevFactionShipArrived` | Fires when supply ship lands | Logistics mods, resource bonuses |
| `GevFactionShipMissed` | Fires when supply ship doesn't arrive | Penalty systems, supply tracking |
| `GevFactionColonistsDeparted` | Fires when colonists leave Mars | Population management, endgame |
| `GevFactionSectorsCreated` | Fires when map sectors initialized | Map generation hooks |
| `GevFactionSectorUnlocked` | Fires when new sector unlocked | Territory expansion mods |
| `GevFactionElectricityClusteringChanged` | Fires when power grid connectivity changes | Power network mods |
| `GevFactionPipesClusteringChanged` | Fires when water/gas network changes | Resource distribution mods |
| `GevFactionMaintenanceClusteringChanged` | Fires when maintenance network changes | Drone management |
| `GevFactionCloseAllWindows` | Fires when UI closes all windows | UI state tracking |
| `GevFactionBestDronesReassigned` | Fires when priority drones reassigned | Drone optimization mods |
| `GevFactionSwarmDetected` | Fires when swarm is discovered | Combat mods, alert systems |
| `GevFactionMilitaryDroneKilled` | Fires when combat drone destroyed | Combat tracking, replacements |
| `GevFactionAIWaveStarted` | Fires when enemy AI attack begins | Defense mods, combat systems |
| `GevFactionAIWaveEnded` | Fires when enemy AI attack ends | Combat statistics, rewards |
| `GevFactionRivalInitialized` | Fires when rival faction appears | Multiplayer/rival mods |
| `GevFactionDialogueStarted` | Fires when dialogue begins | Story mods, narrative systems |
| `GevFactionDialoguePulse` | Fires during dialogue progression | Interactive dialogue mods |
| `GevFactionDialogueFinished` | Fires when dialogue ends | Dialogue tracking, branching |
| `GevFactionDefeated` | Fires when faction is defeated | Victory/defeat conditions |
| `GevFactionOrbitalBuildingChanged` | Fires when orbital structure changes | Space infrastructure mods |

### 🌍 Universe Events (10 events)

Global game state events affecting the entire game world.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevUniverseNewGameStarted` | Fires when new game begins | Initialization mods, starting bonuses |
| `GevUniverseDayPassed` | Fires every in-game day | Daily events, time-based mechanics |
| `GevUniverseGameSpeedChanged` | Fires when time speed changes | Speed-dependent systems |
| `GevUniverseGameOver` | Fires when game ends | Endgame tracking, statistics |
| `GevUniverseContinueEndedGame` | Fires when continuing after game over | Sandbox mode, post-game content |
| `GevUniverseStatsUpdated` | Fires when global statistics update | Dashboard mods, analytics |
| `GevUniverseExplosion` | Fires when explosion occurs | Visual effects, damage mods |
| `GevUniverseSwapFaction` | Fires when active faction changes | Multiplayer, faction switching |
| `GevUniverseHideVein` | Fires when resource vein is hidden | Map visibility mods |
| `GevNotificationsListChanged` | Fires when notification list updates | UI mods, alert systems |

### 🤖 Drone Events (8 events)

Events related to autonomous drone units.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevDroneSpawned` | Fires when drone is created | Drone counting, deployment mods |
| `GevDroneDespawned` | Fires when drone is removed | Cleanup tracking, resource recovery |
| `GevDroneStartWorking` | Fires when drone begins task | Task tracking, efficiency mods |
| `GevDroneStopWorking` | Fires when drone finishes task | Completion tracking, reassignment |
| `GevMaintenanceDroneStartWorking` | Fires when maintenance drone starts | Repair tracking, prioritization |
| `GevMaintenanceDroneStopWorking` | Fires when maintenance drone stops | Maintenance statistics |
| `GevDroneInternalAdd` | Internal engine event - advanced use only | Low-level drone creation |
| `GevDroneInternalLoad` | Internal engine event - advanced use only | Save game loading |
| `GevDroneInternalRemove` | Internal engine event - advanced use only | Drone deletion tracking |

### 🌡️ Planet Events (7 events)

Events tracking Mars atmospheric and environmental changes.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevPlanetTemperatureChanged` | Fires when global temperature changes | Climate tracking, milestones |
| `GevPlanetPressureChanged` | Fires when atmospheric pressure changes | Terraforming progress |
| `GevPlanetO2PressureChanged` | Fires when oxygen pressure changes | Breathability tracking |
| `GevPlanetPressureCO2LevelChanged` | Fires when CO2 levels change | Greenhouse gas monitoring |
| `GevPlanetPressureO2LevelChanged` | Fires when O2 levels change | Life support milestones |

### 🐝 Swarm Events (5 events)

Events related to hostile swarm enemies.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevSwarmSpawned` | Fires when swarm appears | Combat mods, defense alerts |
| `GevSwarmDespawned` | Fires when swarm is destroyed | Victory tracking, rewards |
| `GevSwarmInternalAdd` | Internal engine event - advanced use only | Swarm initialization |
| `GevSwarmInternalLoad` | Internal engine event - advanced use only | Save game loading |
| `GevSwarmInternalRemove` | Internal engine event - advanced use only | Swarm deletion |

### 🛣️ Infrastructure Events (10 events)

Events related to roads, pipes, and transportation networks.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevWaySpawned` | Fires when road/pipe placed | Network expansion tracking |
| `GevWayDespawned` | Fires when road/pipe removed | Network cleanup |
| `GevWayChanged` | Fires when road/pipe modified | Network optimization mods |
| `GevWayOperativeChanged` | Fires when way active state changes | Flow management |
| `GevWayUpgradeStart` | Fires when way upgrade begins | Infrastructure improvements |
| `GevWayUpgraded` | Fires when way upgrade completes | Upgrade tracking |
| `GevWayUpgradeCancel` | Fires when way upgrade cancelled | Resource management |
| `GevWayUpgradeToggleOn` | Fires when upgrade mode toggled | UI state tracking |
| `GevWayInternalAdd` | Internal engine event - advanced use only | Way creation |
| `GevWayInternalLoad` | Internal engine event - advanced use only | Save loading |
| `GevWayInternalRemove` | Internal engine event - advanced use only | Way deletion |

### ⚠️ Hazard Events (2 events)

Events related to environmental dangers.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevHazardSpawned` | Fires when hazard appears (sandstorm, etc.) | Warning systems, preparation |
| `GevHazardDespawned` | Fires when hazard disappears | Recovery tracking, cleanup |

### 🏭 Production Events (1 event)

Events related to factory production.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevFactoryProducedResource` | Fires when factory produces resource | Production tracking, bonuses |

### ⚔️ Combat Events (1 event)

Events related to military actions.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevMilitaryInflictDamage` | Fires when military unit deals damage | Combat statistics, achievements |

### 🚁 Transportation Events (4 events)

Events related to special transport units.

| Event Name | Description | Common Use Cases |
|------------|-------------|------------------|
| `GevRailedCarrierSpawned` | Fires when rail transport created | Logistics tracking |
| `GevRailedCarrierDespawned` | Fires when rail transport removed | Fleet management |
| `GevZeppelinSpawned` | Fires when zeppelin created | Air transport mods |
| `GevZeppelinDespawned` | Fires when zeppelin removed | Air fleet tracking |

## Using Events in YAML Mods

### Basic Structure

```yaml
rules:
  - eventType: !gameEventType <EventName>
    domain: !domain Player
    criteria:
      - # Your conditions here
    effects:
      - # Your actions here
```

### Practical Example: Solar Panel Counter

```yaml
rules:
  # Increment counter when solar panel built
  - eventType: !gameEventType GevBuildingBuilt
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building solar_panel
    effects:
      - !effects-set-blackboard-number
        name: "solar_panels_count"
        value: "increment"

  # Unlock achievement at 50 panels
  - eventType: !gameEventType GevBuildingBuilt
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building solar_panel
      - !operand-blackboard-number
        name: "solar_panels_count"
        comparison: greaterOrEqual
        value: 50
    effects:
      - !effects-unlock-knowledge
        knowledge: !knowledge achievement_solar_farm
```

### Advanced Example: Dynamic Tech Tree

```yaml
rules:
  # Auto-unlock advanced tech when conditions met
  - eventType: !gameEventType GevFactionTechnologyResearchFinished
    domain: !domain Player
    criteria:
      - !operand-faction-has-knowledge
        knowledge: !knowledge basic_chemistry
      - !operand-faction-has-knowledge
        knowledge: !knowledge advanced_engineering
      - !operand-blackboard-number
        name: "factories_built"
        comparison: greaterOrEqual
        value: 10
    effects:
      - !effects-unlock-knowledge
        knowledge: !knowledge experimental_synthesis
      - !effects-add-message
        text: "New experimental technology unlocked!"
```

### Event Chaining Example

```yaml
rules:
  # Stage 1: Track building destruction
  - eventType: !gameEventType GevBuildingDestroyedByDamage
    domain: !domain Player
    effects:
      - !effects-set-blackboard-number
        name: "buildings_lost"
        value: "increment"

  # Stage 2: Trigger emergency when threshold reached
  - eventType: !gameEventType GevBuildingDestroyedByDamage
    domain: !domain Player
    criteria:
      - !operand-blackboard-number
        name: "buildings_lost"
        comparison: greaterOrEqual
        value: 5
    effects:
      - !effects-set-blackboard-string
        name: "emergency_mode"
        value: "active"
      - !effects-add-message
        text: "Emergency protocols activated!"
```

## Best Practices

### Event Selection
- **Use specific events** over broad ones when possible
- **Avoid internal events** (InternalAdd, InternalLoad, InternalRemove) unless you need low-level hooks
- **Test event timing** - some events fire before state changes, others after

### Performance
- **Minimize criteria checks** - only check what's necessary
- **Use blackboard efficiently** - store intermediate results to avoid recalculation
- **Avoid GevUniverseDayPassed overuse** - fires frequently, can impact performance

### Debugging
- **Use add-message effects** to track when events fire
- **Check BepInEx logs** for event timing issues
- **Test with single events** before creating complex chains

### Compatibility
- **Don't rely on event order** unless documented
- **Test with vanilla saves** before distributing
- **Document required game version** if using newer events

## Related Resources

- **[Getting Started Guide](Getting-Started)** - Learn YAML modding basics
- **[Event Classes Reference](Event-Classes-Reference)** - Understand the C# architecture behind events
- **[Per Aspera Events Reference](Per-Aspera-Events-Reference)** - Detailed documentation of all 122 events with categories and descriptions

## Need Help?

- **Discord**: Join the Per Aspera Modding Community
- **GitHub**: [PerAsperaMods Organization](https://github.com/PerAsperaMods)
- **Steam Workshop**: Browse existing mods for examples

---

*This reference covers all 122 GameEventType events available in Per Aspera. Last updated: December 2025*
