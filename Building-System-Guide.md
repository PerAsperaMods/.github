# Building System Guide

🏗️ **Master Mars infrastructure through advanced building management and production optimization.**

## 🎯 Table of Contents

- [Building Fundamentals](#building-fundamentals) - Understanding structures
- [Production Systems](#production-systems) - Resource management
- [Infrastructure Planning](#infrastructure-planning) - Strategic placement
- [Building Modifications](#building-modifications) - Customizing structures  
- [Automation Patterns](#automation-patterns) - Intelligent management
- [Performance Optimization](#performance-optimization) - Efficient operations

---

## Building Fundamentals

### 🏭 Building Categories

Per Aspera features several categories of structures, each serving specific purposes in Mars terraforming:

#### Power Generation

**Solar Arrays** - Primary energy source
```csharp
var solarArrays = BuildingSystem.GetBuildingsByType("SolarArray");
float totalPowerCapacity = 0f;
float totalPowerOutput = 0f;

foreach (var array in solarArrays)
{
    var production = BuildingSystem.GetBuildingProduction(array.RawBuilding);
    totalPowerCapacity += production.OutputRate;
    totalPowerOutput += production.ActualOutput;
}

float efficiency = totalPowerOutput / totalPowerCapacity * 100f;
Log.LogInfo($"☀️ Solar Infrastructure:");
Log.LogInfo($"   Arrays: {solarArrays.Length}");
Log.LogInfo($"   Capacity: {totalPowerCapacity:F1} MW");
Log.LogInfo($"   Output: {totalPowerOutput:F1} MW");
Log.LogInfo($"   Efficiency: {efficiency:F1}%");
```

**Nuclear Reactors** - Reliable baseline power
```csharp
var reactors = BuildingSystem.GetBuildingsByType("NuclearReactor");
foreach (var reactor in reactors)
{
    var production = BuildingSystem.GetBuildingProduction(reactor.RawBuilding);
    var safety = GetReactorSafety(reactor);  // Custom safety assessment
    
    Log.LogInfo($"⚛️ Reactor at {reactor.Position}: {production.ActualOutput:F1} MW (Safety: {safety})");
}
```

#### Resource Extraction

**Water Extractors** - Essential for life support
```csharp
void MonitorWaterInfrastructure()
{
    var extractors = BuildingSystem.GetBuildingsByType("WaterExtractor");
    var purifiers = BuildingSystem.GetBuildingsByType("WaterPurifier");
    
    float totalWaterProduction = 0f;
    foreach (var extractor in extractors)
    {
        var production = BuildingSystem.GetBuildingProduction(extractor.RawBuilding);
        totalWaterProduction += production.ActualOutput;
    }
    
    Log.LogInfo($"💧 Water Infrastructure:");
    Log.LogInfo($"   Extractors: {extractors.Length}");
    Log.LogInfo($"   Purifiers: {purifiers.Length}");
    Log.LogInfo($"   Production: {totalWaterProduction:F2} units/sol");
    
    // Check for production bottlenecks
    if (purifiers.Length < extractors.Length / 2)
    {
        Log.LogWarning("⚠️ Insufficient water purification capacity!");
    }
}
```

#### Atmospheric Processing

**Atmosphere Processors** - Core terraforming infrastructure
```csharp
void AnalyzeAtmosphericInfrastructure()
{
    var processors = BuildingSystem.GetBuildingsByType("AtmosphereProcessor");
    var vaporizers = BuildingSystem.GetBuildingsByType("WaterVaporizer");
    var oxygenGens = BuildingSystem.GetBuildingsByType("OxygenGenerator");
    
    Log.LogInfo($"🌬️ Atmospheric Infrastructure:");
    Log.LogInfo($"   Processors: {processors.Length}");
    Log.LogInfo($"   Vaporizers: {vaporizers.Length}");
    Log.LogInfo($"   O2 Generators: {oxygenGens.Length}");
    
    // Calculate total atmospheric impact
    float pressureIncrease = 0f;
    foreach (var processor in processors)
    {
        var impact = BuildingSystem.GetAtmosphericImpact(processor.RawBuilding);
        pressureIncrease += impact.PressureIncrease;
    }
    
    Log.LogInfo($"   Total pressure impact: +{pressureIncrease:F6} atm/sol");
}
```

### 🔧 Building States and Efficiency

#### Operational Status Monitoring

```csharp
public enum BuildingStatus
{
    Operational,      // Running normally
    PowerShortage,    // Insufficient power
    ResourceStarved,  // Missing input materials
    Maintenance,      // Requires maintenance
    Damaged,         // Needs repair
    Shutdown        // Manually disabled
}

public BuildingStatus GetBuildingStatus(BuildingInfo building)
{
    var production = BuildingSystem.GetBuildingProduction(building.RawBuilding);
    
    if (production.Efficiency < 0.1f)
    {
        if (building.PowerConsumption > GetAvailablePower())
            return BuildingStatus.PowerShortage;
        else if (production.OutputRate == 0f)
            return BuildingStatus.ResourceStarved;
        else
            return BuildingStatus.Maintenance;
    }
    else if (production.Efficiency < 0.7f)
    {
        return BuildingStatus.Damaged;
    }
    
    return BuildingStatus.Operational;
}
```

#### Performance Optimization

```csharp
void OptimizeBuildingPerformance()
{
    var allBuildings = BuildingSystem.GetAllBuildings();
    var problemBuildings = new List<BuildingInfo>();
    
    foreach (var building in allBuildings)
    {
        var status = GetBuildingStatus(building);
        
        switch (status)
        {
            case BuildingStatus.PowerShortage:
                Log.LogWarning($"⚡ {building.Type} at {building.Position} needs power");
                problemBuildings.Add(building);
                break;
                
            case BuildingStatus.ResourceStarved:
                Log.LogWarning($"📦 {building.Type} at {building.Position} needs resources");
                problemBuildings.Add(building);
                break;
                
            case BuildingStatus.Maintenance:
                Log.LogInfo($"🔧 {building.Type} at {building.Position} needs maintenance");
                break;
        }
    }
    
    Log.LogInfo($"🏗️ Buildings requiring attention: {problemBuildings.Count}/{allBuildings.Length}");
}
```

---

## Production Systems

### 🔄 Production Chains

#### Energy Production Chain

```csharp
public class EnergyProductionChain
{
    public void AnalyzeEnergyFlow()
    {
        // Generation
        var solarArrays = BuildingSystem.GetBuildingsByType("SolarArray");
        var reactors = BuildingSystem.GetBuildingsByType("NuclearReactor");
        var windTurbines = BuildingSystem.GetBuildingsByType("WindTurbine");
        
        float solarPower = CalculateTotalOutput(solarArrays);
        float nuclearPower = CalculateTotalOutput(reactors);  
        float windPower = CalculateTotalOutput(windTurbines);
        
        float totalGeneration = solarPower + nuclearPower + windPower;
        
        // Consumption
        var allBuildings = BuildingSystem.GetAllBuildings();
        float totalConsumption = allBuildings.Sum(b => b.PowerConsumption);
        
        // Storage
        var batteries = BuildingSystem.GetBuildingsByType("Battery");
        float storageCapacity = batteries.Length * 50f;  // Assuming 50 MW per battery
        
        Log.LogInfo("⚡ Energy Analysis:");
        Log.LogInfo($"   Solar: {solarPower:F1} MW ({solarPower/totalGeneration*100:F1}%)");
        Log.LogInfo($"   Nuclear: {nuclearPower:F1} MW ({nuclearPower/totalGeneration*100:F1}%)");
        Log.LogInfo($"   Wind: {windPower:F1} MW ({windPower/totalGeneration*100:F1}%)");
        Log.LogInfo($"   Total Generation: {totalGeneration:F1} MW");
        Log.LogInfo($"   Total Consumption: {totalConsumption:F1} MW");
        Log.LogInfo($"   Storage Capacity: {storageCapacity:F1} MW");
        
        // Recommendations
        if (totalConsumption > totalGeneration * 0.9f)
        {
            Log.LogWarning("⚠️ Energy capacity near limit - build more generators");
        }
        
        if (storageCapacity < totalGeneration * 0.5f)
        {
            Log.LogInfo("🔋 Consider more energy storage for night/storm backup");
        }
    }
    
    private float CalculateTotalOutput(BuildingInfo[] buildings)
    {
        return buildings.Sum(b => {
            var production = BuildingSystem.GetBuildingProduction(b.RawBuilding);
            return production.ActualOutput;
        });
    }
}
```

#### Atmospheric Processing Chain

```csharp
public class AtmosphericProcessingChain
{
    public void AnalyzeAtmosphericProduction()
    {
        // Input: Raw atmospheric gases and water
        var processors = BuildingSystem.GetBuildingsByType("AtmosphereProcessor");
        var vaporizers = BuildingSystem.GetBuildingsByType("WaterVaporizer");
        
        // Processing: Greenhouse gas production
        var greenhousePlants = BuildingSystem.GetBuildingsByType("GreenhouseGasPlant");
        var oxygenGenerators = BuildingSystem.GetBuildingsByType("OxygenGenerator");
        
        // Output: Atmospheric changes
        float co2Production = CalculateGasProduction(processors, "CO2");
        float waterVapor = CalculateGasProduction(vaporizers, "H2O");
        float greenhouseGases = CalculateGasProduction(greenhousePlants, "Super-GHG");
        float oxygenProduction = CalculateGasProduction(oxygenGenerators, "O2");
        
        Log.LogInfo("🌬️ Atmospheric Processing Chain:");
        Log.LogInfo($"   CO2 release: {co2Production:F6} atm/sol");
        Log.LogInfo($"   Water vapor: {waterVapor:F6} atm/sol");
        Log.LogInfo($"   Greenhouse gases: {greenhouseGases:F6} atm/sol");
        Log.LogInfo($"   Oxygen production: {oxygenProduction:F6} atm/sol");
        
        // Efficiency analysis
        AnalyzeChainEfficiency();
    }
    
    private void AnalyzeChainEfficiency()
    {
        var climate = ModSDK.Universe.GetClimateData();
        
        // Temperature efficiency (greenhouse effect)
        float temperatureTarget = 273f;  // 0°C target
        float temperatureDelta = Math.Max(0, temperatureTarget - climate.Temperature);
        
        if (temperatureDelta > 20f)
        {
            Log.LogInfo("🔥 Priority: More greenhouse gas production for warming");
        }
        
        // Pressure efficiency
        float pressureTarget = 0.5f;  // 0.5 atm target
        float pressureDelta = Math.Max(0, pressureTarget - climate.TotalPressure);
        
        if (pressureDelta > 0.1f)
        {
            Log.LogInfo("🌫️ Priority: More atmospheric processors for pressure");
        }
        
        // Oxygen efficiency
        float oxygenTarget = 0.16f * climate.TotalPressure;  // 16% oxygen
        float oxygenDelta = Math.Max(0, oxygenTarget - climate.O2Pressure);
        
        if (oxygenDelta > 0.01f)
        {
            Log.LogInfo("🫁 Priority: More oxygen generators for breathability");
        }
    }
}
```

### 📊 Resource Management

#### Resource Flow Monitoring

```csharp
public class ResourceManager
{
    public void MonitorResourceFlows()
    {
        var resources = new[] { "Water", "Iron", "Silicon", "Carbon", "Energy" };
        
        foreach (var resource in resources)
        {
            var production = GetResourceProduction(resource);
            var consumption = GetResourceConsumption(resource);
            var storage = GetResourceStorage(resource);
            
            float netFlow = production - consumption;
            float daysOfSupply = storage / Math.Max(consumption, 1f);
            
            Log.LogInfo($"📦 {resource}:");
            Log.LogInfo($"   Production: {production:F2}/sol");
            Log.LogInfo($"   Consumption: {consumption:F2}/sol");
            Log.LogInfo($"   Net: {netFlow:+F2;-F2}/sol");
            Log.LogInfo($"   Storage: {storage:F1} ({daysOfSupply:F1} sols supply)");
            
            // Warnings
            if (netFlow < 0)
            {
                Log.LogWarning($"⚠️ {resource} deficit: {-netFlow:F2}/sol");
            }
            
            if (daysOfSupply < 10f)
            {
                Log.LogWarning($"⚠️ Low {resource} reserves: {daysOfSupply:F1} sols remaining");
            }
        }
    }
    
    private float GetResourceProduction(string resourceType)
    {
        var buildings = BuildingSystem.GetAllBuildings();
        return buildings
            .Where(b => ProducesResource(b, resourceType))
            .Sum(b => GetResourceOutput(b, resourceType));
    }
    
    private float GetResourceConsumption(string resourceType)
    {
        var buildings = BuildingSystem.GetAllBuildings();
        return buildings
            .Where(b => ConsumesResource(b, resourceType))
            .Sum(b => GetResourceInput(b, resourceType));
    }
}
```

---

## Infrastructure Planning

### 🗺️ Strategic Placement

#### Location Analysis

```csharp
public class BuildingPlacementOptimizer
{
    public Vector3 FindOptimalLocation(string buildingType, Dictionary<string, float> priorities)
    {
        var candidates = GenerateLocationCandidates();
        var scored = new List<(Vector3 location, float score)>();
        
        foreach (var location in candidates)
        {
            float score = EvaluateLocation(location, buildingType, priorities);
            scored.Add((location, score));
        }
        
        var optimal = scored.OrderByDescending(s => s.score).First();
        
        Log.LogInfo($"📍 Optimal location for {buildingType}:");
        Log.LogInfo($"   Position: {optimal.location}");
        Log.LogInfo($"   Score: {optimal.score:F2}");
        
        return optimal.location;
    }
    
    private float EvaluateLocation(Vector3 location, string buildingType, Dictionary<string, float> priorities)
    {
        float score = 0f;
        
        // Distance to other buildings (logistics)
        if (priorities.ContainsKey("proximity"))
        {
            var nearby = GetNearbyBuildings(location, 10f);
            score += nearby.Count * priorities["proximity"];
        }
        
        // Resource availability
        if (priorities.ContainsKey("resources"))
        {
            var resourceDensity = GetResourceDensity(location);
            score += resourceDensity * priorities["resources"];
        }
        
        // Climate factors
        if (priorities.ContainsKey("temperature"))
        {
            var localTemperature = GetLocalTemperature(location);
            score += EvaluateTemperatureForBuilding(buildingType, localTemperature) * priorities["temperature"];
        }
        
        // Power grid accessibility
        if (priorities.ContainsKey("power"))
        {
            var powerAccess = GetPowerGridAccess(location);
            score += powerAccess * priorities["power"];
        }
        
        return score;
    }
}
```

#### Infrastructure Layout Patterns

**Hub and Spoke Model:**
```csharp
public class HubSpokeLayout
{
    public void CreateEnergyHub(Vector3 centerLocation)
    {
        // Central power generation cluster
        var hubBuildings = new[]
        {
            ("NuclearReactor", Vector3.zero),
            ("Battery", new Vector3(5, 0, 0)),
            ("Battery", new Vector3(-5, 0, 0)),
            ("PowerRelay", new Vector3(0, 0, 10))
        };
        
        foreach (var (type, offset) in hubBuildings)
        {
            var position = centerLocation + offset;
            Log.LogInfo($"🏭 Place {type} at hub: {position}");
        }
        
        // Spoke connections to outlying facilities
        var spokeDirections = new[]
        {
            Vector3.forward * 30,     // North spoke
            Vector3.right * 30,       // East spoke  
            Vector3.back * 30,        // South spoke
            Vector3.left * 30         // West spoke
        };
        
        foreach (var direction in spokeDirections)
        {
            var spokeLocation = centerLocation + direction;
            CreateProductionSpoke(spokeLocation, direction.normalized);
        }
    }
    
    private void CreateProductionSpoke(Vector3 location, Vector3 direction)
    {
        // Specialized production facilities along spoke
        var spokeBuildings = new[]
        {
            ("WaterExtractor", location),
            ("AtmosphereProcessor", location + direction * 10),
            ("WaterVaporizer", location + direction * 20)
        };
        
        foreach (var (type, pos) in spokeBuildings)
        {
            Log.LogInfo($"🔧 Place {type} on spoke: {pos}");
        }
    }
}
```

**Grid Layout Model:**
```csharp
public class GridLayout
{
    public void CreateIndustrialGrid(Vector3 startLocation, int gridSize = 5)
    {
        for (int x = 0; x < gridSize; x++)
        {
            for (int z = 0; z < gridSize; z++)
            {
                var position = startLocation + new Vector3(x * 15, 0, z * 15);
                var buildingType = DetermineBuildingForGridPosition(x, z, gridSize);
                
                Log.LogInfo($"🏗️ Grid position ({x},{z}): {buildingType} at {position}");
            }
        }
    }
    
    private string DetermineBuildingForGridPosition(int x, int z, int gridSize)
    {
        // Center: Power generation
        if (x == gridSize / 2 && z == gridSize / 2)
            return "NuclearReactor";
        
        // Edges: Resource extraction  
        if (x == 0 || x == gridSize - 1 || z == 0 || z == gridSize - 1)
            return "WaterExtractor";
        
        // Inner ring: Processing
        if (Math.Abs(x - gridSize / 2) == 1 || Math.Abs(z - gridSize / 2) == 1)
            return "AtmosphereProcessor";
        
        // Default: Support buildings
        return "Battery";
    }
}
```

---

## Building Modifications

### ⚙️ Performance Overrides

#### Building Efficiency Modifications

```csharp
public class BuildingModifications
{
    public void ApplyEfficiencyUpgrades()
    {
        // Solar panel efficiency based on atmospheric clarity
        var climate = ModSDK.Universe.GetClimateData();
        float atmosphericClarity = 1f - Math.Min(climate.TotalPressure * 0.1f, 0.3f);
        float solarEfficiency = 1f + (atmosphericClarity * 0.5f);
        
        OverrideSystem.RegisterOverride(
            "buildings.solar.efficiency",
            solarEfficiency,
            $"Solar efficiency adjusted for atmospheric conditions: {solarEfficiency:F2}x"
        );
        
        // Nuclear reactor safety and efficiency based on cooling
        float nuclearEfficiency = CalculateNuclearEfficiency();
        OverrideSystem.RegisterOverride(
            "buildings.nuclear.efficiency",
            nuclearEfficiency,
            $"Nuclear efficiency with enhanced cooling: {nuclearEfficiency:F2}x"
        );
        
        // Water extractor efficiency based on location
        ApplyLocationBasedWaterEfficiency();
        
        Log.LogInfo("🔧 Building efficiency overrides applied");
    }
    
    private void ApplyLocationBasedWaterEfficiency()
    {
        var waterExtractors = BuildingSystem.GetBuildingsByType("WaterExtractor");
        
        foreach (var extractor in waterExtractors)
        {
            float efficiency = CalculateWaterExtractionEfficiency(extractor.Position);
            string key = $"buildings.water_extractor.{extractor.Id}.efficiency";
            
            OverrideSystem.RegisterOverride(
                key,
                efficiency,
                $"Water extractor efficiency at {extractor.Position}: {efficiency:F2}x"
            );
        }
    }
    
    private float CalculateWaterExtractionEfficiency(Vector3 position)
    {
        // Higher efficiency near polar regions (more ice)
        float distanceFromPole = Math.Abs(position.z);  // Assuming z is latitude
        float polarBonus = Math.Max(0, (50f - distanceFromPole) / 50f) * 0.5f;
        
        // Higher efficiency near known water deposits
        float depositBonus = GetNearbyWaterDeposits(position) * 0.3f;
        
        return 1f + polarBonus + depositBonus;
    }
}
```

#### Production Rate Scaling

```csharp
public class DynamicProductionScaling
{
    public void ScaleProductionBasedOnGameProgress()
    {
        int currentSol = ModSDK.Universe.GetCurrentSol();
        var climate = ModSDK.Universe.GetClimateData();
        
        // Early game: Boost basic infrastructure
        if (currentSol < 50)
        {
            OverrideSystem.RegisterOverride("buildings.solar.output", 1.3f, "Early game solar boost");
            OverrideSystem.RegisterOverride("buildings.water_extractor.rate", 1.2f, "Early game water boost");
        }
        
        // Mid game: Focus on atmospheric processing
        else if (currentSol < 150)
        {
            OverrideSystem.RegisterOverride("buildings.atmosphere_processor.rate", 1.4f, "Mid game atmospheric boost");
            OverrideSystem.RegisterOverride("buildings.greenhouse_gas.efficiency", 1.3f, "Mid game warming boost");
        }
        
        // Late game: Fine-tune for habitability
        else
        {
            ApplyLateGameOptimizations(climate);
        }
        
        Log.LogInfo($"🚀 Production scaling applied for Sol {currentSol}");
    }
    
    private void ApplyLateGameOptimizations(PlanetClimateData climate)
    {
        // Focus on whatever is limiting habitability
        float oxygenRatio = climate.O2Pressure / climate.TotalPressure;
        float co2Ratio = climate.CO2Pressure / climate.TotalPressure;
        
        if (oxygenRatio < 0.16f)
        {
            // Need more oxygen
            OverrideSystem.RegisterOverride("buildings.oxygen_generator.rate", 1.5f, "Late game oxygen focus");
            OverrideSystem.RegisterOverride("buildings.algae_farm.efficiency", 1.3f, "Biological oxygen boost");
        }
        else if (co2Ratio > 0.04f)
        {
            // Need less CO2
            OverrideSystem.RegisterOverride("buildings.carbon_scrubber.efficiency", 1.4f, "CO2 reduction focus");
        }
        else if (climate.TotalPressure < 0.5f)
        {
            // Need more pressure
            OverrideSystem.RegisterOverride("buildings.water_vaporizer.rate", 1.3f, "Pressure increase focus");
        }
    }
}
```

---

## Automation Patterns

### 🤖 Smart Building Management

#### Adaptive Resource Allocation

```csharp
public class SmartBuildingManager
{
    private Dictionary<string, float> resourcePriorities;
    
    public void UpdateResourceAllocation()
    {
        AnalyzeCurrentNeeds();
        RedistributePower();
        OptimizeProduction();
        
        Log.LogInfo("🤖 Smart building management cycle completed");
    }
    
    private void AnalyzeCurrentNeeds()
    {
        var climate = ModSDK.Universe.GetClimateData();
        int currentSol = ModSDK.Universe.GetCurrentSol();
        
        // Reset priorities
        resourcePriorities = new Dictionary<string, float>
        {
            ["Energy"] = 1.0f,
            ["Water"] = 1.0f,
            ["AtmosphericPressure"] = 1.0f,
            ["Oxygen"] = 1.0f,
            ["Temperature"] = 1.0f
        };
        
        // Adjust based on current state
        if (climate.Temperature < 250f)
        {
            resourcePriorities["Temperature"] = 2.0f;  // Critical: too cold
        }
        
        if (climate.TotalPressure < 0.1f)
        {
            resourcePriorities["AtmosphericPressure"] = 1.8f;  // High priority
        }
        
        float oxygenRatio = climate.O2Pressure / climate.TotalPressure;
        if (oxygenRatio < 0.12f)
        {
            resourcePriorities["Oxygen"] = 1.6f;  // Moderately high priority
        }
        
        // Late game: Balance all aspects
        if (currentSol > 200)
        {
            BalancePrioritiesForHabitability();
        }
        
        LogPriorities();
    }
    
    private void RedistributePower()
    {
        var allBuildings = BuildingSystem.GetAllBuildings();
        var powerBuildings = allBuildings.Where(b => b.PowerConsumption > 0).ToList();
        
        // Sort by priority based on building type and current needs
        powerBuildings.Sort((a, b) => {
            float priorityA = GetBuildingPriority(a);
            float priorityB = GetBuildingPriority(b);
            return priorityB.CompareTo(priorityA);  // Descending order
        });
        
        // Adjust power allocation (simplified simulation)
        float totalAvailablePower = GetTotalPowerGeneration();
        float allocatedPower = 0f;
        
        foreach (var building in powerBuildings)
        {
            float requestedPower = building.PowerConsumption;
            float availablePower = totalAvailablePower - allocatedPower;
            float allocatedToBuiding = Math.Min(requestedPower, availablePower);
            
            allocatedPower += allocatedToBuiding;
            
            float powerRatio = allocatedToBuiding / requestedPower;
            string key = $"buildings.{building.Type}.{building.Id}.power_allocation";
            
            OverrideSystem.RegisterOverride(key, powerRatio, $"Smart power allocation: {powerRatio:F2}x");
            
            if (powerRatio < 0.9f)
            {
                Log.LogWarning($"⚡ {building.Type} at {building.Position} receiving reduced power: {powerRatio*100:F0}%");
            }
        }
    }
    
    private float GetBuildingPriority(BuildingInfo building)
    {
        // Base priority by building function
        var basePriorities = new Dictionary<string, float>
        {
            ["NuclearReactor"] = 10f,        // Always highest priority
            ["WaterExtractor"] = 8f,         // Essential for life
            ["OxygenGenerator"] = 7f,        // Critical for breathing
            ["AtmosphereProcessor"] = 6f,    // Important for terraforming
            ["SolarArray"] = 5f,             // Power generation
            ["GreenhouseGasPlant"] = 4f,     // Climate modification
            ["Battery"] = 3f,                // Energy storage
            ["WaterVaporizer"] = 2f          // Supplemental atmospheric
        };
        
        float basePriority = basePriorities.GetValueOrDefault(building.Type, 1f);
        
        // Modify by current resource priorities
        if (building.Type == "GreenhouseGasPlant" && resourcePriorities["Temperature"] > 1.5f)
        {
            basePriority *= 2f;  // Boost greenhouse gas production when heating needed
        }
        
        if (building.Type == "OxygenGenerator" && resourcePriorities["Oxygen"] > 1.5f)
        {
            basePriority *= 1.8f;  // Boost oxygen when needed
        }
        
        return basePriority;
    }
}
```

#### Predictive Maintenance

```csharp
public class PredictiveMaintenanceSystem
{
    private Dictionary<string, DateTime> lastMaintenanceCheck;
    
    public void ScheduleMaintenance()
    {
        var allBuildings = BuildingSystem.GetAllBuildings();
        var maintenanceQueue = new List<(BuildingInfo building, float urgency)>();
        
        foreach (var building in allBuildings)
        {
            float urgency = CalculateMaintenanceUrgency(building);
            
            if (urgency > 0.3f)  // 30% urgency threshold
            {
                maintenanceQueue.Add((building, urgency));
            }
        }
        
        // Sort by urgency (highest first)
        maintenanceQueue.Sort((a, b) => b.urgency.CompareTo(a.urgency));
        
        Log.LogInfo($"🔧 Maintenance Required: {maintenanceQueue.Count} buildings");
        
        foreach (var (building, urgency) in maintenanceQueue.Take(5))  // Top 5 most urgent
        {
            Log.LogInfo($"   {building.Type} at {building.Position}: {urgency*100:F0}% urgency");
        }
    }
    
    private float CalculateMaintenanceUrgency(BuildingInfo building)
    {
        float urgency = 0f;
        
        // Efficiency degradation
        var production = BuildingSystem.GetBuildingProduction(building.RawBuilding);
        if (production.Efficiency < 0.8f)
        {
            urgency += (0.8f - production.Efficiency) * 2f;  // 0-40% urgency from efficiency
        }
        
        // Age-based maintenance (simplified)
        int buildingSol = GetBuildingSol(building);  // When building was constructed
        int currentSol = ModSDK.Universe.GetCurrentSol();
        int age = currentSol - buildingSol;
        
        if (age > 50)  // Buildings need maintenance after 50 sols
        {
            urgency += Math.Min((age - 50) * 0.01f, 0.4f);  // 0-40% urgency from age
        }
        
        // Critical building bonus
        if (IsCriticalBuilding(building.Type))
        {
            urgency *= 1.5f;  // 50% higher urgency for critical infrastructure
        }
        
        return Math.Min(urgency, 1f);  // Cap at 100%
    }
    
    private bool IsCriticalBuilding(string buildingType)
    {
        var criticalTypes = new[] 
        {
            "NuclearReactor", "WaterExtractor", "OxygenGenerator", "PowerRelay"
        };
        
        return criticalTypes.Contains(buildingType);
    }
}
```

---

## Performance Optimization

### ⚡ Efficient Building Operations

#### Batch Processing

```csharp
public class EfficientBuildingMonitor
{
    private DateTime lastFullUpdate = DateTime.MinValue;
    private HashSet<string> trackedBuildings = new HashSet<string>();
    
    public void OptimizedUpdate()
    {
        // Full system scan only every 5 sols
        if ((DateTime.Now - lastFullUpdate).TotalMinutes > 5)  // Simulate 5 sol interval
        {
            PerformFullSystemScan();
            lastFullUpdate = DateTime.Now;
        }
        else
        {
            // Quick updates for critical buildings only
            PerformCriticalBuildingCheck();
        }
    }
    
    private void PerformFullSystemScan()
    {
        Log.LogInfo("🔍 Performing full building system scan...");
        
        var allBuildings = BuildingSystem.GetAllBuildings();
        
        // Batch process buildings by type for efficiency
        var buildingsByType = allBuildings.GroupBy(b => b.Type);
        
        foreach (var typeGroup in buildingsByType)
        {
            ProcessBuildingGroup(typeGroup.Key, typeGroup.ToArray());
        }
        
        Log.LogInfo($"✅ Full scan completed: {allBuildings.Length} buildings processed");
    }
    
    private void ProcessBuildingGroup(string buildingType, BuildingInfo[] buildings)
    {
        switch (buildingType)
        {
            case "SolarArray":
                ProcessSolarArrays(buildings);
                break;
                
            case "WaterExtractor":
                ProcessWaterExtractors(buildings);
                break;
                
            case "AtmosphereProcessor":
                ProcessAtmosphereProcessors(buildings);
                break;
                
            default:
                ProcessGenericBuildings(buildings);
                break;
        }
    }
    
    private void ProcessSolarArrays(BuildingInfo[] solarArrays)
    {
        // Batch calculate solar efficiency based on atmospheric conditions
        var climate = ModSDK.Universe.GetClimateData();
        float baseEfficiency = CalculateSolarEfficiency(climate);
        
        float totalCapacity = 0f;
        float totalOutput = 0f;
        
        foreach (var array in solarArrays)
        {
            var production = BuildingSystem.GetBuildingProduction(array.RawBuilding);
            totalCapacity += production.OutputRate;
            totalOutput += production.ActualOutput;
        }
        
        Log.LogInfo($"☀️ Solar Arrays: {solarArrays.Length} units, {totalOutput:F1}/{totalCapacity:F1} MW ({totalOutput/totalCapacity*100:F1}%)");
    }
}
```

#### Caching and Lazy Loading

```csharp
public class CachedBuildingSystem
{
    private static readonly TimeSpan CacheTimeout = TimeSpan.FromSeconds(30);
    
    private Dictionary<string, (DateTime timestamp, object data)> cache 
        = new Dictionary<string, (DateTime, object)>();
    
    public BuildingInfo[] GetCachedBuildingsByType(string buildingType)
    {
        string cacheKey = $"buildings_type_{buildingType}";
        
        if (cache.TryGetValue(cacheKey, out var cached))
        {
            if (DateTime.Now - cached.timestamp < CacheTimeout)
            {
                return (BuildingInfo[])cached.data;
            }
        }
        
        // Cache miss or expired - fetch fresh data
        var buildings = BuildingSystem.GetBuildingsByType(buildingType);
        cache[cacheKey] = (DateTime.Now, buildings);
        
        return buildings;
    }
    
    public PlanetClimateData GetCachedClimateData()
    {
        string cacheKey = "climate_data";
        
        if (cache.TryGetValue(cacheKey, out var cached))
        {
            if (DateTime.Now - cached.timestamp < TimeSpan.FromSeconds(5))  // Shorter cache for climate
            {
                return (PlanetClimateData)cached.data;
            }
        }
        
        var climate = ModSDK.Universe.GetClimateData();
        cache[cacheKey] = (DateTime.Now, climate);
        
        return climate;
    }
    
    public void ClearExpiredCache()
    {
        var expiredKeys = cache
            .Where(kvp => DateTime.Now - kvp.Value.timestamp > CacheTimeout)
            .Select(kvp => kvp.Key)
            .ToList();
            
        foreach (var key in expiredKeys)
        {
            cache.Remove(key);
        }
        
        if (expiredKeys.Count > 0)
        {
            Log.LogInfo($"🗑️ Cleared {expiredKeys.Count} expired cache entries");
        }
    }
}
```

---

## Next Steps

### 🎓 Continue Learning

1. **[Climate System Guide](Climate-System-Guide)** - Atmospheric modification strategies
2. **[Performance Optimization](Performance-Guide)** - Efficient code patterns
3. **[Advanced Patterns](Advanced-Patterns)** - Expert modding techniques

### 🚀 Ready to Build?

Start with our [First Mod Tutorial](First-Mod-Tutorial) to create your own building enhancement mod, or explore [Code Examples](Code-Examples) for specific building management patterns!

---

**Build the infrastructure that will transform Mars into humanity's second home!** 🏗️🔴➡️🌍