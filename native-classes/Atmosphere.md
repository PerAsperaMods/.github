# 🌫️ Atmosphere Class Reference

The `Atmosphere` wrapper provides comprehensive access to Mars' atmospheric system, including gas composition, temperature, pressure, and terraforming effects. This is your primary interface for climate monitoring and modification.

## 📖 Overview

```csharp
using PerAspera.GameAPI.Wrappers;

// Access via Planet
var planet = SDK.Game.CurrentPlanet;
var atmosphere = planet.Atmosphere;

// Read climate data
float tempK = atmosphere.Temperature;
float tempC = tempK - 273.15f;
float pressure = atmosphere.Pressure;

Logger.LogInfo($"Temperature: {tempC:F1}°C");
Logger.LogInfo($"Pressure: {pressure:F0} Pa");
```

---

## 🌡️ Core Climate Properties

### Temperature
```csharp
public float Temperature { get; }
```

**Description:** Current atmospheric temperature in Kelvin.

**Usage:**
```csharp
float tempK = atmosphere.Temperature;
float tempC = tempK - 273.15f;  // Convert to Celsius

Logger.LogInfo($"Temperature: {tempC:F1}°C / {tempK:F1}K");

// Check terraforming milestones
if (tempC > 0)
{
    Logger.LogInfo("✅ Water can exist in liquid form!");
}

if (tempC > 15)
{
    Logger.LogInfo("✅ Comfortable temperature for habitation!");
}
```

**Reference Values:**
- `-60°C` (213K) - Typical Martian surface
- `0°C` (273K) - Water freezing point
- `15°C` (288K) - Earth-like temperature
- `25°C` (298K) - Warm habitable

---

### Pressure
```csharp
public float Pressure { get; }
```

**Description:** Total atmospheric pressure in Pascals (Pa).

**Usage:**
```csharp
float pressure = atmosphere.Pressure;
float kPa = pressure / 1000f;
float atm = pressure / 101325f;  // Earth atmospheres

Logger.LogInfo($"Pressure: {kPa:F2} kPa ({atm:F3} atm)");

// Check terraforming progress
if (pressure > 50000)
{
    Logger.LogInfo("✅ Pressure increasing!");
}

if (pressure > 101325)
{
    Logger.LogInfo("✅ Earth-like pressure achieved!");
}
```

**Reference Values:**
- `600 Pa` - Current Mars (very thin)
- `50,000 Pa` - Early terraforming  
- `101,325 Pa` (1 atm) - Earth sea level
- `150,000 Pa` - Thick breathable atmosphere

---

## 💨 Gas Composition

### Composition
```csharp
public AtmosphericComposition Composition { get; }
```

**Description:** Access to all atmospheric gases with partial pressures and percentages.

**Usage:**
```csharp
var composition = atmosphere.Composition;

// Access specific gases
var co2 = composition["CO2"];
var o2 = composition["O2"];
var n2 = composition["N2"];
var h2o = composition["H2O"];

if (co2 != null)
{
    Logger.LogInfo($"CO2: {co2.Percentage:F1}%");
    Logger.LogInfo($"  Partial Pressure: {co2.PartialPressure:F2} kPa");
}

if (o2 != null)
{
    Logger.LogInfo($"O2: {o2.Percentage:F1}%");
    
    if (o2.Percentage > 20f)
    {
        Logger.LogInfo("✅ Breathable oxygen levels!");
    }
}
```

---

### Individual Gas Access

#### CO2 (Carbon Dioxide)
```csharp
var co2 = composition["CO2"];
```

**Properties:**
- `PartialPressure` - Pressure contribution (kPa)
- `Percentage` - Percentage of total atmosphere
- `IsReadOnly` - Can this gas be modified?

**Usage:**
```csharp
var co2 = atmosphere.Composition["CO2"];
if (co2 != null)
{
    float percentage = co2.Percentage;
    float pressure = co2.PartialPressure;
    
    Logger.LogInfo($"CO2: {percentage:F2}% ({pressure:F2} kPa)");
    
    // Modify if not read-only
    if (!co2.IsReadOnly)
    {
        co2.PartialPressure = 50f;  // Increase CO2
    }
}
```

---

#### O2 (Oxygen)
```csharp
var o2 = composition["O2"];
```

**Usage:**
```csharp
var o2 = atmosphere.Composition["O2"];
if (o2 != null)
{
    Logger.LogInfo($"Oxygen: {o2.Percentage:F2}%");
    
    // Check breathability
    if (o2.Percentage >= 19.5f && o2.Percentage <= 23.5f)
    {
        Logger.LogInfo("✅ Safe oxygen levels for humans!");
    }
    else if (o2.Percentage < 19.5f)
    {
        Logger.LogWarning("⚠️ Insufficient oxygen");
    }
    else
    {
        Logger.LogWarning("⚠️ Oxygen toxicity risk");
    }
}
```

---

#### N2 (Nitrogen)
```csharp
var n2 = composition["N2"];
```

**Usage:**
```csharp
var n2 = atmosphere.Composition["N2"];
if (n2 != null)
{
    Logger.LogInfo($"Nitrogen: {n2.Percentage:F2}%");
    
    // Earth-like atmosphere is ~78% N2
    if (n2.Percentage > 75f && n2.Percentage < 80f)
    {
        Logger.LogInfo("✅ Earth-like nitrogen levels!");
    }
}
```

---

#### H2O (Water Vapor)
```csharp
var h2o = composition["H2O"];
```

**Usage:**
```csharp
var h2o = atmosphere.Composition["H2O"];
if (h2o != null)
{
    Logger.LogInfo($"Water Vapor: {h2o.Percentage:F2}%");
    
    // Humidity indication
    if (h2o.Percentage > 1f)
    {
        Logger.LogInfo("✅ Humid atmosphere forming!");
    }
}
```

---

### AllGases
```csharp
public IEnumerable<AtmosphericGas> AllGases { get; }
```

**Description:** Iterate through all atmospheric gases.

**Usage:**
```csharp
Logger.LogInfo("Atmospheric Composition:");
foreach (var gas in atmosphere.Composition.AllGases)
{
    Logger.LogInfo($"  {gas.Name} ({gas.Symbol}):");
    Logger.LogInfo($"    {gas.Percentage:F2}%");
    Logger.LogInfo($"    {gas.PartialPressure:F2} kPa");
}
```

---

## 🔥 Terraforming Effects

### TerraformingEffects
```csharp
public IEnumerable<TerraformingEffect> TerraformingEffects { get; }
```

**Description:** Active terraforming modifications affecting climate.

**Usage:**
```csharp
Logger.LogInfo("Active Terraforming Effects:");

foreach (var effect in atmosphere.TerraformingEffects)
{
    float tempChange = effect.TemperatureEffect;
    
    Logger.LogInfo($"  {effect.Name}:");
    Logger.LogInfo($"    Description: {effect.Description}");
    Logger.LogInfo($"    Temperature Impact: {tempChange:+0.00;-0.00;0}K");
}

// Calculate total impact
float totalImpact = atmosphere.TerraformingEffects
    .Sum(e => e.TemperatureEffect);

Logger.LogInfo($"Total Climate Impact: {totalImpact:+0.00;-0.00;0}K");
```

**Common Effects:**
- Greenhouse gases
- Polar ice melting
- Magnetic field generation
- Solar mirrors/shades

---

## 💡 Practical Examples

### Complete Climate Dashboard

```csharp
[BepInPlugin("com.example.climate", "Climate Dashboard", "1.0.0")]
public class ClimateDashboard : PerAsperaSDKPlugin
{
    protected override void OnSDKReady()
    {
        SDK.Events.ClimateAnalysisComplete += DisplayClimate;
    }
    
    private void DisplayClimate(ClimateEventData data)
    {
        var planet = SDK.Game.CurrentPlanet;
        if (planet == null) return;
        
        var atmosphere = planet.Atmosphere;
        
        Logger.LogInfo("═══════════════════════════════════");
        Logger.LogInfo("      MARS CLIMATE DASHBOARD");
        Logger.LogInfo("═══════════════════════════════════");
        
        // Temperature
        float tempC = atmosphere.Temperature - 273.15f;
        Logger.LogInfo($"🌡️  Temperature: {tempC:F1}°C ({atmosphere.Temperature:F1}K)");
        
        // Pressure
        float kPa = atmosphere.Pressure / 1000f;
        float atm = atmosphere.Pressure / 101325f;
        Logger.LogInfo($"🌫️  Pressure: {kPa:F2} kPa ({atm:F3} atm)");
        
        // Gas composition
        Logger.LogInfo("💨 Atmospheric Composition:");
        foreach (var gas in atmosphere.Composition.AllGases)
        {
            string icon = gas.Symbol switch
            {
                "CO2" => "🔴",
                "O2" => "🔵",
                "N2" => "⚪",
                "H2O" => "💧",
                _ => "⚫"
            };
            Logger.LogInfo($"   {icon} {gas.Symbol}: {gas.Percentage:F2}%");
        }
        
        // Terraforming effects
        if (atmosphere.TerraformingEffects.Any())
        {
            Logger.LogInfo("🔥 Active Effects:");
            foreach (var effect in atmosphere.TerraformingEffects)
            {
                Logger.LogInfo($"   • {effect.Name}: {effect.TemperatureEffect:+0.0;-0.0}K");
            }
        }
        
        // Milestones
        CheckMilestones(tempC, atmosphere.Pressure, data.OxygenLevel);
        
        Logger.LogInfo("═══════════════════════════════════");
    }
    
    private void CheckMilestones(float tempC, float pressure, float oxygen)
    {
        List<string> achieved = new();
        
        if (tempC > 0) achieved.Add("✅ Liquid water possible");
        if (tempC > 15) achieved.Add("✅ Comfortable temperature");
        if (pressure > 50000) achieved.Add("✅ Pressure building");
        if (pressure > 101325) achieved.Add("✅ Earth-like pressure");
        if (oxygen > 15) achieved.Add("✅ Oxygen rising");
        if (oxygen > 20) achieved.Add("✅ Breathable atmosphere");
        
        if (achieved.Any())
        {
            Logger.LogInfo("🎯 Milestones:");
            foreach (var milestone in achieved)
            {
                Logger.LogInfo($"   {milestone}");
            }
        }
    }
}
```

### Atmospheric Hazard Monitor

```csharp
[BepInPlugin("com.example.hazards", "Atmospheric Hazards", "1.0.0")]
public class AtmosphericHazards : PerAsperaSDKPlugin
{
    protected override void OnSDKReady()
    {
        SDK.Events.ClimateAnalysisComplete += CheckHazards;
    }
    
    private void CheckHazards(ClimateEventData data)
    {
        var atmosphere = SDK.Game.CurrentPlanet?.Atmosphere;
        if (atmosphere == null) return;
        
        float tempC = atmosphere.Temperature - 273.15f;
        float pressure = atmosphere.Pressure;
        
        // Temperature hazards
        if (tempC < -80)
        {
            Logger.LogWarning($"🥶 EXTREME COLD: {tempC:F0}°C");
        }
        else if (tempC > 50)
        {
            Logger.LogWarning($"🔥 EXTREME HEAT: {tempC:F0}°C");
        }
        
        // Pressure hazards
        if (pressure < 1000)
        {
            Logger.LogWarning($"⚠️ VACUUM CONDITIONS: {pressure:F0} Pa");
        }
        else if (pressure > 200000)
        {
            Logger.LogWarning($"⚠️ HIGH PRESSURE: {pressure:F0} Pa");
        }
        
        // Toxic gas levels
        var co2 = atmosphere.Composition["CO2"];
        if (co2 != null && co2.Percentage > 5f)
        {
            Logger.LogWarning($"☠️ TOXIC CO2 LEVELS: {co2.Percentage:F1}%");
        }
        
        var o2 = atmosphere.Composition["O2"];
        if (o2 != null && o2.Percentage > 30f)
        {
            Logger.LogWarning($"⚠️ OXYGEN TOXICITY RISK: {o2.Percentage:F1}%");
        }
    }
}
```

### Terraforming Progress Tracker

```csharp
[BepInPlugin("com.example.terraform", "Terraform Tracker", "1.0.0")]
public class TerraformTracker : PerAsperaSDKPlugin
{
    private float _initialTemp;
    private float _initialPressure;
    
    protected override void OnSDKReady()
    {
        SDK.Events.GameFullyLoaded += () =>
        {
            var atmosphere = SDK.Game.CurrentPlanet?.Atmosphere;
            if (atmosphere != null)
            {
                _initialTemp = atmosphere.Temperature;
                _initialPressure = atmosphere.Pressure;
                
                Logger.LogInfo($"Baseline Climate:");
                Logger.LogInfo($"  Temperature: {_initialTemp:F1}K");
                Logger.LogInfo($"  Pressure: {_initialPressure:F0} Pa");
            }
        };
        
        SDK.Events.MartianDayChanged += TrackProgress;
    }
    
    private void TrackProgress(MartianDayEventData data)
    {
        if (data.TotalDays % 100 != 0) return;  // Every 100 sols
        
        var atmosphere = SDK.Game.CurrentPlanet?.Atmosphere;
        if (atmosphere == null) return;
        
        float tempChange = atmosphere.Temperature - _initialTemp;
        float pressureChange = atmosphere.Pressure - _initialPressure;
        float pressurePercent = (pressureChange / _initialPressure) * 100f;
        
        Logger.LogInfo($"═══════════════════════════════════");
        Logger.LogInfo($"  Terraforming Progress (Sol {data.TotalDays})");
        Logger.LogInfo($"═══════════════════════════════════");
        Logger.LogInfo($"Temperature: {tempChange:+0.0;-0.0}K change");
        Logger.LogInfo($"Pressure: {pressureChange:+0;-0} Pa ({pressurePercent:+0.0;-0.0}%)");
        
        // Rate of change
        float tempRate = tempChange / data.TotalDays;
        float pressureRate = pressureChange / data.TotalDays;
        
        Logger.LogInfo($"Rate:");
        Logger.LogInfo($"  {tempRate:+0.000;-0.000}K per sol");
        Logger.LogInfo($"  {pressureRate:+0.0;-0.0} Pa per sol");
        Logger.LogInfo($"═══════════════════════════════════");
    }
}
```

### Gas Balance Optimizer

```csharp
SDK.Events.ClimateAnalysisComplete += (data) =>
{
    var atmosphere = SDK.Game.CurrentPlanet?.Atmosphere;
    if (atmosphere == null) return;
    
    var composition = atmosphere.Composition;
    
    // Check atmospheric balance
    var co2 = composition["CO2"];
    var o2 = composition["O2"];
    var n2 = composition["N2"];
    
    if (co2 != null && o2 != null && n2 != null)
    {
        // Target: Earth-like atmosphere
        // N2: ~78%, O2: ~21%, CO2: ~0.04%
        
        bool balanced = 
            n2.Percentage > 75f && n2.Percentage < 80f &&
            o2.Percentage > 19f && o2.Percentage < 23f &&
            co2.Percentage < 1f;
        
        if (balanced)
        {
            Logger.LogInfo("✅ ACHIEVEMENT: Earth-like atmospheric composition!");
        }
        else
        {
            Logger.LogInfo("Atmospheric Balance:");
            Logger.LogInfo($"  N2: {n2.Percentage:F1}% (target: 78%)");
            Logger.LogInfo($"  O2: {o2.Percentage:F1}% (target: 21%)");
            Logger.LogInfo($"  CO2: {co2.Percentage:F1}% (target: <1%)");
        }
    }
};
```

---

## ⚠️ Important Notes

### Temperature Units

**Always in Kelvin, convert for display:**
```csharp
float tempK = atmosphere.Temperature;
float tempC = tempK - 273.15f;
float tempF = (tempC * 9f/5f) + 32f;

Logger.LogInfo($"{tempC:F1}°C / {tempF:F1}°F / {tempK:F1}K");
```

### Pressure Units

**Pascals (Pa) by default:**
```csharp
float pa = atmosphere.Pressure;
float kPa = pa / 1000f;
float atm = pa / 101325f;
float bar = pa / 100000f;
```

### Gas Modification

**Check IsReadOnly before modifying:**
```csharp
var gas = atmosphere.Composition["O2"];
if (gas != null && !gas.IsReadOnly)
{
    gas.PartialPressure = 21f;  // Safe
}
else
{
    Logger.LogWarning("Cannot modify this gas");
}
```

### Null Safety

**Always validate atmosphere access:**
```csharp
var planet = SDK.Game.CurrentPlanet;
if (planet == null) return;

var atmosphere = planet.Atmosphere;
// Atmosphere is never null if planet exists, but gas access can be
var co2 = atmosphere.Composition["CO2"];
if (co2 == null)
{
    Logger.LogWarning("CO2 data not available");
    return;
}
```

---

## 🔗 Related Classes

- **[Planet](Planet.md)** - Planet-level climate access
- **[Universe](Universe.md)** - Game-wide time management
- **[Building](Building.md)** - Infrastructure climate impact

---

## 📚 Related Documentation

- **[Climate Events](../sdk/Events.md#climate-events)** - Atmospheric event handling
- **[Climate API Guide](../sdk/Climate.md)** - Advanced climate manipulation
- **[Tutorials](../tutorials/Climate.md)** - Climate modification examples
- **[SDK Overview](../sdk/Overview.md)** - Complete SDK reference

---

**Last Updated:** December 17, 2025  
**SDK Version:** 1.1.0
