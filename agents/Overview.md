# 🤖 Intelligent Agent System Overview

The Per Aspera Modding Framework features an **AI-powered development assistant** that automatically switches between specialized expert modes based on your needs.

## 🎯 What is the Agent System?

An intelligent co-pilot that:
- **🧠 Understands context** - Analyzes your request and activates the right expert
- **⚡ Switches automatically** - No manual mode selection needed
- **🔧 Provides expertise** - Deep knowledge in BepInEx, YAML, SDK, CI/CD, and architecture
- **🎯 Stays focused** - Each agent handles only its specialty
- **🔗 Coordinates seamlessly** - Agents work together on complex requests

## 📚 Available Agents

### 🔧 BepInEx Development Agent
**Expertise:** C# plugin development, IL2CPP integration, HarmonyX patching

**Triggers automatically when you mention:**
- `HarmonyPatch`, `Prefix`, `Postfix`, `Transpiler`
- `BasePlugin`, `ManualLogSource`
- IL2CPP errors, `NullReferenceException`
- `Assembly-CSharp`, `Il2CppInterop`

**Use for:**
- Creating BepInEx 6 IL2CPP plugins
- Writing Harmony patches
- Debugging runtime errors
- IL2CPP type conversion issues

**Documentation:** [BepInEx Agent Guide](BepInEx-Agent.md)

---

### 📄 YAML Datamodel Agent
**Expertise:** Game data modification, balancing, content creation

**Triggers automatically when you mention:**
- `building.yaml`, `resource.yaml`, `technology-*.yaml`
- `!resource`, `!knowledge`, `!buildingCategory`
- Datamodel, buildings, resources, tech trees
- Balancing, gameplay modifications

**Use for:**
- Modifying building stats
- Adding/changing resources
- Editing technology trees
- Balancing gameplay mechanics

**Documentation:** [YAML Agent Guide](YAML-Agent.md)

---

### 🧰 SDK Usage Agent
**Expertise:** Game API wrappers, SDK-first development, native game integration

**Triggers automatically when you mention:**
- `PerAspera.ModSDK`, `PerAspera.GameAPI`
- `BaseGame`, `Universe`, `Planet` wrappers
- Event system, type-safe access
- "SDK-first", "without patches"

**Use for:**
- Using high-level ModSDK
- Accessing game data via wrappers
- Event-driven development
- Avoiding low-level patches when possible

**Documentation:** [SDK Agent Guide](SDK-Agent.md)

---

### ⚙️ CI/CD Automation Agent
**Expertise:** GitHub Actions, build automation, deployment pipelines

**Triggers automatically when you mention:**
- `.github/workflows/`, GitHub Actions
- `dotnet build`, release automation
- Steam Workshop packaging
- Artifacts, deployment

**Use for:**
- Setting up automated builds
- Creating release workflows
- Steam Workshop deployment
- Multi-project automation

**Documentation:** [CI/CD Agent Guide](CICD-Agent.md)

---

### 🏗️ Architecture Design Agent
**Expertise:** System design, patterns, reverse engineering, performance

**Triggers automatically when you mention:**
- "design system for", "architecture for"
- "reverse engineer", "analyze system"
- UML, C4 diagrams, ADR
- Patterns: MVC, Observer, Factory
- Performance optimization

**Use for:**
- Planning complex mod systems
- Analyzing game internals
- Refactoring existing code
- Performance optimization
- Design documentation

**Documentation:** [Architecture Agent Guide](Architecture-Agent.md)

---

### 🎯 General Coordinator Agent
**Expertise:** Multi-domain coordination, project planning, integration

**Triggers automatically when you mention:**
- "create mod from scratch"
- "complete system", "end-to-end"
- Multi-domain requirements
- Strategic planning

**Use for:**
- Creating complete mods
- Complex multi-step projects
- Coordinating multiple domains
- Strategic mod planning

**Documentation:** [General Agent Guide](General-Agent.md)

---

## 🎨 How It Works

### Automatic Detection Example

**Your request:** *"I'm getting a NullReferenceException in my Harmony patch"*

```
🔍 Analyzing request...
✅ Detected: IL2CPP error + Harmony patch
🔧 Activating: BepInEx Expert Mode
→ Runtime debugging expertise
→ Harmony patch analysis
→ IL2CPP interop troubleshooting
```

**Your request:** *"How to modify solar panel energy production in building.yaml?"*

```
🔍 Analyzing request...
✅ Detected: building.yaml + stat modification
📄 Activating: YAML Datamodel Mode
→ Building configuration expertise
→ Balance analysis
→ Save compatibility validation
```

**Your request:** *"Design an architecture for dynamic weather events"*

```
🔍 Analyzing request...
✅ Detected: System design + architecture
🏗️ Activating: Architecture Design Mode
→ System design patterns
→ Performance analysis
→ Component modeling
→ ADR generation
```

---

## 🎛️ Manual Agent Override

While the system auto-detects, you can force a specific agent:

```
@bepinex - C# development and IL2CPP expertise
@yaml - Datamodel and gameplay balancing
@sdk - Game API wrapper usage
@cicd - GitHub Actions automation
@architecture - System design and planning
@general - Multi-domain coordination
```

### When to Use Manual Override

**Example 1:** SDK-first approach
```
@sdk How can I access building energy production without patches?
```
Forces SDK agent even though you might need patches later.

**Example 2:** Architecture planning before coding
```
@architecture Plan the system architecture before implementing the mod
```
Forces design before implementation.

---

## 🔗 Multi-Agent Coordination

For complex requests spanning multiple domains:

### Example: Complete Mod Development

**Your request:** *"Create a complete terraforming overhaul mod"*

**Agent coordination:**
```
🎯 General Coordinator (Primary)
├── 🏗️ Architecture Agent
│   └── Design system architecture
│   └── Define component interactions
│
├── 🧰 SDK Agent  
│   └── Map available game APIs
│   └── Identify SDK coverage
│
├── 🔧 BepInEx Agent
│   └── Implement required patches
│   └── Create runtime modifications
│
├── 📄 YAML Agent
│   └── Modify datamodel files
│   └── Balance new mechanics
│
└── ⚙️ CI/CD Agent
    └── Setup build pipeline
    └── Create release workflow
```

**Seamless handoffs:**
1. Architecture agent designs system
2. SDK agent maps game APIs
3. BepInEx agent implements patches for gaps
4. YAML agent modifies data files
5. CI/CD agent automates everything

---

## 💡 Best Practices

### ✅ Let Auto-Detection Work

**Good:**
```
"I need to modify the climate system to add new greenhouse gases"
→ Auto-detects: YAML agent + SDK agent coordination
```

**Also Good:**
```
"Getting errors when patching Building.UpdateEnergyProduction"
→ Auto-detects: BepInEx agent + IL2CPP expertise
```

### ✅ Be Specific About Your Goal

**Good:**
```
"Create an event-driven system that tracks resource production rates"
→ Clear goal: SDK agent for events, architecture for design
```

**Less Good:**
```
"Help with resources"
→ Ambiguous: Could be YAML, SDK, or patches
```

### ✅ Mention Technologies Explicitly

**Good:**
```
"Use HarmonyX to patch the ResourceManager.AddResource method"
→ Clear: BepInEx agent
```

**Good:**
```
"Modify technology-engineering.yaml to add new research tier"
→ Clear: YAML agent
```

### ✅ Request Multi-Step Planning

**Good:**
```
"Plan and implement a complete building production chain mod from scratch"
→ Coordinator handles architecture → SDK → BepInEx → YAML → CI/CD
```

---

## 🎓 Learning Path

### Beginner → Intermediate → Advanced

#### 1️⃣ **Start with SDK Agent**
Learn the high-level API before diving into patches:
```
@sdk Show me how to track building construction using events
```

#### 2️⃣ **Add YAML Skills**
Modify game data without code:
```
@yaml How to create a new building type with custom production chain?
```

#### 3️⃣ **Learn BepInEx When Needed**
Only go low-level when SDK insufficient:
```
@bepinex The SDK can't modify energy production at runtime, 
how do I patch it?
```

#### 4️⃣ **Automate with CI/CD**
Professional deployment:
```
@cicd Setup automated builds and Steam Workshop deployment
```

#### 5️⃣ **Design Complex Systems**
Architecture-first approach:
```
@architecture Design a multi-mod compatibility layer with 
event bus and override registry
```

---

## 🆘 Getting Help

### Quick Questions
Just ask naturally - auto-detection handles most cases:
```
"Why is my mod not loading?"
"How to add a new resource?"
"Best way to track temperature changes?"
```

### Complex Projects
Request planning first:
```
@general Create a complete overhaul mod that adds seasons to Mars
```

### Debugging
Include error context:
```
@bepinex Getting this error when patching: [paste error]
```

### Performance Issues
```
@architecture My mod causes lag in late-game, how to optimize?
```

---

## 📊 Agent Selection Guide

| Task | Best Agent | Reason |
|------|-----------|--------|
| **Create new plugin** | `@bepinex` | Low-level framework |
| **Modify building stats** | `@yaml` | Data-driven |
| **Track game events** | `@sdk` | Event system |
| **Patch game code** | `@bepinex` | Runtime modification |
| **Setup CI/CD** | `@cicd` | Automation |
| **Plan complex mod** | `@architecture` | System design |
| **Multi-domain mod** | `@general` | Coordination |
| **Performance issue** | `@architecture` | Analysis |
| **Add research** | `@yaml` | Tech trees |
| **Custom UI** | `@bepinex` | Low-level access |

---

## 🎯 Next Steps

**Ready to use the agents?**

1. **[Try SDK Agent](SDK-Agent.md)** - Start with high-level development
2. **[Learn YAML Agent](YAML-Agent.md)** - Modify game data
3. **[Explore BepInEx Agent](BepInEx-Agent.md)** - Advanced patching
4. **[Setup CI/CD](CICD-Agent.md)** - Automate your workflow

**Or jump into coding:**
- **[Your First Mod](../getting-started/First-Mod.md)** - Hands-on tutorial
- **[Common Tasks](../tutorials/Buildings.md)** - Practical examples

---

**Questions?** The agents are designed to help! Just ask naturally and let the system find the right expert.
