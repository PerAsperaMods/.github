# Getting Started with Per Aspera Modding

🎯 **Your first Per Aspera mod in 15 minutes!**

## 🎮 What is Per Aspera?

Per Aspera is a Mars terraforming strategy game where you build colonies and transform the red planet into a habitable world. The modding system allows you to:

- ✅ **Modify buildings** and their behavior
- ✅ **Create new events** and quests  
- ✅ **Customize technologies** and research
- ✅ **Adjust economic** balancing
- ✅ **Extend gameplay** with C#

## 🛠️ Mod Types

### 1. **YAML Mods** (Easy) 
- Modify **data files**
- No programming required
- **Simple installation** via mods folder
- Examples: new buildings, resource balancing

### 2. **BepInEx Mods** (Advanced)  
- **C#** programming with Harmony patches
- **Deep gameplay** modifications
- Installation via **BepInEx framework**
- Examples: new mechanics, custom UI

## 🚀 Quick Setup

### Prerequisites
- Per Aspera installed via Steam
- Windows 10/11

### Step 1: BepInEx Framework
1. Download **BepInEx IL2CPP**: [GitHub Releases](https://github.com/BepInEx/BepInEx/releases)
2. Choose: `BepInEx_UnityIL2CPP_x64_6.x.x.zip`
3. Extract to Per Aspera folder:
   ```
   F:\SteamLibrary\steamapps\common\Per Aspera\
   ├── BepInEx\          ← Nouveau
   ├── doorstop_config.ini
   ├── winhttp.dll
   └── Per Aspera.exe
   ```

### Step 2: First YAML Mod
Create a mod folder:
```
Per Aspera\
└── StreamingAssets\
    └── Mods\
        └── SpaceMirrorCounter\
            ├── manifest.yaml
            └── rule-patch.yaml
```

## 📝 Your First Mod

### "Space Mirror Counter" Mod

Create **manifest.yaml**:
```yaml
manifestVersion: 1
mod:
  name: "Space Mirror Counter"
  version: "1.0.0"
  author: "YourName"
  description: "Counts built space mirrors"

dependencies: []
files:
  - "rule-patch.yaml"
```

Create **rule-patch.yaml**:
```yaml
rules:
  # Increment counter each time a space mirror is built
  - eventType: !gameEventType GevBuildingBuilt
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building space_mirror
    effects:
      - !effects-set-blackboard-number
        name: "mirrors_count"
        value: "increment"
        
  # Afficher le compteur dans les logs
  - eventType: !gameEventType GevBuildingBuilt  
    domain: !domain Player
    criteria:
      - !operand-building-type
        buildingType: !building space_mirror
    effects:
      - !effects-debug-log
        message: "Space Mirror #{{ blackboard.mirrors_count }} built!"
```

## ✅ Test & Validation

1. **Launch Per Aspera**
2. **Build a space mirror**
3. **Check logs**: `BepInEx\LogOutput.log`
4. **Search for**: `"Space Mirror #1 built!"`

If it works, congratulations! 🎉

## 📚 Next Steps

### Learn More
- **[Events Reference](Per-Aspera-Events-Reference)** - All 122 available events
- **[Event Classes](Event-Classes-Reference)** - Technical C# documentation
- **[YAML Reference](YAML-Reference)** - Complete datamodel syntax

### Go Further  
- **[BepInEx Guide](BepInEx-Guide)** - Advanced C# mods with Harmony
- **[Featured Mods](Featured-Mods)** - Community examples
- **[Contributing](Contributing)** - Contribute to the framework

## 🤝 Need Help?

- 💬 **Discord PerAsperaMod**
- 🐛 **[GitHub Issues](https://github.com/PerAsperaMod/.github/issues)**
- 📖 **[FAQ](FAQ)** - Frequently asked questions
- 🎮 **[Steam Workshop](https://steamcommunity.com/app/944260/workshop/)**

## 🎯 Pro Tips

1. **Start simple** - Modify existing values first
2. **Test often** - Check logs after each change
3. **Backup** - Keep backups of your test saves
4. **Share** - The community always helps!

---

> 🌟 **First mod successful? You're now a Per Aspera modder!**