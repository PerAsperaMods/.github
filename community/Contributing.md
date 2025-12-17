# Contributing to Per Aspera ModSDK

## 🎯 Welcome!

Thank you for your interest in contributing to the Per Aspera ModSDK! This document provides guidelines for contributing code, documentation, and improvements to the SDK framework.

**Ways to Contribute:**
- 🐛 Report bugs and issues
- 💡 Suggest new SDK features
- 📝 Improve documentation
- 🔧 Submit code improvements
- 🧪 Write tests and examples
- 🌍 Translate documentation

---

## 🚀 Getting Started

### 1. Development Environment Setup

**Prerequisites:**
- Visual Studio 2022 (Community or higher)
- .NET 6.0 SDK
- Git for Windows
- Per Aspera game installed (for testing)

**Clone the Repository:**
```powershell
cd F:\ModPeraspera
git clone https://github.com/YourOrg/PerAsperaModSDK.git
cd PerAsperaModSDK
```

**Build the SDK:**
```powershell
# Restore dependencies
dotnet restore

# Build all projects
dotnet build

# Run tests
dotnet test
```

### 2. Project Structure

```
PerAsperaModSDK/
├── PerAspera.Core/              # Core utilities and extensions
├── PerAspera.GameAPI/           # Game object wrappers
│   ├── Wrappers/                # Type-safe IL2CPP wrappers
│   └── Events/                  # Game event definitions
├── PerAspera.ModSDK/            # High-level SDK API
│   ├── SDK.cs                   # Main SDK facade
│   ├── Events.cs                # Event subscription system
│   └── Overrides.cs             # Runtime override system
├── PerAspera.Tests/             # Unit and integration tests
├── Examples/                    # Example mod projects
└── Documentation/               # API documentation source
```

---

## 📋 Contribution Guidelines

### Code Style

**C# Coding Standards:**
```csharp
// ✅ CORRECT: PascalCase for public members
public class BuildingWrapper
{
    public string TypeKey { get; private set; }
    public Vector3 Position { get; private set; }
    
    public void UpdateProduction(float amount)
    {
        // Method implementation
    }
}

// ✅ CORRECT: camelCase for private fields with underscore
private readonly ILogger _logger;
private Dictionary<int, Building> _buildingCache;

// ✅ CORRECT: Descriptive names
public float CalculateEnergyEfficiency(Building building)
{
    // ...
}

// ❌ WRONG: Abbreviations and Hungarian notation
public float CalcEngEff(Building bldg) // Don't do this
{
    // ...
}
```

**Formatting:**
- **Indentation:** 4 spaces (not tabs)
- **Line length:** 120 characters maximum
- **Braces:** K&R style (opening brace on same line)
- **Spacing:** One blank line between methods

**EditorConfig:**
```ini
# .editorconfig (already in repo)
root = true

[*.cs]
indent_style = space
indent_size = 4
end_of_line = crlf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
max_line_length = 120
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Class | PascalCase | `BuildingWrapper` |
| Interface | I + PascalCase | `IGameDataProvider` |
| Method | PascalCase | `GetAllBuildings()` |
| Property | PascalCase | `EnergyProduction` |
| Private field | _camelCase | `_buildingCache` |
| Const | UPPER_SNAKE_CASE | `MAX_BUILDINGS` |
| Event | On + PascalCase | `OnBuildingPlaced` |

### Documentation Requirements

**XML Documentation:**
```csharp
/// <summary>
/// Retrieves all buildings of a specific type from the current planet.
/// </summary>
/// <param name="buildingType">The building type key (e.g., "SolarPanel")</param>
/// <returns>Array of buildings matching the type, or empty array if none found</returns>
/// <exception cref="ArgumentNullException">Thrown when buildingType is null</exception>
/// <example>
/// <code>
/// var solarPanels = SDK.Game.GetBuildingsByType("SolarPanel");
/// foreach (var panel in solarPanels)
/// {
///     Console.WriteLine($"Panel at {panel.Position}");
/// }
/// </code>
/// </example>
public Building[] GetBuildingsByType(string buildingType)
{
    if (buildingType == null)
        throw new ArgumentNullException(nameof(buildingType));
        
    return SDK.Game.Planet.Buildings
        .Where(b => b.TypeKey == buildingType)
        .ToArray();
}
```

**README.md for Features:**
Every new feature should include:
- Purpose and use cases
- API examples
- Performance considerations
- Breaking changes (if any)

---

## 🔧 Development Workflow

### 1. Create a Feature Branch

```powershell
# Update main branch
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/building-efficiency-api

# Or for bug fixes
git checkout -b fix/null-reference-in-wrapper
```

**Branch Naming:**
- `feature/descriptive-name` - New features
- `fix/issue-description` - Bug fixes
- `docs/what-changed` - Documentation only
- `refactor/component-name` - Code refactoring

### 2. Make Your Changes

**Before coding:**
- [ ] Check if issue exists, create if not
- [ ] Discuss major changes in issue comments
- [ ] Review [Architecture Overview](../architecture/Overview.md)

**While coding:**
- [ ] Follow code style guidelines
- [ ] Add XML documentation
- [ ] Write unit tests for new code
- [ ] Update integration tests if needed
- [ ] Run existing tests (all should pass)

**Testing Checklist:**
```powershell
# Run all tests
dotnet test

# Check code coverage (if configured)
dotnet test /p:CollectCoverage=true

# Manual testing in-game
# 1. Build SDK: dotnet build
# 2. Copy DLLs to BepInEx/plugins/
# 3. Launch Per Aspera
# 4. Test feature in-game
# 5. Check BepInEx logs
```

### 3. Commit Your Changes

**Commit Message Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code restructuring (no behavior change)
- `perf`: Performance improvement
- `test`: Adding or updating tests
- `chore`: Build process, dependencies

**Examples:**
```bash
# Good commit messages:
git commit -m "feat(wrappers): add Atmosphere wrapper with gas composition API

- Added AtmosphereWrapper class with CO2/O2/N2/H2O properties
- Implemented TerraformingEffects property for climate impact
- Added unit tests for gas composition calculations
- Performance: < 0.1ms per access (cached native calls)

Closes #42"

git commit -m "fix(events): prevent NullReferenceException in OnBuildingDestroyed

Building pointer was not validated before accessing properties.
Added IL2CPP null check (Pointer != IntPtr.Zero).

Fixes #67"

# Bad commit messages:
git commit -m "fixed stuff"  # ❌ Too vague
git commit -m "WIP"  # ❌ Work in progress, not ready
git commit -m "asdfasdf"  # ❌ Meaningless
```

### 4. Submit Pull Request

**Before submitting:**
```powershell
# Sync with main
git fetch origin
git rebase origin/main

# Run final checks
dotnet build
dotnet test
```

**PR Title Format:**
```
[Type] Brief description of change
```

**PR Description Template:**
```markdown
## Description
Brief summary of what this PR accomplishes.

## Motivation
Why is this change needed? What problem does it solve?

## Changes Made
- Added X feature to Y component
- Modified Z to improve performance
- Updated documentation for ABC

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manually tested in-game (describe scenario)
- [ ] Performance profiled (if applicable)

## Breaking Changes
- [ ] None
- [ ] Yes (describe migration path below)

## Migration Guide
(If breaking changes) How should users update their code?

## Screenshots/Videos
(If UI changes) Include visuals

## Related Issues
Closes #123
Relates to #456

## Checklist
- [ ] Code follows style guidelines
- [ ] XML documentation added
- [ ] Tests added and passing
- [ ] Documentation updated
- [ ] CHANGELOG.md updated (for release)
```

---

## 🧪 Testing Guidelines

### Unit Test Structure

```csharp
using NUnit.Framework;
using PerAspera.GameAPI.Wrappers;

namespace PerAspera.Tests.Wrappers
{
    [TestFixture]
    public class BuildingWrapperTests
    {
        [Test]
        public void TypeKey_WithValidBuilding_ReturnsCorrectType()
        {
            // Arrange
            var mockNative = CreateMockBuilding("SolarPanel");
            var wrapper = new Building(mockNative);
            
            // Act
            string typeKey = wrapper.TypeKey;
            
            // Assert
            Assert.AreEqual("SolarPanel", typeKey);
        }
        
        [Test]
        public void Constructor_WithNullNative_ThrowsArgumentNullException()
        {
            // Act & Assert
            Assert.Throws<ArgumentNullException>(() => new Building(null));
        }
        
        [Test]
        public void IsAlive_WithDestroyedBuilding_ReturnsFalse()
        {
            // Arrange
            var mockNative = CreateMockBuilding("SolarPanel");
            mockNative.Health = 0f;
            var wrapper = new Building(mockNative);
            
            // Act
            bool isAlive = wrapper.IsAlive;
            
            // Assert
            Assert.IsFalse(isAlive);
        }
        
        private MockBuilding CreateMockBuilding(string type)
        {
            return new MockBuilding
            {
                TypeKey = type,
                Health = 100f,
                Position = Vector3.zero
            };
        }
    }
}
```

### Integration Test Example

```csharp
[TestFixture]
[Category("Integration")]
public class EventSystemIntegrationTests
{
    [Test]
    public void OnBuildingPlaced_WhenTriggered_NotifiesSubscribers()
    {
        // Arrange
        bool eventReceived = false;
        Building? receivedBuilding = null;
        
        SDK.Events.OnBuildingPlaced += (building) =>
        {
            eventReceived = true;
            receivedBuilding = building;
        };
        
        // Act
        var testBuilding = CreateTestBuilding();
        SimulateBuildingPlacement(testBuilding);
        
        // Assert
        Assert.IsTrue(eventReceived, "Event was not received");
        Assert.IsNotNull(receivedBuilding, "Building was null");
        Assert.AreEqual("SolarPanel", receivedBuilding.TypeKey);
    }
}
```

### Performance Test Requirements

```csharp
[TestFixture]
[Category("Performance")]
public class PerformanceTests
{
    [Test]
    public void GetAllBuildings_With1000Buildings_CompletesUnder1ms()
    {
        // Arrange
        CreateMassiveColony(1000);
        var stopwatch = Stopwatch.StartNew();
        
        // Act
        for (int i = 0; i < 100; i++)
        {
            var buildings = SDK.Game.Planet.Buildings;
        }
        
        stopwatch.Stop();
        
        // Assert
        double avgMs = stopwatch.Elapsed.TotalMilliseconds / 100.0;
        Assert.Less(avgMs, 1.0, $"Average: {avgMs:F2}ms (target: <1ms)");
    }
}
```

---

## 📝 Documentation Standards

### API Documentation

**Every public API needs:**
1. XML summary describing purpose
2. Parameter descriptions with validation rules
3. Return value description
4. Exception documentation
5. Usage example
6. Performance notes (if relevant)

### Wiki Documentation

**When adding SDK features:**
1. Update [SDK Overview](../sdk/Overview.md)
2. Add examples to relevant tutorials
3. Update [Architecture Overview](../architecture/Overview.md) if architectural change
4. Add entry to CHANGELOG.md

**Documentation style:**
- Use **active voice** ("Returns buildings" not "Buildings are returned")
- Be **concise** but **complete**
- Include **code examples** that actually work
- Explain **why**, not just **what**

---

## 🐛 Bug Reports

### Creating Quality Bug Reports

**Use this template:**
```markdown
## Bug Description
Clear description of the bug.

## Steps to Reproduce
1. Install SDK version X.X.X
2. Create mod with this code: [paste code]
3. Run game and trigger event Y
4. Observe error

## Expected Behavior
What should happen?

## Actual Behavior
What actually happens?

## Environment
- SDK Version: 1.1.0
- BepInEx Version: 6.0.0-pre.1
- Game Version: 1.5.2
- OS: Windows 11

## Logs
```
[Paste relevant BepInEx log output]
```

## Additional Context
Screenshots, save files, or other relevant info.
```

---

## 💡 Feature Requests

### Proposing New Features

**Before requesting:**
1. Search existing issues for duplicates
2. Consider if it fits SDK scope (not game modification)
3. Think about backward compatibility

**Feature request template:**
```markdown
## Feature Summary
One-sentence description.

## Motivation
Why is this needed? What problem does it solve?

## Proposed API
```csharp
// Example of how the API might look
SDK.NewFeature.DoSomething(parameter);
```

## Use Cases
1. Use case 1: [description]
2. Use case 2: [description]

## Alternatives Considered
- Alternative A: [pros/cons]
- Alternative B: [pros/cons]

## Implementation Notes
Technical considerations, challenges, dependencies.
```

---

## 🎓 Learning Resources

### For Contributors

**Internal Documentation:**
- [Architecture Overview](../architecture/Overview.md) - Design patterns
- [Harmony Patching](../advanced/Harmony-Patching.md) - IL2CPP techniques
- [Performance Guidelines](../advanced/Performance.md) - Optimization

**External Resources:**
- [BepInEx Documentation](https://docs.bepinex.dev/)
- [HarmonyX Wiki](https://github.com/BepInEx/HarmonyX/wiki)
- [Unity IL2CPP Scripting](https://docs.unity3d.com/Manual/IL2CPP.html)
- [C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)

---

## 📦 Release Process

### Versioning

We use [Semantic Versioning](https://semver.org/):
- **MAJOR**: Breaking API changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

**Examples:**
- `1.0.0` → `1.1.0` - Added new event system
- `1.1.0` → `1.1.1` - Fixed NullReference bug
- `1.1.1` → `2.0.0` - Changed SDK.Events API (breaking)

### Release Checklist

**SDK Maintainers:**
- [ ] All tests passing
- [ ] CHANGELOG.md updated
- [ ] Version bumped in all `.csproj` files
- [ ] Documentation updated
- [ ] Create Git tag `v1.2.0`
- [ ] Build release artifacts
- [ ] Create GitHub release with notes
- [ ] Publish to NuGet (if applicable)
- [ ] Announce in community Discord

---

## 🏆 Recognition

### Contributors Hall of Fame

All contributors are recognized in:
- `CONTRIBUTORS.md` file
- Release notes for their contributions
- GitHub insights and graphs

**Contribution Types:**
- 🔧 Code contributors
- 📝 Documentation writers
- 🐛 Bug reporters
- 💡 Feature designers
- 🌍 Translators
- 🧪 Testers

---

## ❓ Questions?

**Get Help:**
- **Discord:** [Per Aspera Modding Community](#)
- **GitHub Discussions:** For design discussions
- **GitHub Issues:** For bugs and features

**Response Times:**
- Issues/PRs: Within 48 hours
- Discord questions: Within 24 hours
- Email: Within 1 week

---

## 📜 Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inspiring community for all.

**Expected Behavior:**
- Use welcoming and inclusive language
- Be respectful of differing viewpoints
- Accept constructive criticism gracefully
- Focus on what is best for the community
- Show empathy towards other community members

**Unacceptable Behavior:**
- Harassment, trolling, or insulting comments
- Personal or political attacks
- Publishing others' private information
- Any conduct inappropriate in a professional setting

**Enforcement:**
Violations can be reported to [modding@peraspera.com](mailto:modding@peraspera.com).
All complaints will be reviewed and investigated.

---

## 📋 Checklist for Contributors

**Before your first contribution:**
- [ ] Read this contributing guide completely
- [ ] Set up development environment
- [ ] Build SDK successfully
- [ ] Run all tests
- [ ] Join community Discord
- [ ] Introduce yourself in #contributors channel

**For each contribution:**
- [ ] Create issue (if doesn't exist)
- [ ] Create feature branch
- [ ] Write code following style guide
- [ ] Add/update tests
- [ ] Add XML documentation
- [ ] Update wiki documentation
- [ ] Commit with proper message format
- [ ] Sync with main branch
- [ ] Create pull request with template
- [ ] Address review feedback
- [ ] Celebrate when merged! 🎉

---

**Thank you for contributing to Per Aspera ModSDK! Your work helps the entire modding community.** 🚀
