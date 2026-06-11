# ModDevHelper — Plugin de Debug YAML

> **Statut :** Opérationnel — DLL déployée dans `BepInEx\plugins\`  
> **Usage :** Dev only — activer pendant le développement YAML, désactiver pour la release

---

## Qu'est-ce que c'est ?

ModDevHelper est un plugin BepInEx qui s'active à chaque chargement de jeu et :

1. **Audite tous les buildings et resources chargés** après `YAMLLoader.CompleteLoading()`  
2. **Bloque les crashs `PlacementPresenter.Start()`** en interceptant les buildings avec `prefabName` null avant qu'ils ne crashent Unity

Sans ce plugin, un `materialType: Mined` sur une resource custom ou un `prefabName` null sur un building provoquent des crashes silencieux (pas de nom de la source dans les logs).

---

## Comment l'utiliser

### Lire les résultats

Ouvrir `D:\SteamLibrary\steamapps\common\Per Aspera\BepInEx\LogOutput.log` et chercher `[ModDev]` :

```
[ModDev] === ModDevHelper YAML Audit START ===
[ModDev] [RESOURCE] [043] 'resource_ai_data_core' material=Placeholder prefab='(null)'
[ModDev] [RESOURCE] 'resource_worker_drone_kit' (idx=44): materialType=Mined sur resource custom
         → utiliser 'materialType: Placeholder' pour eviter crash scatter/BuildPanel
[ModDev] === Audit termine : 1 erreur(s), 0 warning(s) ===
```

**Ligne `=== Audit OK ===`** → aucun problème détecté dans le YAML chargé.

---

## Règles vérifiées

### Resources

| Sévérité | Condition | Message |
|----------|-----------|---------|
| `ERROR` | Resource avec `index > 42` + `materialType: Mined` ou `Manufactured` | crash IndexOutOfRange probable dans BuildPanel/scatter |
| `WARNING` | Resource `Mined`/`Manufactured` avec `prefabName` vide | crash possible si utilisée comme `outputResource` |

### Buildings

| Sévérité | Condition | Message |
|----------|-----------|---------|
| `ERROR` | Building mod avec `prefabName` null ou vide | crash `PlacementPresenter.Start()` au placement |
| `WARNING` | `outputResource` d'un building sans `prefabName` + type `Mined`/`Manufactured` | crash affichage BuildPanel |

---

## Configuration

**Fichier** : `BepInEx\config\com.modperaspera.moddevhelper.cfg` (créé au 1er lancement)

```ini
[General]
Enabled = true

[Audit]
VerboseBuildings = false   # true = log tous les buildings, pas seulement les problèmes
VerboseResources = true    # true = log toutes les resources custom (index > 42)
```

Mettre `Enabled = false` en release pour ne pas impacter les performances.

---

## Fixes courants signalés

### `ERROR [RESOURCE] materialType=Mined sur resource custom`

Dans le YAML de la resource :
```yaml
# AVANT (crash)
materialType: Mined

# APRÈS (correct pour resource custom)
materialType: Placeholder
```

### `ERROR [BUILDING] prefabName null/vide`

Dans le YAML du building, vérifier que `prefabName` correspond à un prefab Unity existant.  
Exemple depuis un building vanilla similaire :
```yaml
prefabName: BasicColonyBuilding   # copier depuis un building vanilla qui fonctionne
```

---

## Architecture technique

```
F:\ModPeraspera\Individual-Mods\ModDevHelper\
├── ModDevHelperPlugin.cs       — BasePlugin, Harmony init, ConfigEntry
├── CompleteLoadingPatch.cs     — Postfix YAMLLoader.CompleteLoading (via AccessTools)
├── PostLoadAuditor.cs          — Règles R1–R4 sur ResourceType.table + BuildingType.table
└── PlacementPresenterPatch.cs  — Prefix PlacementPresenter.Start() (bloc si prefabName null)
```

Voir aussi : `F:\ModPeraspera\Internal_doc\ARCHITECTURE\ModDevHelper-Spec.md` — spec complète.
