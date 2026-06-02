# 📦 Installation Simple - Per Aspera Mods

## 🎯 Installation en 3 étapes (Aucune compétence technique requise)

### 1. Téléchargement
- Allez sur [Releases](https://github.com/PerAsperaMods/ModPeraspera/releases/latest)
- Téléchargez **PerAspera-UserMods-vX.X.X.zip**

### 2. Installation
1. Ouvrez le fichier ZIP téléchargé
2. Extraire tous les fichiers vers :
   ```
   Steam/steamapps/common/Per Aspera/BepInEx/plugins/
   ```
   
   **💡 Conseil :** Si le dossier `BepInEx` n'existe pas, installez d'abord BepInEx (voir prérequis ci-dessous)

### 3. Lancement
- Démarrez Per Aspera normalement
- Les mods se chargent automatiquement
- Aucune configuration supplémentaire nécessaire

---

## ⚙️ Prérequis (Installation unique)

### BepInEx pour IL2CPP
1. Téléchargez [BepInEx 6.x IL2CPP](https://github.com/BepInEx/BepInEx/releases)
2. Extraire dans le dossier Per Aspera : `Steam/steamapps/common/Per Aspera/`
3. Lancez le jeu une fois pour initialiser BepInEx
4. Fermez le jeu

**✅ Vous êtes prêt à installer nos mods !**

---

## 🎮 Mods Inclus

| Mod | Description | Fonctionnalité |
|-----|-------------|----------------|
| **AtmosphereRelease** | Gestion avancée de l'atmosphère | Terraforming optimisé |
| **MasterGui** | Interface améliorée | UI personnalisée |
| **SolarPowerOverride** | Énergie solaire modifiée | Production énergétique |
| **ReworkWaterLogic** | Logique de l'eau refonte | Systèmes hydriques |
| **ImportRessources** | Ressources personnalisées | Nouvelles ressources |
| **AsperaBaseGameProvider** | Fournisseur de données | APIs de base |

---

## ❓ Questions Fréquentes

### "Où est mon dossier Steam ?"
- **Windows** : `C:\Program Files (x86)\Steam\steamapps\common\Per Aspera\`
- **Steam Deck** : `~/.local/share/Steam/steamapps/common/Per Aspera/`

### "Je ne vois pas le dossier BepInEx"
- Installez d'abord BepInEx (voir prérequis ci-dessus)
- Lancez le jeu une fois après installation de BepInEx

### "Les mods ne se chargent pas"
1. Vérifiez que les DLL sont dans `BepInEx/plugins/`
2. Regardez les logs : `BepInEx/LogOutput.log`
3. Assurez-vous que BepInEx est bien installé

### "Puis-je désinstaller un mod ?"
- Oui, supprimez simplement le fichier `.dll` correspondant du dossier `plugins/`

---

## 🆘 Support

- **Bugs** : [Signaler un problème](https://github.com/PerAsperaMods/ModPeraspera/issues)
- **Discord** : [Communauté modding](https://discord.gg/peraspera-mods)
- **Documentation** : [Guide complet](https://github.com/PerAsperaMods/ModPeraspera/wiki)

---

## 🔄 Mise à Jour

Pour mettre à jour vers une nouvelle version :
1. Supprimez les anciens fichiers `.dll` du dossier `plugins/`
2. Téléchargez et installez la nouvelle version
3. Relancez le jeu

**💡 Les sauvegardes sont compatibles entre versions mineures**

---

*Installation simple, jeu amélioré ! 🚀*