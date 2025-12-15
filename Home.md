# PerAspera Modding Framework

🌍 **Welcome to the international Per Aspera modding community!**

[![Version](https://img.shields.io/badge/SDK-1.1.0-blue.svg)](https://github.com/PerAsperaMods/PerAspera-SDK/releases)
[![Framework](https://img.shields.io/badge/BepInEx-6.x%20IL2CPP-green.svg)](https://github.com/BepInEx/BepInEx)
[![Community](https://img.shields.io/badge/Discord-Join%20Us-purple.svg)](https://discord.gg/peraspera-modding)
[![Workshop](https://img.shields.io/badge/Steam-Workshop-blue.svg)](https://steamcommunity.com/workshop/browse/?appid=944290)

## 🚀 What is Per Aspera Modding?

Per Aspera is a Mars terraforming strategy game where you build colonies and transform the red planet into a habitable world. Our modding framework provides comprehensive tools for creating sophisticated modifications:

- **🎮 Complete Game Access**: Direct integration with game systems (BaseGame, Universe, Planet)
- **🌡️ Advanced Climate System**: Real-time atmospheric simulation and terraforming tracking
- **🏗️ Building Integration**: Monitor and modify infrastructure behavior
- **📊 Event System**: Comprehensive event handling for all game activities  
- **⚙️ Override System**: Dynamic runtime modification of game values
- **🛠️ Developer Tools**: Intelligent configuration, debugging, and deployment

## 📚 Documentation Structure

### 🎯 Getting Started
- **[Installation & Setup](Installation-Setup)** - Complete development environment setup
- **[Your First Mod](First-Mod-Tutorial)** - Step-by-step beginner tutorial
- **[Architecture Overview](Architecture-Overview)** - Understanding SDK design
- **[Quick Reference](Quick-Reference)** - Common tasks and code snippets

### 🔧 Core Systems
- **[SDK API Reference](SDK-API-Reference)** - Complete ModSDK documentation
- **[Climate System Guide](Climate-System-Guide)** - Atmospheric simulation APIs
- **[Building System Guide](Building-System-Guide)** - Infrastructure management
- **[Event System Guide](Event-System-Guide)** - Event handling and custom events
- **[Override System Guide](Override-System-Guide)** - Dynamic value modification

### 🏗️ Advanced Topics
- **[Performance Optimization](Performance-Guide)** - Efficient mod development
- **[Debugging & Troubleshooting](Debugging-Guide)** - Common issues and solutions
- **[Multi-Mod Compatibility](Compatibility-Guide)** - Working with other mods
- **[Custom Systems Development](Custom-Systems-Guide)** - Extending the framework

### 📦 Content Modification
- **[YAML Datamodel Guide](YAML-Datamodel-Guide)** - Buildings, resources, technologies
- **[Localization Guide](Localization-Guide)** - Multi-language support
- **[Asset Creation](Asset-Creation-Guide)** - Graphics and audio

### 🧪 Examples & Samples
- **[Code Examples](Code-Examples)** - Ready-to-use code snippets
- **[Complete Mod Projects](Sample-Projects)** - Full implementations
- **[Best Practices](Best-Practices)** - Professional development patterns

## 🌍 International Community

### 🤝 Getting Help & Support
- **🔥 [Discord Community](https://discord.gg/peraspera-modding)** - Real-time help in multiple languages
- **📖 [GitHub Discussions](https://github.com/PerAsperaMods/PerAspera-SDK/discussions)** - Asynchronous community support
- **❓ [FAQ](FAQ)** - Frequently asked questions
- **🐛 [Report Issues](https://github.com/PerAsperaMods/PerAspera-SDK/issues)** - Bug reports and feature requests

### 🌟 Showcase & Sharing
- **[Featured Mods](Featured-Mods)** - Community highlighted projects
- **[Steam Workshop](https://steamcommunity.com/workshop/browse/?appid=944290)** - Official mod distribution
- **[Community Creations](Community-Creations)** - Developer showcase
- **[Collaboration Hub](Collaboration-Hub)** - Find team members

### 🤲 Contributing
- **[Contribution Guide](Contributing)** - How to help improve the framework
- **[Translation Project](Translation-Project)** - Help translate documentation
- **[Code of Conduct](Code-of-Conduct)** - Community guidelines
- **[Developer Setup](Developer-Setup)** - Contributing to SDK development

## 🔗 Essential Resources

| Resource | Description | Link |
|----------|-------------|------|
| **🎮 Steam Workshop** | Official mod distribution platform | [Browse Mods](https://steamcommunity.com/workshop/browse/?appid=944290) |
| **📦 NuGet Packages** | SDK packages for development | [PerAspera.ModSDK](https://www.nuget.org/packages/PerAspera.ModSDK) |
| **🐙 GitHub Organization** | Source code and repositories | [PerAsperaMods](https://github.com/PerAsperaMods) |
| **📖 Game Wiki** | Official game information | [Per Aspera Wiki](https://per-aspera.fandom.com/) |
| **💬 Discord** | International modding community | [Join Server](https://discord.gg/peraspera-modding) |
| **🎯 Reddit** | Community discussions | [r/PerAspera](https://reddit.com/r/PerAspera) |

## 📈 Framework Status

### Current Versions
- **SDK Version**: 1.1.0 (December 2024)
- **Supported Per Aspera**: Latest (1.4.x)
- **BepInEx**: 6.0.0-be.752+
- **Target Framework**: .NET 6.0

### Compatibility Matrix
| SDK Version | Per Aspera | BepInEx | .NET |
|-------------|------------|---------|------|
| 1.1.x | Latest | 6.0.0-be.752+ | 6.0+ |
| 1.0.x | 1.4.x | 6.0.0-be.700+ | 6.0+ |

### Community Metrics
- **Active Developers**: 50+ international contributors
- **Published Mods**: 200+ on Steam Workshop
- **Languages Supported**: English, French, German, Spanish, Japanese
- **Discord Members**: 1,500+ active modders

## 🎨 Quick Start Examples

### Climate Monitoring Mod
```csharp
[BepInPlugin("com.yourname.climatemod", "Climate Monitor", "1.0.0")]
public class ClimateMonitor : BasePlugin
{
    public override void Load()
    {
        ModSDK.Initialize(this);
        ModSDK.Events.Subscribe("climate.temperatureChanged", OnClimateChange);
        Log.LogInfo("🌡️ Climate monitoring started!");
    }
    
    private void OnClimateChange(object data)
    {
        var climate = ModSDK.Universe.GetClimateData();
        Log.LogInfo($"Mars temperature: {climate.Temperature}K");
    }
}
```

### Building Enhancement Mod
```csharp
// Increase solar panel efficiency by 25%
OverrideSystem.RegisterOverride(
    "buildings.solar.efficiency", 
    1.25f, 
    "Enhanced solar panels for faster energy production"
);
```

## 🎆 Get Started Today!

Ready to start your Mars terraforming modding journey?

1. **🚀 [Install the SDK](Installation-Setup)** - Set up your development environment
2. **📚 [Read the Tutorial](First-Mod-Tutorial)** - Create your first mod in 30 minutes  
3. **🌍 [Join Discord](https://discord.gg/peraspera-modding)** - Connect with the international community
4. **📦 [Publish on Workshop](https://steamcommunity.com/workshop/browse/?appid=944290)** - Share your creation

---

> 🌍 **"Mars awaits your creativity"** - Create, share, terraform!
> 
> *Building the future of Mars, one mod at a time.*

**Wiki maintained by the PerAsperaMods international community** 🌟  
*Available in: English • Français • Deutsch • Español • 日本語*