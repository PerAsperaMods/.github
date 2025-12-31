# 🗺️ Guide d'Utilisation - Système Keeper/KeeperMap/Handle

## 🎯 Vue d'Ensemble

Le système **Keeper** de Per Aspera est le registre central de toutes les entités du jeu. Il utilise un système **Handle** pour identifier de manière unique chaque objet et une **KeeperMap** pour les retrouver efficacement.

---

## 🏗️ Architecture du Système

### Composants Principaux

```csharp
// 1. Handle - Identifiant unique d'entité
public struct Handle : IEquatable<Handle>
{
    public readonly int index;    // Position dans le registre
    public readonly int version;  // Version pour éviter les références obsolètes
}

// 2. KeeperMap - Dictionnaire Handle → Object  
public class KeeperMap
{
    private readonly Dictionary<Handle, IHandleable> _objects;
    
    public IHandleable Find(Handle handle)                    // Recherche de base
    public T Find<T>(Handle handle) where T : IHandleable    // Recherche typée
    public bool Contains(Handle handle)                       // Vérification existence
}

// 3. Keeper - Gestionnaire principal
public class Keeper : IDisposable
{
    public KeeperMap map;                           // La map principale
    public Handle Register(IHandleable handleable) // Enregistrement
    public void Unregister(IHandleable handleable) // Désenregistrement
}
```

---

## 🛠️ Patterns d'Utilisation

### Pattern 1: Accès Direct via Handle Connu (Recommandé - Avec Wrappers)

```csharp
public static T? FindByHandle<T>(Handle handle) where T : class, IHandleable
{
    try
    {
        // 1. Obtenir KeeperMapWrapper directement
        var keeperMapWrapper = KeeperMapWrapper.GetCurrent();
        if (keeperMapWrapper == null) 
        {
            LogAspera.Warning("KeeperMapWrapper not available");
            return null;
        }
        
        // 2. Utiliser la méthode wrapper type-safe
        return keeperMapWrapper.Find<T>(handle);
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"FindByHandle failed: {ex.Message}");
        return null;
    }
}
```

### Pattern 2: Accès via Wrapper → Handle → KeeperMapWrapper

```csharp
public static object? GetNativeFactionFromWrapper(GameAPI.Wrappers.Faction factionWrapper)
{
    try
    {
        // 1. Obtenir le Handle depuis le wrapper
        var handle = factionWrapper.GetHandle();
        if (handle.Equals(Handle.Null)) 
        {
            LogAspera.Warning("Faction wrapper has invalid handle");
            return null;
        }
        
        // 2. Utiliser KeeperMapWrapper pour récupérer l'objet natif
        var keeperMapWrapper = KeeperMapWrapper.GetCurrent();
        if (keeperMapWrapper == null)
        {
            LogAspera.Warning("KeeperMapWrapper not available");
            return null;
        }
        
        // 3. Utiliser la méthode wrapper type-safe
        return keeperMapWrapper.Find<object>(handle);
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"GetNativeFactionFromWrapper failed: {ex.Message}");
        return null;
    }
}
```

### Pattern 3: Vérification d'Existence avec Wrappers

```csharp
public static bool EntityExists(Handle handle)
{
    try
    {
        var keeperMapWrapper = KeeperMapWrapper.GetCurrent();
        if (keeperMapWrapper == null) return false;
        
        // Utilisation de la méthode wrapper type-safe
        return keeperMapWrapper.Contains(handle);
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"EntityExists check failed: {ex.Message}");
        return false;
    }
}
```

### Pattern 4: Énumération avec Diagnostics Wrapper

```csharp
public static void PrintKeeperDiagnostics()
{
    try
    {
        var keeperMapWrapper = KeeperMapWrapper.GetCurrent();
        if (keeperMapWrapper == null)
        {
            LogAspera.Warning("KeeperMapWrapper not available for diagnostics");
            return;
        }
        
        // Utiliser les méthodes diagnostics du wrapper
        var diagnostics = keeperMapWrapper.GetDiagnostics();
        LogAspera.Info($"Keeper Diagnostics: {diagnostics.EntityCount} entities");
        LogAspera.Info($"Memory Usage: {diagnostics.MemoryUsageKB} KB");
        
        // Énumération type-safe via wrapper
        var entities = keeperMapWrapper.EnumerateEntities();
        var typeCounts = new Dictionary<string, int>();
        
        foreach (var entity in entities)
        {
            if (entity == null) continue;
            var typeName = entity.GetType().Name;
            typeCounts[typeName] = typeCounts.GetValueOrDefault(typeName, 0) + 1;
        }
        
        foreach (var kvp in typeCounts.OrderByDescending(x => x.Value))
        {
            LogAspera.Info($"  {kvp.Key}: {kvp.Value}");
        }
    }
    catch (Exception ex)
    {
        LogAspera.Error($"PrintKeeperDiagnostics failed: {ex.Message}");
    }
}
```

---

## 🎮 Cas d'Usage Concrets

### Cas 1: Accéder aux ResourceTypes via Faction

```csharp
public static Dictionary<string, ResourceInfo> GetKnownResources()
{
    try
    {
        // 1. Obtenir Faction via Universe (wrapper)
        var universe = BaseGame.GetCurrent()?.GetUniverse();
        var playerFaction = universe?.GetPlayerFaction();
        if (playerFaction == null) return new Dictionary<string, ResourceInfo>();
        
        // 2. Obtenir l'objet natif Faction via Handle
        var nativeFaction = GetNativeFactionFromWrapper(playerFaction);
        if (nativeFaction == null) return new Dictionary<string, ResourceInfo>();
        
        // 3. Accéder aux knownResourceTypes (ArraySet<ResourceType>)
        var knownResourceTypes = nativeFaction.GetFieldValue<object>("knownResourceTypes");
        if (knownResourceTypes == null) return new Dictionary<string, ResourceInfo>();
        
        // 4. Énumérer les ResourceTypes
        var resourceDict = new Dictionary<string, ResourceInfo>();
        foreach (var resourceType in EnumerateArraySet(knownResourceTypes))
        {
            if (resourceType == null) continue;
            
            var resourceInfo = new ResourceInfo
            {
                Name = resourceType.GetFieldValue<string>("name") ?? "Unknown",
                DisplayName = resourceType.GetFieldValue<string>("displayName") ?? "Unknown",
                Category = resourceType.GetFieldValue<object>("category")?.GetFieldValue<string>("name") ?? "Unknown",
                Description = resourceType.GetFieldValue<string>("description") ?? "No description",
                IsKnown = true
            };
            
            resourceDict[resourceInfo.Name] = resourceInfo;
        }
        
        return resourceDict;
    }
    catch (Exception ex)
    {
        LogAspera.Error($"GetKnownResources failed: {ex.Message}");
        return new Dictionary<string, ResourceInfo>();
    }
}
```

### Cas 2: Rechercher Tous les Bâtiments

```csharp
public static List<Building> GetAllBuildings()
{
    try
    {
        var buildings = FindAllByType<Building>();
        return buildings.ToList();
    }
    catch (Exception ex)
    {
        LogAspera.Error($"GetAllBuildings failed: {ex.Message}");
        return new List<Building>();
    }
}
```

### Cas 3: Vérifier l'Existence d'une Entité

```csharp
public static bool EntityExists(Handle handle)
{
    try
    {
        var keeper = GetKeeperInstance();
        if (keeper == null) return false;
        
        var map = keeper.GetFieldValue<object>("map");
        if (map == null) return false;
        
        return map.InvokeMethod<bool>("Contains", handle);
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"EntityExists check failed: {ex.Message}");
        return false;
    }
}
```

---

## 🔧 Helper Methods

### Obtenir KeeperMapWrapper (Recommandé)

```csharp
private static KeeperMapWrapper? GetKeeperMapWrapper()
{
    try
    {
        return KeeperMapWrapper.GetCurrent();
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"GetKeeperMapWrapper failed: {ex.Message}");
        return null;
    }
}
```

### Méthodes Helper avec Wrappers

```csharp
// Recherche type-safe avec validation automatique
public static T? FindEntity<T>(Handle handle) where T : class, IHandleable
{
    var wrapper = GetKeeperMapWrapper();
    return wrapper?.Find<T>(handle);
}

// Vérification d'existence optimisée
public static bool CheckEntityExists(Handle handle)
{
    var wrapper = GetKeeperMapWrapper();
    return wrapper?.Contains(handle) ?? false;
}

// Énumération filtrée par type
public static IEnumerable<T> GetEntitiesByType<T>() where T : class, IHandleable
{
    var wrapper = GetKeeperMapWrapper();
    if (wrapper == null) return Enumerable.Empty<T>();
    
    return wrapper.EnumerateEntitiesByType(entity => entity is T).Cast<T>();
}
```

### Énumérer un ArraySet

```csharp
private static IEnumerable<object> EnumerateArraySet(object arraySet)
{
    try
    {
        if (arraySet == null) yield break;
        
        // ArraySet expose généralement une méthode GetEnumerator ou _items
        var enumerator = arraySet.InvokeMethod<object>("GetEnumerator");
        if (enumerator == null)
        {
            // Fallback: essayer d'accéder à _items directement
            var items = arraySet.GetFieldValue<object>("_items");
            if (items != null)
            {
                foreach (var item in EnumerateCollection(items))
                    yield return item;
            }
            yield break;
        }
        
        // Utiliser l'enumerator
        while (enumerator.InvokeMethod<bool>("MoveNext"))
        {
            var current = enumerator.GetPropertyValue<object>("Current");
            if (current != null) yield return current;
        }
    }
    catch (Exception ex)
    {
        LogAspera.Warning($"EnumerateArraySet failed: {ex.Message}");
    }
}
```

---

## ⚡ Optimisations Performance

### Cache des Handles Fréquents

```csharp
private static readonly ConcurrentDictionary<string, Handle> _handleCache = new();

public static void CacheHandle(string key, Handle handle)
{
    _handleCache[key] = handle;
}

public static Handle? GetCachedHandle(string key)
{
    return _handleCache.TryGetValue(key, out var handle) ? handle : null;
}
```

### Validation Batch des Handles

```csharp
public static Dictionary<Handle, bool> ValidateHandles(IEnumerable<Handle> handles)
{
    var results = new Dictionary<Handle, bool>();
    
    try
    {
        var keeper = GetKeeperInstance();
        if (keeper == null) return results;
        
        var map = keeper.GetFieldValue<object>("map");
        if (map == null) return results;
        
        foreach (var handle in handles)
        {
            results[handle] = map.InvokeMethod<bool>("Contains", handle);
        }
    }
    catch (Exception ex)
    {
        LogAspera.Error($"ValidateHandles failed: {ex.Message}");
    }
    
    return results;
}
```

---

## 🚨 Bonnes Pratiques

### ✅ À Faire

1. **Toujours vérifier les nulls** - Keeper peut être indisponible
2. **Utiliser try/catch** - Les accès réflexion peuvent échouer
3. **Cache les Handles fréquents** - Éviter les lookups répétés
4. **Vérifier Contains() avant Find()** - Optimisation performance
5. **Loguer les erreurs appropriées** - Debugging facilité

### ❌ À Éviter

1. **Ne pas stocker des références directes aux objets natifs** - Peuvent devenir invalides
2. **Éviter les énumérations complètes fréquentes** - Coût performance élevé
3. **Ne pas assume que les Handles restent valides après restart** - Système peut se réinitialiser
4. **Éviter l'accès concurrent sans protection** - Thread safety requise

---

## 🔍 Debugging

### Diagnostics Keeper

```csharp
public static void PrintKeeperDiagnostics()
{
    try
    {
        var keeper = GetKeeperInstance();
        if (keeper == null) return;
        
        var map = keeper.GetFieldValue<object>("map");
        if (map == null) return;
        
        var objectsDict = map.GetFieldValue<object>("_objects");
        if (objectsDict == null) return;
        
        var count = objectsDict.GetPropertyValue<int>("Count");
        LogAspera.Info($"Keeper contains {count} registered entities");
        
        // Compter par type
        var typeCounts = new Dictionary<string, int>();
        var values = objectsDict.InvokeMethod<object>("get_Values");
        
        foreach (var item in values)
        {
            if (item == null) continue;
            
            var typeName = item.GetType().Name;
            typeCounts[typeName] = typeCounts.GetValueOrDefault(typeName, 0) + 1;
        }
        
        foreach (var kvp in typeCounts.OrderByDescending(x => x.Value))
        {
            LogAspera.Info($"  {kvp.Key}: {kvp.Value}");
        }
    }
    catch (Exception ex)
    {
        LogAspera.Error($"PrintKeeperDiagnostics failed: {ex.Message}");
    }
}
```

---

## 🎯 Résumé

Le système **Keeper/KeeperMap/Handle** est le cœur de l'accès aux entités Per Aspera:

1. **Handle** = Identifiant unique (index + version)
2. **KeeperMapWrapper** = API type-safe pour accéder aux entités
3. **Keeper Wrapper** = Gestionnaire avec méthodes helper intégrées

**Pattern recommandé moderne**: 
```csharp
// ✅ Avec Wrappers (Recommandé)
var wrapper = KeeperMapWrapper.GetCurrent();
var entity = wrapper?.Find<T>(handle);

// ❌ Accès direct (Déprécié)  
var keeper = baseGame.GetKeeper().GetNativeObject();
var entity = keeper.GetFieldValue("map").InvokeMethod<T>("Find", handle);
```

**Avantages des Wrappers**:
- 🛡️ Type-safe et gestion d'erreurs automatique
- ⚡ Performance optimisée (moins de réflexion)
- 🔧 Méthodes helper intégrées (diagnostics, énumération)
- 📈 Facilement extensible pour futures fonctionnalités