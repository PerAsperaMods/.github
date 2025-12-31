# Enhanced Event System - Initialization Events Reference

## 🎯 **Système d'événements multi-phases pour l'initialisation du jeu**

**✅ MISE À JOUR 2025-12-20**: Architecture **dual events** validée avec succès - F9 Commands SDK pleinement fonctionnel!

Le système d'événements Per Aspera SDK utilise maintenant une **architecture dual** combinant:
- **Harmony Patches**: Détection immédiate du GameHub (compatibilité menu)
- **Native Events**: Événements natifs du moteur (accès BaseGame complet)

**🎯 DÉCOUVERTE CLÉE**: Une approche unique était insuffisante - il faut les **DEUX systèmes** pour une compatibilité complète.

Les mods peuvent choisir exactement quand ils veulent s'initialiser selon leurs besoins, avec une détection robuste couvrant tous les scénarios.

## 🏗️ **Architecture Dual - Harmony + Native Events**

**✅ PATTERN VALIDÉ**: Combinaison requise pour compatibilité complète

### **Harmony Patches - Détection Immédiate Menu** 
```csharp
// ✅ FONCTIONNE: GameHub detection via Harmony patch
[HarmonyPatch(typeof(GameHubManager), "Hub_InitializationComplete")]
[HarmonyPostfix]
public static void OnHubInitializationComplete()
{
    // Émet événements SDK immédiatement
    // Fonctionne AVANT la création de BaseGame
    EmitGameHubInitializedEvent();
}
```

### **Native Events - Gestion État Jeu**
```csharp
// ✅ FONCTIONNE: Events natifs pour accès BaseGame
NativeEventManager.Subscribe<UniverseNewGameStartedNativeEvent>(
    NativeEventConstants.UniverseNewGameStarted, OnGameStarted);
    
NativeEventManager.Subscribe<UniverseContinueEndedGameNativeEvent>(
    NativeEventConstants.UniverseContinueEnded, OnGameLoaded);
```

### **Implémentation Dual dans votre Plugin**
```csharp
public override void Load()
{
    // 1. Harmony patches pour réponse immédiate menu
    ApplyGameHubPatches();
    
    // 2. Native events pour état de jeu complet
    SetupNativeEventSubscriptions();
}
```

---

## 📋 **Phases d'initialisation disponibles**

### 1. **GameHub Ready** - ⚡ **Le plus tôt possible (Harmony)**
```csharp
using PerAspera.GameAPI.Events.Integration;

// S'abonner au premier événement possible
EnhancedEventBus.SubscribeToGameHubReady(() =>
{
    // Cette méthode s'exécute dès que GameHubManager.Awake() est appelé
    LogAspera.Info("Mod ready - Earliest possible initialization!");
    
    // ✅ Idéal pour : UI mods, Twitch integration, logging systems
    // ❌ Éviter : Accès aux données du jeu (pas encore disponibles)
});
```

### 2. **Early Mods Ready** - 🚀 **Accès BaseGame**
```csharp
// S'abonner pour avoir accès à BaseGame mais pas encore Universe/Planet
EnhancedEventBus.SubscribeToEarlyModsReady(eventData =>
{
    var baseGame = eventData.BaseGameWrapper;
    
    LogAspera.Info("Early mod initialization with BaseGame access");
    
    if (eventData.BaseGameAvailable && eventData.SafeForEarlyInit)
    {
        // ✅ Idéal pour : Core mod systems, configuration loading
        // ❌ Éviter : Planet/Universe access (pas encore créés)
        InitializeCoreModSystems();
    }
});
```

### 3. **BaseGame Created** - 🏗️ **BaseGame instance disponible**
```csharp
// S'abonner à la création de BaseGame
EnhancedEventBus.SubscribeToBaseGameCreated(eventData =>
{
    var baseGame = eventData.BaseGameWrapper;
    bool isInitialized = eventData.IsInitialized;
    
    LogAspera.Info($"BaseGame created, initialized: {isInitialized}");
    
    // ✅ Idéal pour : Game state monitoring, early hooks
    // ⚠️ Note : Universe pas encore disponible
});
```

### 4. **Universe Created** - 🌌 **Universe instance disponible**
```csharp
// S'abonner à la création de Universe
EnhancedEventBus.SubscribeToUniverseCreated(eventData =>
{
    var universe = eventData.UniverseWrapper;
    var baseGame = eventData.BaseGameWrapper;
    
    LogAspera.Info("Universe created - Faction and time systems available");
    
    // ✅ Idéal pour : Faction mods, time-based systems, research mods
    // ⚠️ Note : Planet pas encore disponible
    var factions = universe.GetFactions();
    LogAspera.Info($"Universe has {factions.Count} factions");
});
```

### 5. **Planet Created** - 🪐 **Planet instance disponible**
```csharp
// S'abonner à la création de Planet
EnhancedEventBus.SubscribeToPlanetCreated(eventData =>
{
    var planet = eventData.PlanetWrapper;
    var universe = eventData.UniverseWrapper;
    
    LogAspera.Info("Planet created - Building and climate systems available");
    
    // ✅ Idéal pour : Building mods, climate mods, resource tracking
    // ⚠️ Note : Pas encore tous les buildings/ressources chargés
    var climate = planet.GetClimate();
    LogAspera.Info($"Planet climate: {climate.GetTemperature()}°C");
});
```

### 6. **Game Fully Loaded** - ✅ **Tout disponible**
```csharp
// S'abonner quand tout est complètement chargé
EnhancedEventBus.SubscribeToGameFullyLoaded(eventData =>
{
    var baseGame = eventData.BaseGameWrapper;
    var universe = eventData.UniverseWrapper;
    var planet = eventData.PlanetWrapper;
    
    LogAspera.Info("Game fully loaded - All systems available!");
    
    // ✅ Idéal pour : Complex mods, full game analysis, data processing
    // ✅ Tout est disponible : Buildings, Resources, Tech tree, etc.
    
    var buildings = planet.GetBuildings();
    var resources = planet.GetResources();
    
    LogAspera.Info($"Planet has {buildings.Count} buildings and {resources.Count} resource types");
});
```

## 🎯 **Guide de choix d'événement**

### **Mods UI/Interface** → `GameHubReady`
```csharp
// Twitch integration, custom UI, input handlers
EnhancedEventBus.SubscribeToGameHubReady(() =>
{
    InitializeTwitchClient();
    SetupCustomUI();
    RegisterInputHandlers();
});
```

### **Mods Core Systems** → `EarlyModsReady`
```csharp
// Configuration, logging, core utilities
EnhancedEventBus.SubscribeToEarlyModsReady(eventData =>
{
    LoadModConfiguration();
    InitializeLoggingSystem();
    SetupCoreUtilities();
});
```

### **Mods Faction/Research** → `UniverseCreated`
```csharp
// Tech tree modifications, faction systems
EnhancedEventBus.SubscribeToUniverseCreated(eventData =>
{
    ModifyTechTree(eventData.UniverseWrapper);
    SetupFactionBonuses();
});
```

### **Mods Building/Climate** → `PlanetCreated`
```csharp
// Building modifications, climate systems
EnhancedEventBus.SubscribeToPlanetCreated(eventData =>
{
    RegisterCustomBuildings();
    InitializeClimateMonitoring(eventData.PlanetWrapper);
});
```

### **Mods Complex/Analytics** → `GameFullyLoaded`
```csharp
// Full game analysis, complex integrations
EnhancedEventBus.SubscribeToGameFullyLoaded(eventData =>
{
    AnalyzeFullGameState(eventData);
    StartComplexProcessing();
    EnableAllFeatures();
});
```

## ⚡ **Bonnes pratiques**

### **Initialisation en cascade**
```csharp
public class MyMod : BasePlugin
{
    private bool _coreInitialized = false;
    private bool _gameInitialized = false;

    public override void Load()
    {
        // Phase 1: Core du mod (le plus tôt possible)
        EnhancedEventBus.SubscribeToGameHubReady(() =>
        {
            InitializeCore();
            _coreInitialized = true;
        });

        // Phase 2: Intégration jeu (quand le jeu est prêt)
        EnhancedEventBus.SubscribeToGameFullyLoaded(eventData =>
        {
            if (_coreInitialized)
            {
                InitializeGameIntegration(eventData);
                _gameInitialized = true;
            }
        });
    }

    private void InitializeCore()
    {
        // Setup indépendant du jeu
        LoadConfiguration();
        SetupLogging();
    }

    private void InitializeGameIntegration(GameFullyLoadedEvent eventData)
    {
        // Integration avec les systèmes du jeu
        ConnectToGameSystems(eventData);
        StartGameMonitoring();
    }
}
```

### **Gestion d'erreurs robuste**
```csharp
EnhancedEventBus.SubscribeToGameFullyLoaded(eventData =>
{
    try
    {
        // Validation avant utilisation
        if (eventData.BaseGameWrapper?.IsValidWrapper != true)
        {
            LogAspera.Error("BaseGame not valid in GameFullyLoaded event");
            return;
        }

        // Utilisation sécurisée
        var planet = eventData.PlanetWrapper;
        if (planet?.IsValidWrapper == true)
        {
            ProcessPlanetData(planet);
        }
    }
    catch (Exception ex)
    {
        LogAspera.Error($"Failed to initialize on game loaded: {ex.Message}");
        // Graceful degradation
        EnableLimitedMode();
    }
});
```

Le système d'événements Per Aspera SDK permet maintenant un contrôle précis et type-safe de l'initialisation des mods à tous les stades du chargement du jeu !