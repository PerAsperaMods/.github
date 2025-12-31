# Documentation Complète - MarsBridge REST API

## Vue d'Ensemble

Le système MarsBridge REST API est une solution simplifiée pour créer un pont intelligent entre Satisfactory et Per Aspera. Contrairement à l'ancienne architecture complexe basée sur des fichiers, cette nouvelle approche utilise une API REST HTTP pour permettre à Satisfactory de contrôler la terraformation sur Mars via Per Aspera.

### Objectif Principal
- **Satisfactory** : Gère la production industrielle et exporte des matériaux de terraformation
- **Per Aspera** : Reçoit les commandes via HTTP et optimise le climat martien en conséquence
- **Communication** : API REST sur le port 8080 avec endpoints JSON

## Architecture

### Composants Principaux
1. **Plugin BepInEx** : `MarsBridgeRestAPIPlugin.cs` - Plugin principal intégré à Per Aspera
2. **Serveur HTTP** : HttpListener intégré pour recevoir les requêtes REST
3. **Modèles de Données** : `ApiModels.cs` - Classes pour la sérialisation JSON
4. **Intégration SDK** : Utilisation des wrappers Per Aspera pour accéder aux données du jeu

### Flux de Données
```
Satisfactory Mod → HTTP POST → MarsBridge REST API → SDK Per Aspera → Modification Climat
```

## Livrable Actuel

### ✅ Implémenté et Fonctionnel
- **Plugin BepInEx Compilé** : `MarsBridge.RestAPI.dll` déployé dans `BepInEx/plugins/`
- **Serveur HTTP** : Démarre automatiquement sur le port 8080 au lancement du jeu
- **Endpoints REST** :
  - `GET /mars/health` : Vérification de santé du service
  - `GET /mars/status` : État actuel de la planète Mars
  - `GET /mars/atmosphere/pressure` : Pression atmosphérique globale
  - `GET /mars/atmosphere/composition` : Composition atmosphérique complète
  - `POST /mars/climate` : Commandes de modification climatique
  - `POST /mars/resources` : Gestion des ressources
- **Modèles de Données** : Classes complètes pour la communication JSON
- **Logging** : Intégration avec BepInEx pour le débogage
- **Gestion d'Erreurs** : Réponses d'erreur structurées

### ✅ Testé et Validé
- **Compilation** : Projet .NET 6.0 se compile sans erreur
- **Déploiement** : Plugin se charge correctement dans Per Aspera
- **Tests API** : Script PowerShell `Test-RestAPI.ps1` valide tous les endpoints
- **Logs BepInEx** : Confirmation du démarrage du serveur HTTP

### 📁 Structure des Fichiers
```
Individual-Mods/MarsBridge.RestAPI/
├── MarsBridge.RestAPI.csproj          # Configuration projet .NET 6.0
├── MarsBridgeRestAPIPlugin.cs         # Plugin principal BepInEx
├── Models/ApiModels.cs                # Modèles de données JSON
├── Test-RestAPI.ps1                   # Script de test des endpoints
└── _OLD_SYSTEM_NOTE.md                # Documentation ancien système
```

## Ce Qui Reste à Faire

### 🔄 Priorité Haute - Intégration SDK
- **Implémentation Climat** : Remplacer les placeholders dans `/mars/climate` par des appels réels au SDK Climate
- **Accès Ressources** : Intégrer les wrappers SDK pour la lecture/écriture des ressources
- **Événements Game** : Souscrire aux événements Per Aspera pour les changements en temps réel
- **Validation Données** : Vérifier que les modifications sont appliquées correctement

### 🔄 Priorité Moyenne - Développement Satisfactory
- **Mod Satisfactory** : Créer un mod Satisfactory qui communique avec l'API REST
- **Production Monitoring** : Surveiller la production de matériaux dans Satisfactory
- **HTTP Client** : Implémenter les requêtes POST automatiques vers Per Aspera
- **Configuration** : Paramètres pour l'adresse IP/port de Per Aspera

### 🔄 Priorité Basse - Améliorations
- **Sécurité API** : Ajouter une authentification basique ou token
- **Monitoring** : Dashboard pour surveiller les échanges entre jeux
- **Logging Avancé** : Logs détaillés pour le débogage cross-game
- **Documentation Utilisateur** : Guide pour les utilisateurs finaux

### 🧹 Nettoyage Ancien Système
- **Suppression** : L'ancien système complexe (MarsBridgeIntelligence) est archivé
- **Archivage** : Documentation complète dans `_OLD_SYSTEM_NOTE.md`
- **Action Requise** : Redémarrage système pour débloquer les fichiers avant suppression complète

## Installation et Utilisation

### Prérequis
- Per Aspera installé avec BepInEx 6
- .NET 6.0 SDK pour la compilation
- Port 8080 disponible sur la machine

### Déploiement
1. Compiler le projet : `dotnet build MarsBridge.RestAPI.csproj`
2. Copier `MarsBridge.RestAPI.dll` vers `BepInEx/plugins/`
3. Lancer Per Aspera - le serveur HTTP démarre automatiquement
4. Vérifier les logs BepInEx pour confirmation

### Test de l'API
```powershell
# Exécuter le script de test
.\Test-RestAPI.ps1
```

### Utilisation depuis Satisfactory
```csharp
// Exemple d'appel depuis un mod Satisfactory
using System.Net.Http;
using Newtonsoft.Json;

var client = new HttpClient();
var command = new ClimateCommand { Action = "optimize", TargetTemperature = 20.0f };
var json = JsonConvert.SerializeObject(command);
await client.PostAsync("http://localhost:8080/mars/climate", new StringContent(json));
```

## API Endpoints Détaillés

### GET /mars/health
**Description** : Vérifie que le service est opérationnel
**Réponse** :
```json
{
  "status": "healthy",
  "timestamp": "2025-12-29T...",
  "version": "1.0.0"
}
```

### GET /mars/status
**Description** : Retourne l'état actuel de Mars
**Réponse** :
```json
{
  "planet": {
    "temperature": 15.5,
    "atmosphericPressure": 0.8,
    "resources": {
      "water": 1000,
      "oxygen": 500
    }
  },
  "timestamp": "2025-12-29T..."
}
```

### GET /mars/atmosphere/pressure
**Description** : Retourne la pression atmosphérique globale
**Réponse** :
```json
{
  "success": true,
  "message": "Atmospheric pressure retrieved successfully",
  "data": {
    "totalPressure": 0.5,
    "unit": "atm",
    "isBreathable": false,
    "timestamp": "2025-12-29T..."
  }
}
```

### GET /mars/atmosphere/composition
**Description** : Retourne la composition complète de l'atmosphère
**Réponse** :
```json
{
  "success": true,
  "message": "Atmospheric composition retrieved successfully",
  "data": {
    "gases": {
      "CO2": {
        "partialPressure": 0.4,
        "percentage": 80.0,
        "unit": "atm"
      },
      "N2": {
        "partialPressure": 0.08,
        "percentage": 16.0,
        "unit": "atm"
      }
    },
    "totalPressure": 0.5,
    "temperature": 15.5,
    "unit": "atm",
    "timestamp": "2025-12-29T..."
  }
}
```

### POST /mars/climate
**Description** : Envoie une commande de modification climatique
**Corps** :
```json
{
  "action": "optimize",
  "targetTemperature": 20.0,
  "parameters": {
    "duration": 3600
  }
}
```

### POST /mars/resources
**Description** : Gère les ressources de terraformation
**Corps** :
```json
{
  "action": "export",
  "resourceType": "water",
  "amount": 100,
  "destination": "mars"
}
```

## Modèles de Données

### ApiModels.cs
```csharp
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public string Message { get; set; }
    public T Data { get; set; }
    public DateTime Timestamp { get; set; }
}

public class ClimateCommand
{
    public string Action { get; set; }
    public float TargetTemperature { get; set; }
    public Dictionary<string, object> Parameters { get; set; }
}

public class PlanetStatus
{
    public float Temperature { get; set; }
    public float AtmosphericPressure { get; set; }
    public Dictionary<string, float> Resources { get; set; }
}

public class ResourceCommand
{
    public string Action { get; set; }
    public string ResourceType { get; set; }
    public float Amount { get; set; }
    public string Destination { get; set; }
}
```

## Tests et Validation

### Tests Automatisés
- **Script PowerShell** : `Test-RestAPI.ps1` teste tous les endpoints
- **Validation Réponses** : Vérifie le format JSON et les codes HTTP
- **Tests d'Intégration** : Simule les appels depuis Satisfactory

### Debugging
- **Logs BepInEx** : `BepInEx/LogOutput.log` pour les erreurs du plugin
- **Console HTTP** : Vérifier que le port 8080 est ouvert
- **Tests Manuels** : Utiliser Postman ou curl pour tester les endpoints

## Évolution du Projet

### Ancienne Architecture (Archivée)
- **Complexité** : 5 projets séparés, service Windows, polling de fichiers
- **Problèmes** : Maintenance difficile, performance limitée, "usine à gaz"
- **Décision** : Abandonnée en faveur de l'approche REST API

### Nouvelle Architecture (Courante)
- **Simplicité** : Un seul plugin BepInEx avec serveur HTTP intégré
- **Performance** : Communication directe via HTTP, pas de polling
- **Maintenabilité** : Code centralisé, facile à étendre
- **Évolutivité** : Prêt pour l'intégration Twitch et autres mods

## Prochaines Étapes

1. **Immédiat** : Implémenter les appels SDK réels pour le climat
2. **Court Terme** : Développer le mod Satisfactory correspondant
3. **Moyen Terme** : Ajouter la sécurité et le monitoring
4. **Long Terme** : Intégration avec d'autres jeux/mods

---

**Date de Documentation** : 29 décembre 2025
**Version du Système** : 1.0.0 (REST API)
**Statut** : Fonctionnel, prêt pour développement complémentaire
**Documentation Hybride** : Synchronisée avec `E:\SatisfactoryModding\DocSatisFactoryAspera\MarsBridge-REST-API.md`