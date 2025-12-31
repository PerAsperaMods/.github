# 🎮 Architecture GUI Per Aspera - Documentation Complète

## 📚 Introduction

Cette documentation analyse l'architecture des deux projets GUI MasterGui et MasterGui2 pour Per Aspera, basée sur l'analyse complète du code source et des patterns validés.

**Sources analysées :**
- **MasterGui v2.0.0** (PerAspera-MasterGui) - Version SDK modulaire
- **MasterGui2 v1.0.0** (MasterGui2) - Version moderne simplifiée 
- **SDK UnityGuiHelper** - Helper validé pour Unity GUI IL2CPP

---

## 🏗️ Architecture des Deux Projets

### 🔹 **MasterGui (v2.0.0) - Approche SDK Modulaire**

#### Structure Architecture
```
PerAspera-MasterGui/
├── MasterGui.cs                 # Plugin principal BasePlugin
├── MasterGuiBehaviour.cs        # MonoBehaviour Unity
├── MasterGuiLoadingHelper.cs    # Helper de chargement
├── UI/
│   ├── MasterGuiRenderer.cs     # Rendu OnGUI() principal
│   └── CategoryDrawer.cs        # Composants UI spécialisés
├── Systems/                     # Systèmes de jeu
├── Patches/                     # Patches Harmony
└── BlackBoardGui.cs            # Interface blackboard
```

#### Caractéristiques Techniques
- **Framework :** BepInEx 6 IL2CPP + ModSDK modulaire
- **GUI System :** Unity OnGUI() (ImGui style)
- **Event System :** ModSDK.EventManager avec abonnements
- **Architecture :** Modulaire avec séparation renderer/behavior
- **Patches :** Support Harmony pour modifications runtime

```csharp
// Pattern principal MasterGui
[BepInPlugin("com.wafhien.peraspera.mastergui", "Per Aspera Master GUI", "2.0.0")]
[BepInDependency("com.peraspera.modsdk", HardDependency)]
public class MasterGuiPlugin : BasePlugin
{
    public override void Load()
    {
        ModSDK.Initialize("MasterGui", "2.0.0");
        ModSDK.EventManager.Subscribe("BaseGameReady", OnBaseGameReady);
    }
}
```

### 🔹 **MasterGui2 (v1.0.0) - Approche Simplifiée**

#### Structure Architecture
```
MasterGui2/
├── MasterGui2Plugin.cs         # Plugin principal BasePlugin
├── MasterGuiInterface.cs       # Interface console
├── OverrideSystemManager.cs    # Système de surcharge de valeurs
└── UI/
    ├── MasterGuiBehaviour.cs   # MonoBehaviour Unity
    └── MasterGuiRenderer.cs    # Rendu OnGUI()
```

#### Caractéristiques Techniques
- **Framework :** BepInEx 6 IL2CPP + ModSDK standard
- **GUI System :** Unity OnGUI() + Interface console
- **Architecture :** Simplifiée avec focus sur overrides de valeurs
- **Spécialisation :** Système d'override de paramètres de jeu
- **Control System :** Interface F7 + commandes console

```csharp
// Pattern principal MasterGui2
[BepInPlugin("com.peraspera.mastergui2", "Master GUI 2", "1.0.0")]
[BepInDependency("peraaspera.modsdk", HardDependency)]
public class MasterGui2Plugin : BasePlugin
{
    public override void Load()
    {
        ModSDK.Initialize("MasterGui2", "1.0.0");
        ModSDK.EventManager.Subscribe("BaseGameReady", OnBaseGameReady);
        InitializePlugin();
    }
}
```

---

## ⚙️ Patterns d'Implémentation GUI Validés

### 1️⃣ **Pattern MonoBehaviour Unity IL2CPP** ✅

Le pattern fondamental pour toute GUI Unity dans Per Aspera :

```csharp
// Étape 1: Enregistrement IL2CPP (CRUCIAL)
using Il2CppInterop.Runtime.Injection;

public class CustomGuiBehaviour : MonoBehaviour
{
    private static bool s_spawned = false;

    public static void Spawn()
    {
        if (s_spawned) return;

        // 🔑 ÉTAPE CRUCIALE : Enregistrer le type IL2CPP
        ClassInjector.RegisterTypeInIl2Cpp<CustomGuiBehaviour>();
        
        // Créer GameObject persistant
        var guiObject = new GameObject("CustomGui_Interface");
        GameObject.DontDestroyOnLoad(guiObject);
        guiObject.AddComponent<CustomGuiBehaviour>();
        
        s_spawned = true;
    }

    // Lifecycle Unity standard
    private void Update()
    {
        // Input handling
        if (UnityEngine.Input.GetKeyDown(KeyCode.F7))
        {
            ToggleGui();
        }
    }

    // Rendu GUI
    private void OnGUI()
    {
        if (showGui)
        {
            DrawMainWindow();
        }
    }
}
```

**✅ Validé dans :** MasterGui2/UI/MasterGuiBehaviour.cs L32-47

### 2️⃣ **Pattern SDK UnityGuiHelper** ✅ 

Helper SDK officiel pour simplifier la création GUI :

```csharp
// Utilisation du SDK helper (recommandé)
using PerAspera.GameAPI.Helpers;

public static void CreateGui()
{
    // Utilise le helper SDK validé
    if (UnityGuiHelper.RegisterMonoBehaviour<MyGuiBehaviour>())
    {
        var component = UnityGuiHelper.CreatePersistentGameObject<MyGuiBehaviour>("MyGui");
        if (component != null)
        {
            Log.Info("GUI créée avec succès via SDK helper");
        }
    }
}
```

**✅ Validé dans :** SDK/PerAspera.GameAPI/Helpers/UnityGuiHelper.cs L20-45

### 3️⃣ **Pattern Event System ModSDK** ✅

Coordination avec le lifecycle du jeu :

```csharp
public class GuiPlugin : BasePlugin
{
    public override void Load()
    {
        // Initialiser ModSDK
        ModSDK.Initialize("MyGui", "1.0.0");
        
        // S'abonner aux événements de jeu
        ModSDK.EventManager.Subscribe("BaseGameReady", OnGameReady);
        ModSDK.EventManager.Subscribe("GamePaused", OnGamePaused);
    }

    private void OnGameReady(object eventData)
    {
        // Spawner la GUI quand le jeu est prêt
        MyGuiBehaviour.Spawn();
    }
    
    private void OnGamePaused(object eventData)
    {
        // Réagir aux événements de jeu
        MyGuiBehaviour.SetPaused(true);
    }
}
```

**✅ Validé dans :** MasterGui2/MasterGui2Plugin.cs L31-56

---

## 🖥️ Implémentation GUI OnGUI() Patterns

### 1️⃣ **Pattern Fenêtre Principale**

```csharp
private Rect windowRect = new Rect(10, 10, 500, 700);
private Vector2 scrollPos;
private bool showGui = true;

private void OnGUI()
{
    if (!showGui) return;

    // Style personnalisé
    GUI.skin = CreateCustomSkin();
    
    // Fenêtre principale avec scroll
    windowRect = GUI.Window(
        id: 12345,
        clientRect: windowRect,
        func: DrawMainWindow,
        text: "🎮 Per Aspera - Custom GUI"
    );
}

private void DrawMainWindow(int windowId)
{
    GUILayout.BeginVertical();
    
    scrollPos = GUILayout.BeginScrollView(scrollPos);
    
    // Contenu de la fenêtre
    DrawGameControls();
    DrawSystemStatus();
    DrawSettings();
    
    GUILayout.EndScrollView();
    GUILayout.EndVertical();
    
    // Permettre le drag de la fenêtre
    GUI.DragWindow();
}
```

**✅ Validé dans :** MasterGui/UI/MasterGuiRenderer.cs L45-78

### 2️⃣ **Pattern Contrôles Catégorisés**

```csharp
private Dictionary<string, bool> categoryExpanded = new();

private void DrawCategorizedControls()
{
    var categories = new[]
    {
        ("⚡ Energy", new[] { "Solar Efficiency", "Wind Power", "Consumption" }),
        ("🌡️ Climate", new[] { "Temperature", "Pressure", "Humidity" }),
        ("🏗️ Buildings", new[] { "Construction Speed", "Efficiency" })
    };

    foreach (var (categoryName, controls) in categories)
    {
        // Catégorie pliable
        categoryExpanded[categoryName] = DrawCollapsibleCategory(
            categoryName, 
            categoryExpanded.GetValueOrDefault(categoryName, true)
        );

        if (categoryExpanded[categoryName])
        {
            GUILayout.BeginVertical("box");
            
            foreach (var control in controls)
            {
                DrawGameValueControl(control);
            }
            
            GUILayout.EndVertical();
        }
    }
}

private bool DrawCollapsibleCategory(string name, bool expanded)
{
    GUILayout.BeginHorizontal();
    
    var style = new GUIStyle(GUI.skin.button)
    {
        alignment = TextAnchor.MiddleLeft,
        fontStyle = FontStyle.Bold
    };
    
    string arrow = expanded ? "▼" : "▶";
    bool clicked = GUILayout.Button($"{arrow} {name}", style);
    
    GUILayout.EndHorizontal();
    
    return clicked ? !expanded : expanded;
}
```

**✅ Validé dans :** MasterGui2/UI/MasterGuiRenderer.cs pattern similaire

### 3️⃣ **Pattern Input et État**

```csharp
private void Update()
{
    // Input management
    HandleKeyboardInput();
    HandleGuiState();
}

private void HandleKeyboardInput()
{
    // Toggle GUI (F7)
    if (UnityEngine.Input.GetKeyDown(KeyCode.F7))
    {
        showGui = !showGui;
        Log.Info($"GUI toggled: {(showGui ? "shown" : "hidden")}");
    }
    
    // Save settings (Ctrl+S)
    if (UnityEngine.Input.GetKey(KeyCode.LeftControl) && 
        UnityEngine.Input.GetKeyDown(KeyCode.S))
    {
        SaveSettings();
    }
    
    // Reset all (Ctrl+R)
    if (UnityEngine.Input.GetKey(KeyCode.LeftControl) && 
        UnityEngine.Input.GetKeyDown(KeyCode.R))
    {
        ResetAllValues();
    }
}

private void HandleGuiState()
{
    // Auto-hide si jeu en pause
    if (BaseGame.Instance?.isPaused == true && showGui)
    {
        showGui = false;
    }
}
```

**✅ Validé dans :** MasterGui2/UI/MasterGuiBehaviour.cs L70-95

---

## 🔧 Système Override de Valeurs (MasterGui2 Pattern)

### Architecture du Système d'Override

```csharp
// Classe de données pour override
public class GameOverride
{
    public string Key { get; set; }
    public string DisplayName { get; set; }
    public string Category { get; set; }
    public float DefaultValue { get; set; }
    public float CurrentValue { get; set; }
    public float MinValue { get; set; }
    public float MaxValue { get; set; }
    public bool IsEnabled { get; set; }
    public string Description { get; set; }
}

// Manager singleton
public class OverrideSystemManager
{
    private static OverrideSystemManager? _instance;
    public static OverrideSystemManager Instance => _instance ??= new();

    private readonly Dictionary<string, GameOverride> _overrides = new();
    
    public void RegisterOverride(string key, string displayName, float defaultValue, 
                                float minValue, float maxValue, string category = "General")
    {
        var gameOverride = new GameOverride(key, displayName, defaultValue, minValue, maxValue, category);
        _overrides[key] = gameOverride;
    }
    
    public void SetOverrideValue(string key, float value)
    {
        if (_overrides.TryGetValue(key, out var gameOverride))
        {
            gameOverride.CurrentValue = Mathf.Clamp(value, gameOverride.MinValue, gameOverride.MaxValue);
            ApplyOverride(gameOverride);
        }
    }
}
```

### Pattern d'Application des Overrides

```csharp
// Exemple d'application d'overrides via SDK
private void ApplyOverride(GameOverride gameOverride)
{
    switch (gameOverride.Key)
    {
        case "solar_efficiency":
            ModSDK.Commands.Execute($"multiply_solar_efficiency {gameOverride.CurrentValue}");
            break;
            
        case "temperature_factor":
            ModSDK.Climate.SetTemperatureMultiplier(gameOverride.CurrentValue);
            break;
            
        case "construction_speed":
            ModSDK.Buildings.SetConstructionSpeedMultiplier(gameOverride.CurrentValue);
            break;
    }
}
```

**✅ Validé dans :** MasterGui2/OverrideSystemManager.cs L47-89

---

## 💡 Interface Console Pattern (MasterGui2)

### Système d'Interface Console

```csharp
public static class MasterConsoleInterface
{
    public static void Initialize()
    {
        ModSDK.Log.Info("💡 Available commands:");
        ModSDK.Log.Info("  - 'list' - Show all available overrides");
        ModSDK.Log.Info("  - 'enable <key>' - Enable an override");
        ModSDK.Log.Info("  - 'set <key> <value>' - Set override value");
        ModSDK.Log.Info("  - 'reset <key>' - Reset override to default");
    }

    public static void ProcessCommand(string command)
    {
        var parts = command.Trim().ToLower().Split(' ');
        var cmd = parts[0];

        switch (cmd)
        {
            case "list":
                ShowAllOverrides();
                break;
                
            case "set":
                if (parts.Length > 2 && float.TryParse(parts[2], out float value))
                    SetOverrideValue(parts[1], value);
                break;
                
            case "enable":
                if (parts.Length > 1)
                    EnableOverride(parts[1]);
                break;
        }
    }
}
```

**✅ Validé dans :** MasterGui2/MasterGuiInterface.cs L34-78

---

## 🎯 Comparaison Architecturale

| Aspect | MasterGui (v2.0.0) | MasterGui2 (v1.0.0) |
|--------|-------------------|---------------------|
| **Architecture** | Modulaire complexe | Simplifiée spécialisée |
| **SDK Integration** | ModSDK complet | ModSDK + overrides |
| **GUI Complexity** | Interface complète multi-systèmes | Interface focused sur valeurs |
| **Extensibility** | Très extensible | Extensible pour overrides |
| **Performance** | Plus lourd | Plus léger |
| **Learning Curve** | Élevée | Modérée |
| **Use Cases** | GUI complètes complexes | Panneau de contrôle spécialisé |

---

## 📋 Patterns Recommandés par Use Case

### 🎮 **Pour Interface Complète de Jeu**
**Utiliser :** Pattern MasterGui modulaire
- Interface multi-systèmes
- Support patches Harmony
- Architecture extensible

### ⚙️ **Pour Panneau de Contrôle Spécialisé**
**Utiliser :** Pattern MasterGui2 simplifié
- Focus sur overrides de valeurs
- Interface console + GUI
- Rapidité de développement

### 🛠️ **Pour Helper SDK**
**Utiliser :** UnityGuiHelper SDK
- Helper officiel validé
- Simplification création MonoBehaviour
- Pattern recommandé pour nouveaux projets

---

## 🔗 Liens et Références

- **[MasterGui Source](../Individual-Mods/PerAspera-MasterGui/)** - Version modulaire complète
- **[MasterGui2 Source](../Individual-Mods/MasterGui2/)** - Version simplifiée spécialisée  
- **[SDK UnityGuiHelper](../SDK/PerAspera.GameAPI/Helpers/UnityGuiHelper.cs)** - Helper officiel
- **[Validated Patterns](../Internal_doc/ARCHITECTURE/VALIDATED-PATTERNS.md)** - Patterns IL2CPP validés
- **[Game Commands](Game-Commands.md)** - Commandes pour tests GUI
- **[BepInX Guide](../Internal_doc/bepinex6-docs/articles/dev_guide/plugin_tutorial/)** - Tutoriel plugins

---

**Dernière mise à jour :** 17 décembre 2025  
**Statut :** ✅ Documentation complète basée sur analyse de code source  
**Validation :** Patterns extraits de projets fonctionnels validés