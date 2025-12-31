# 🔧 Système Consolidé d'Exécution des Commandes - Documentation Complète

## 📋 Vue d'Ensemble

Le **Système Consolidé d'Exécution des Commandes** est une architecture unifiée pour l'exécution des commandes dans Per Aspera, combinant les commandes console traditionnelles avec les nouvelles API natives standard. Ce système fournit une interface cohérente pour tous les types d'opérations de modding.

### 🎯 Objectifs

- **Consolidation** : Unifier tous les appels de commandes dans une seule API
- **Performance** : Prioriser les API natives sur les commandes console
- **Fiabilité** : Fallback automatique en cas d'échec des API natives
- **Maintenabilité** : Code modulaire et extensible
- **Documentation** : Interface bien documentée pour les développeurs

### 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    ConsoleCommandExecutor                   │
│                    (Classe principale)                      │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────┐    │
│  │            ExecuteConsoleCommand()                 │    │
│  │            (Exécution console de base)             │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │         Commandes Standard Natives                 │    │
│  │  ├─ ExecuteFactionAddResource()                    │    │
│  │  ├─ ExecuteAddResearchPoints()                     │    │
│  │  ├─ ExecuteUnlockBuilding()                        │    │
│  │  ├─ ExecuteFactionAddResources() (batch)           │    │
│  │  └─ ExecuteCommand() (générique)                   │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │            Mode Dual (Console + Natif)             │    │
│  │  - useNative: true → API native (recommandé)       │    │
│  │  - useNative: false → Commande console (fallback)  │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                 PerAsperaExtensions                         │
│                 (Fonctions utilitaires)                     │
├─────────────────────────────────────────────────────────────┤
│  ├─ GetCurrentFaction()                                    │
│  ├─ ParseResourceType()                                    │
│  ├─ AddResourcesToFaction()                                │
│  ├─ GetConsole()                                           │
│  └─ ExecuteConsoleCommand()                                │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                 Harmony Patches                             │
│                 (InteractionManagerPatches)                 │
├─────────────────────────────────────────────────────────────┤
│  ├─ FactionAddResourceDistributedPatch                     │
│  └─ Autres patches utilisant ConsoleCommandExecutor        │
└─────────────────────────────────────────────────────────────┘
```

## 📚 Classes Principales

### ConsoleCommandExecutor

Classe statique principale contenant toutes les méthodes d'exécution de commandes.

#### Méthodes Publiques

##### ExecuteConsoleCommand(string command)
```csharp
/// <summary>
/// Exécute une commande console brute
/// </summary>
/// <param name="command">Commande complète (ex: "factionaddresourcedistributed Parts 500")</param>
/// <returns>true si succès, false sinon</returns>
public static bool ExecuteConsoleCommand(string command)
```

**Exemple :**
```csharp
bool success = ConsoleCommandExecutor.ExecuteConsoleCommand("factionaddresourcedistributed Parts 500");
```

##### ExecuteFactionAddResource(string resourceName, float amount, bool useNative = true)
```csharp
/// <summary>
/// Ajoute des ressources à la faction actuelle
/// </summary>
/// <param name="resourceName">Nom de la ressource (Parts, Water, Energy, etc.)</param>
/// <param name="amount">Quantité à ajouter</param>
/// <param name="useNative">true = API native (recommandé), false = console</param>
/// <returns>true si succès, false sinon</returns>
public static bool ExecuteFactionAddResource(string resourceName, float amount, bool useNative = true)
```

**Exemples :**
```csharp
// API native (recommandé)
ConsoleCommandExecutor.ExecuteFactionAddResource("Parts", 500, useNative: true);

// Console (fallback)
ConsoleCommandExecutor.ExecuteFactionAddResource("Water", 1000, useNative: false);
```

##### ExecuteFactionAddResources(Dictionary<string, float> resources, bool useNative = true)
```csharp
/// <summary>
/// Ajoute plusieurs ressources en batch
/// </summary>
/// <param name="resources">Dictionnaire nom->quantité</param>
/// <param name="useNative">Mode d'exécution</param>
/// <returns>true si toutes les ressources ont été ajoutées</returns>
public static bool ExecuteFactionAddResources(Dictionary<string, float> resources, bool useNative = true)
```

**Exemple :**
```csharp
var resources = new Dictionary<string, float>
{
    { "Parts", 500 },
    { "Water", 1000 },
    { "Energy", 750 }
};

bool success = ConsoleCommandExecutor.ExecuteFactionAddResources(resources, useNative: true);
```

##### ExecuteAddResearchPoints(float amount, bool useNative = true)
```csharp
/// <summary>
/// Ajoute des points de recherche à la faction actuelle
/// </summary>
/// <param name="amount">Nombre de points à ajouter</param>
/// <param name="useNative">Mode d'exécution</param>
/// <returns>true si succès, false sinon</returns>
public static bool ExecuteAddResearchPoints(float amount, bool useNative = true)
```

##### ExecuteUnlockBuilding(string buildingName, bool useNative = true)
```csharp
/// <summary>
/// Déverrouille un bâtiment pour la faction actuelle
/// </summary>
/// <param name="buildingName">Nom du bâtiment à déverrouiller</param>
/// <param name="useNative">Mode d'exécution</param>
/// <returns>true si succès, false sinon</returns>
public static bool ExecuteUnlockBuilding(string buildingName, bool useNative = true)
```

##### ExecuteCommand(string commandName, params object[] parameters)
```csharp
/// <summary>
/// Exécute une commande générique avec paramètres
/// </summary>
/// <param name="commandName">Nom de la commande</param>
/// <param name="parameters">Paramètres de la commande</param>
/// <returns>true si succès, false sinon</returns>
public static bool ExecuteCommand(string commandName, params object[] parameters)
```

**Exemple :**
```csharp
// Équivalent à "customcommand param1 param2 param3"
ConsoleCommandExecutor.ExecuteCommand("customcommand", "param1", "param2", "param3");
```

### PerAsperaExtensions

Classe utilitaire fournissant les fonctions de bas niveau utilisées par ConsoleCommandExecutor.

#### Méthodes Utiles pour les Mods

##### GetCurrentFaction()
```csharp
/// <summary>
/// Récupère la faction du joueur actuel
/// </summary>
/// <returns>Objet faction ou null</returns>
public static object? GetCurrentFaction()
```

##### ParseResourceType(string resourceName)
```csharp
/// <summary>
/// Convertit un nom de ressource en enum ResourceType
/// </summary>
/// <param name="resourceName">Nom de la ressource (Parts, Water, etc.)</param>
/// <returns>Enum ResourceType ou null si invalide</returns>
public static object? ParseResourceType(string resourceName)
```

##### AddResourcesToFaction(object faction, string resourceType, float amount)
```csharp
/// <summary>
/// Ajoute des ressources au stockpile d'une faction
/// </summary>
/// <param name="faction">Objet faction</param>
/// <param name="resourceType">Type de ressource</param>
/// <param name="amount">Quantité</param>
/// <returns>true si succès</returns>
public static bool AddResourcesToFaction(object faction, string resourceType, float amount)
```

## 🎮 Guide d'Utilisation

### Configuration Initiale

1. **Référencer le SDK** dans votre projet `.csproj` :
```xml
<Import Project="$(MSBuildThisFileDirectory)..\..\SDK\SDK-DLL\sdkDLL.props" />
```

2. **Importer les namespaces** :
```csharp
using PerAspera.Core.IL2CPP;
using BepInEx.Logging;
```

### Exemples d'Utilisation dans un Mod

#### Mod Simple d'Ajout de Ressources

```csharp
using BepInEx;
using PerAspera.Core.IL2CPP;

namespace MyResourceMod
{
    [BepInPlugin("com.example.resourcemod", "Resource Mod", "1.0.0")]
    public class ResourceMod : BasePlugin
    {
        private ManualLogSource _logger;

        public override void Load()
        {
            _logger = Logger;

            // Ajouter des ressources au démarrage
            AddStartingResources();
        }

        private void AddStartingResources()
        {
            var resources = new Dictionary<string, float>
            {
                { "Parts", 1000 },
                { "Water", 5000 },
                { "Energy", 2500 },
                { "Oxygen", 1000 }
            };

            bool success = ConsoleCommandExecutor.ExecuteFactionAddResources(resources, useNative: true);

            if (success)
            {
                _logger.LogInfo("✅ Ressources de départ ajoutées avec succès");
            }
            else
            {
                _logger.LogError("❌ Échec de l'ajout des ressources de départ");
            }
        }
    }
}
```

#### Mod avec Commandes Console Personnalisées

```csharp
using BepInEx;
using PerAspera.Core.IL2CPP;
using UnityEngine;

namespace MyCommandMod
{
    [BepInPlugin("com.example.commandmod", "Command Mod", "1.0.0")]
    public class CommandMod : BasePlugin
    {
        private ManualLogSource _logger;

        public override void Load()
        {
            _logger = Logger;

            // Enregistrer des raccourcis clavier
            // F1 : Ajouter des ressources
            // F2 : Points de recherche
            // F3 : Déverrouiller bâtiment
        }

        void Update()
        {
            if (Input.GetKeyDown(KeyCode.F1))
            {
                ExecuteResourceCommand();
            }
            else if (Input.GetKeyDown(KeyCode.F2))
            {
                ExecuteResearchCommand();
            }
            else if (Input.GetKeyDown(KeyCode.F3))
            {
                ExecuteBuildingCommand();
            }
        }

        private void ExecuteResourceCommand()
        {
            bool success = ConsoleCommandExecutor.ExecuteFactionAddResource("Parts", 100, useNative: true);
            _logger.LogInfo(success ? "✅ Parts ajoutés" : "❌ Échec ajout Parts");
        }

        private void ExecuteResearchCommand()
        {
            bool success = ConsoleCommandExecutor.ExecuteAddResearchPoints(500, useNative: true);
            _logger.LogInfo(success ? "✅ Points de recherche ajoutés" : "❌ Échec ajout recherche");
        }

        private void ExecuteBuildingCommand()
        {
            bool success = ConsoleCommandExecutor.ExecuteUnlockBuilding("SolarPanel", useNative: true);
            _logger.LogInfo(success ? "✅ Bâtiment déverrouillé" : "❌ Échec déverrouillage");
        }
    }
}
```

## 🔄 Migration depuis l'Ancien Système

### Avant (Code Fragmenté)

```csharp
// Ancienne approche - code éparpillé
var console = GetConsole();
console?.InvokeMethod("ExecuteCommandString", "factionaddresourcedistributed Parts 500");

// Ou directement avec extensions
var faction = PerAsperaExtensions.GetCurrentFaction();
PerAsperaExtensions.AddResourcesToFaction(faction, "Parts", 500);
```

### Après (Système Consolidé)

```csharp
// Nouvelle approche - API unifiée
ConsoleCommandExecutor.ExecuteFactionAddResource("Parts", 500, useNative: true);
```

### Avantages de la Migration

- ✅ **Cohérence** : Une seule API pour toutes les commandes
- ✅ **Performance** : API natives prioritaires
- ✅ **Fiabilité** : Fallback automatique
- ✅ **Maintenabilité** : Code centralisé
- ✅ **Documentation** : Interface bien documentée

## 📋 Ressources Disponibles

### Noms de Ressources Valides

| Nom Console | Nom API | Description |
|-------------|---------|-------------|
| `Parts` | `CHG` | Composants mécaniques |
| `Water` | `WATER` | Eau |
| `Energy` | `ENERGY` | Énergie |
| `Oxygen` | `OXYGEN` | Oxygène |
| `Nitrogen` | `NITROGEN` | Azote |
| `Iron` | `IRON` | Fer |

### Commandes Console Disponibles

| Commande | Description | Statut |
|----------|-------------|---------|
| `factionaddresourcedistributed <resource> <amount>` | Ajoute des ressources | ✅ Implémenté |
| `addresearchpoints <amount>` | Ajoute des points de recherche | 🔄 Prêt pour test |
| `unlockbuilding <building>` | Déverrouille un bâtiment | 🔄 Prêt pour test |
| `pausegame` | Met le jeu en pause | 📋 À implémenter |
| `savegame <name>` | Sauvegarde la partie | 📋 À implémenter |

### Codes de Bâtiments

| Nom Bâtiment | Description |
|--------------|-------------|
| `SolarPanel` | Panneau solaire |
| `WaterExtractor` | Extracteur d'eau |
| `OxygenGenerator` | Générateur d'oxygène |
| `Habitat` | Habitat |

## 🛠️ Dépannage

### Erreurs Courantes

#### "Console wrapper not available"
```
Cause: Le système console n'est pas initialisé
Solution: Vérifier que le jeu est complètement chargé avant d'exécuter des commandes
```

#### "Cannot get current faction"
```
Cause: Aucune faction joueur trouvée
Solution: S'assurer que le joueur a une faction active
```

#### "Resource type not found"
```
Cause: Nom de ressource invalide
Solution: Utiliser les noms de ressources valides listés ci-dessus
```

#### API native échoue, fallback console
```
Cause: API native non disponible (version ancienne)
Solution: Le système bascule automatiquement en mode console
```

### Logs et Debugging

Le système produit des logs détaillés :

```
[Info   :ConsoleCommandExecutor] ✅ Successfully added 500 Parts to faction (native API)
[Warning:ConsoleCommandExecutor] ⚠️ AddResearchPoints method not found, falling back to console
[Error  :ConsoleCommandExecutor] ❌ Native resource addition failed: Object reference not set to an instance of an object
```

### Test des Fonctionnalités

Pour tester le système :

1. **Démarrer Per Aspera** avec le mod chargé
2. **Ouvrir la console** (F1 ou commande appropriée)
3. **Tester les commandes** :
   - `import parts 500` (devrait ajouter des Parts)
   - `import water 1000` (devrait ajouter de l'eau)
4. **Vérifier les logs** BepInEx pour confirmer l'exécution

## 🎯 Bonnes Pratiques

### Performance

- **Préférer les API natives** (`useNative: true`) pour de meilleures performances
- **Utiliser le batch** pour ajouter plusieurs ressources à la fois
- **Éviter les appels fréquents** dans Update() - utiliser des timers ou événements

### Fiabilité

- **Toujours vérifier le retour** des méthodes (bool)
- **Logger les succès/échecs** pour le debugging
- **Utiliser des noms de ressources valides** (voir la liste ci-dessus)

### Architecture

- **Centraliser la logique** dans des méthodes dédiées
- **Éviter la duplication** de code d'exécution de commandes
- **Documenter les appels** importants avec des commentaires

### Sécurité

- **Valider les paramètres** avant l'exécution
- **Limiter les quantités** pour éviter les abus
- **Vérifier les permissions** si nécessaire

## 🚀 Évolution Future

### Commandes Planifiées

- **Contrôle du jeu** : Pause/Resume/Speed control
- **Gestion des unités** : Spawn/Move/Destroy/Control
- **Événements** : Déclenchement manuel d'événements
- **Relations** : Gestion des alliances/guerres
- **Climat** : Contrôle température/pression/atmosphère
- **Économie** : Taxes/Commerce/Trade routes

### Améliorations

- **Validation étendue** : Vérification des paramètres avant exécution
- **Callbacks** : Notifications d'événements de commande
- **Historique** : Logging des commandes exécutées
- **UI Integration** : Interface graphique pour les commandes
- **Configuration** : Paramétrage des comportements par défaut

---

## 📞 Support

Pour des questions ou problèmes :

1. **Vérifier les logs** BepInEx pour les messages d'erreur
2. **Tester en mode console** si l'API native échoue
3. **Consulter cette documentation** pour les noms valides
4. **Signaler les bugs** avec les logs complets

Le système est conçu pour être **robuste et évolutif**, servant de base solide pour tous vos mods Per Aspera ! 🎮