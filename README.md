# Per Aspera Modding Organization Wiki

📖 **Public Documentation Hub**: https://github.com/PerAsperaMods/.github/tree/main/Organization-Wiki

This repository contains comprehensive documentation for Per Aspera modding, including guides, references, and architectural documentation.

---

## 🚀 Quick Start

### New to Modding?
- **[Installation Guide](getting-started/Installation.md)** - Set up your development environment
- **[First Mod Tutorial](getting-started/First-Mod.md)** - Create your first working mod
- **[Troubleshooting](troubleshooting/Common-Errors.md)** - Common issues and solutions

### Existing Modders?
- **[SDK Components](architecture/SDK-Components.md)** - Complete 9-project SDK reference
- **[Game Commands](reference/Game-Commands.md)** - 55 validated runtime commands
- **[Architecture Overview](architecture/Overview.md)** - System design patterns

---

## 📚 Documentation Structure

### 🎯 **Getting Started**
- [Installation.md](getting-started/Installation.md) - Development environment setup
- [First-Mod.md](getting-started/First-Mod.md) - Complete beginner tutorial
- [Development-Workflow.md](getting-started/Development-Workflow.md) - Best practices

### 🏗️ **Architecture**
- [Overview.md](architecture/Overview.md) - System design and patterns
- [SDK-Components.md](architecture/SDK-Components.md) - **Complete 9-project SDK reference**
- [Plugin-Architecture.md](architecture/Plugin-Architecture.md) - BepInX 6 IL2CPP structure

### 📖 **Reference**
- [Game-Commands.md](reference/Game-Commands.md) - **55 validated programmatic commands**
- [SDK-DLL-Props-Guide.md](reference/SDK-DLL-Props-Guide.md) - **Simplified SDK integration with .props file**
- [Console-Commands.md](reference/Console-Commands.md) - Debug/cheat console commands ⚠️
- [Manifest-YAML.md](reference/Manifest-YAML.md) - Complete manifest structure ⚠️
- [Building-YAML-Properties.md](reference/Building-YAML-Properties.md) - Building properties reference ⚠️
- [Special-Projects.md](reference/Special-Projects.md) - Large-scale project system ⚠️
- [Localization.md](reference/Localization.md) - Multi-language support ⚠️
- [Texture-Overrides.md](reference/Texture-Overrides.md) - Custom graphics system ⚠️

### ⚡ **Advanced**
- [Harmony-Patching.md](advanced/Harmony-Patching.md) - Runtime modification techniques
- [Performance.md](advanced/Performance.md) - Optimization strategies
- [Event-System.md](advanced/Event-System.md) - Game event handling

### 🛠️ **Troubleshooting** 
- [Common-Errors.md](troubleshooting/Common-Errors.md) - Frequent issues and fixes
- [Debugging.md](troubleshooting/Debugging.md) - Debug techniques
- [BepInX-Errors.md](troubleshooting/BepInX-Errors.md) - BepInX-specific problems

### 👥 **Community**
- [Contributing.md](community/Contributing.md) - How to contribute to the project
- [Modding-Guidelines.md](community/Modding-Guidelines.md) - Community standards

---

## 🚨 Documentation Validation Status

### ✅ **Validated & Production Ready**
- SDK Components (complete 9-project reference)
- Game Commands (55 runtime-validated commands)
- Installation guides and tutorials
- Architecture documentation
- Troubleshooting guides

### ⚠️ **Official But Potentially Obsolete**
Documentation marked with ⚠️ comes from **ISA Quantum AI Laboratory Modding Manual v2.0** but **requires validation against current game**:

- **Console Commands** - Debug commands may be outdated
- **Manifest YAML** - Structure may have changed
- **Building YAML Properties** - Many properties likely obsolete
- **Special Projects** - System may no longer exist
- **Localization** - CSV format may be incorrect
- **Texture Overrides** - Complex Unity system likely non-functional

**⚠️ Critical Warning**: These documents contain both valuable information and potentially obsolete data. Always cross-validate against current working mods and game behavior.

---

## 🔧 Three-Tier Documentation System

### 1. **Organization-Wiki/** (Public)
User-facing documentation, tutorials, and guides.
**Target**: Mod developers and community

### 2. **Reference/** (Quick Lookup)  
Concise reference materials and cheat sheets.
**Target**: Experienced developers

### 3. **Internal_doc/** (Technical)
Technical specifications, AI guidance, and detailed references.
**Target**: Advanced developers and AI agents

---

## 📋 Usage Instructions

### For End Users
1. Start with [Installation.md](getting-started/Installation.md)
2. Follow [First-Mod.md](getting-started/First-Mod.md) tutorial  
3. Use [SDK-Components.md](architecture/SDK-Components.md) as your main reference
4. Check [Troubleshooting](troubleshooting/Common-Errors.md) when stuck

### For Contributors
1. Read [Contributing.md](community/Contributing.md) guidelines
2. Follow [Development-Workflow.md](getting-started/Development-Workflow.md)
3. Use **validated documentation** as examples
4. **Never reference unvalidated content** without verification

### For AI Agents
1. **Always check validated documentation first**
2. **Use ⚠️ marked content with extreme caution**  
3. **Cross-reference with Internal_doc/ for technical details**
4. **Prioritize working examples over theoretical documentation**

---

## 🔄 GitHub Wiki Synchronization

These files are synchronized with the GitHub Wiki at:
**https://github.com/PerAsperaMods/.github/tree/main/Organization-Wiki**

To update the public wiki:
```bash
git add Organization-Wiki/
git commit -m "Update wiki documentation"
git push origin main
```

---

## ⚡ Quick Action Items

### High Priority Validation
1. **Test unvalidated YAML structures** against current game
2. **Verify console commands** still work
3. **Check localization system** exists
4. **Validate building properties** with working mods

### Documentation Maintenance
1. **Move validated content** out of ⚠️ status
2. **Mark confirmed obsolete** information clearly
3. **Create updated guides** with current methods
4. **Maintain separation** between validated and unvalidated content

---

**Last Updated:** December 17, 2025  
**Status:** ✅ Complete documentation structure with validation system  
**Validation**: Mixed - Core docs validated, official manual content requires testing