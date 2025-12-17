# 🚀 Per Aspera Modding Framework - Official Documentation

🌍 **Welcome to the international Per Aspera modding community!**

[![Version](https://img.shields.io/badge/SDK-1.1.0-blue.svg)](https://github.com/PerAsperaMods/PerAspera-SDK/releases)
[![Framework](https://img.shields.io/badge/BepInEx-6.x%20IL2CPP-green.svg)](https://github.com/BepInEx/BepInEx)
[![Community](https://img.shields.io/badge/Discord-Join%20Us-purple.svg)](https://discord.gg/peraspera-modding)
[![Workshop](https://img.shields.io/badge/Steam-Workshop-blue.svg)](https://steamcommunity.com/workshop/browse/?appid=944290)

## 🎮 What is Per Aspera Modding?

Per Aspera is a Mars terraforming strategy game where you build colonies and transform the red planet into a habitable world. Our modding framework provides comprehensive tools for creating sophisticated modifications:

- **🎮 Complete Game Access**: Direct integration with game systems (BaseGame, Universe, Planet)
- **🌡️ Advanced Climate System**: Real-time atmospheric simulation and terraforming tracking
- **🏗️ Building Integration**: Monitor and modify infrastructure behavior
- **📊 Event System**: Comprehensive event handling for all game activities  
- **⚙️ Override System**: Dynamic runtime modification of game values
- **🤖 Intelligent Agent System**: AI-powered development assistance
- **🛠️ Developer Tools**: Professional debugging, testing, and deployment

---

## 📖 Quick Navigation

### 🌟 Getting Started
- **[Installation Guide](getting-started/Installation.md)** - Set up your development environment (5 min)
- **[Your First Mod](getting-started/First-Mod.md)** - Create a working mod (15 min)
- **[Project Structure](getting-started/Project-Structure.md)** - Understand the framework architecture
- **[Build & Deploy](getting-started/Build-Deploy.md)** - Compile and test your mods
- **[Quick Reference](getting-started/Quick-Reference.md)** - Common code patterns

### 🤖 Intelligent Agent System  
- **[Agent System Overview](agents/Overview.md)** - How AI assistants help you mod
- **[BepInEx Development Agent](agents/BepInEx-Agent.md)** - C# plugin & IL2CPP expert
- **[YAML Datamodel Agent](agents/YAML-Agent.md)** - Game data modification expert
- **[SDK Usage Agent](agents/SDK-Agent.md)** - Game API wrapper expert
- **[CI/CD Automation Agent](agents/CICD-Agent.md)** - GitHub Actions expert
- **[Architecture Design Agent](agents/Architecture-Agent.md)** - System design expert

### 📚 Common Modding Tasks
- **[Modifying Buildings](tutorials/Buildings.md)** - Change stats, behavior, and production
- **[Adding Resources](tutorials/Resources.md)** - Create or modify resource types
- **[Technology Trees](tutorials/Technology.md)** - Modify research and dependencies
- **[Event Handling](tutorials/Events.md)** - React to game events in your code
- **[UI Customization](tutorials/UI.md)** - Add custom interface elements
- **[Climate Modification](tutorials/Climate.md)** - Alter terraforming mechanics

### 🔧 SDK API Reference
- **[ModSDK Overview](sdk/Overview.md)** - High-level event-driven SDK
- **[Core API](sdk/Core-API.md)** - PerAspera.Core utilities & extensions
- **[Game Wrappers](sdk/Wrappers.md)** - Type-safe game object access
- **[Event System](sdk/Events.md)** - Complete event documentation
- **[Climate API](sdk/Climate.md)** - Terraforming system helpers
- **[Commands API](sdk/Commands.md)** - Game command integration
- **[Override System](sdk/Overrides.md)** - Runtime value modification

### 🎮 Native Game Classes
- **[BaseGame](native-classes/BaseGame.md)** - Core game singleton & lifecycle
- **[Universe](native-classes/Universe.md)** - Universe, factions & global state
- **[Planet](native-classes/Planet.md)** - Planet systems, buildings & resources
- **[Building](native-classes/Building.md)** - Building mechanics & production
- **[Resource](native-classes/Resource.md)** - Resource management & flow
- **[Technology](native-classes/Technology.md)** - Research trees & unlocks
- **[Atmosphere](native-classes/Atmosphere.md)** - Climate & terraforming

### 🛠️ Advanced Development
- **[BepInEx IL2CPP](advanced/BepInEx-IL2CPP.md)** - Low-level plugin development
- **[Harmony Patching](advanced/Harmony-Patching.md)** - Runtime code modification
- **[Performance Optimization](advanced/Performance.md)** - Efficient mod patterns
- **[Debugging Guide](advanced/Debugging.md)** - Troubleshoot issues effectively
- **[IL2CPP Interop](advanced/IL2CPP-Interop.md)** - Working with IL2CPP types
- **[Multi-Mod Compatibility](advanced/Compatibility.md)** - Play nice with others

### 🏗️ Architecture & Design
- **[Architecture Overview](architecture/Overview.md)** - Design patterns for complex mods
- **[Multi-Mod Compatibility](architecture/Compatibility.md)** - Mod communication protocols
- **[Testing Strategies](architecture/Testing.md)** - Unit and integration testing

### 🤝 Community
- **[Contributing Guidelines](community/Contributing.md)** - How to contribute to the SDK
- **[Code of Conduct](community/Code-of-Conduct.md)** - Community standards
- **[Release Notes](community/CHANGELOG.md)** - Version history

### 📦 Content Modification
- **[YAML Datamodel Guide](content/YAML-Datamodel.md)** - Buildings, resources, tech
- **[Localization Guide](content/Localization.md)** - Multi-language support
- **[Asset Creation](content/Asset-Creation.md)** - Graphics, audio & UI
- **[Save Compatibility](content/Save-Compatibility.md)** - Maintain save file integrity

### 🐛 Troubleshooting
- **[Common Errors](troubleshooting/Common-Errors.md)** - Quick fixes for frequent issues
- **[Build Problems](troubleshooting/Build-Problems.md)** - Compilation & reference errors
- **[Runtime Issues](troubleshooting/Runtime-Issues.md)** - Crashes & exceptions
- **[IL2CPP Errors](troubleshooting/IL2CPP-Errors.md)** - Type conversion problems

### 🌍 Community & Resources
- **[Contributing Guide](community/Contributing.md)** - How to contribute to SDK
- **[Code Standards](community/Code-Standards.md)** - Conventions & best practices
- **[Release Process](community/Release-Process.md)** - Publishing your mods
- **[Steam Workshop](community/Steam-Workshop.md)** - Share with the community
- **[FAQ](community/FAQ.md)** - Frequently asked questions

---

## 🎯 Quick Start Paths

| I want to... | Start here... |
|--------------|---------------|
| **Create my first mod** | [Installation Guide](getting-started/Installation.md) → [First Mod](getting-started/First-Mod.md) |
| **Modify building stats** | [Buildings Tutorial](tutorials/Buildings.md) |
| **Use AI agent help** | [Agent System Overview](agents/Overview.md) |
| **Fix an error** | [Troubleshooting](troubleshooting/Common-Errors.md) |
| **Use high-level SDK** | [SDK Overview](sdk/Overview.md) |
| **Write advanced patches** | [BepInEx IL2CPP](advanced/BepInEx-IL2CPP.md) |
| **Publish on Workshop** | [Steam Workshop Guide](community/Steam-Workshop.md) |

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