# Resource Scatter System Guide

## Overview

The Per Aspera resource scatter system controls how mineral veins and resource deposits are distributed across Mars. This comprehensive guide explains all scatter mechanisms available to modders for creating realistic and engaging resource distribution patterns.

## Table of Contents

- [Core Concepts](#core-concepts)
- [Scatter Pass Types](#scatter-pass-types)
- [Configuration Files](#configuration-files)
- [Extraction Levels](#extraction-levels)
- [Advanced Techniques](#advanced-techniques)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

---

## Core Concepts

### Resource Distribution Philosophy

Per Aspera uses a multi-layered approach to resource placement:

1. **Guaranteed starter resources** near landing sites
2. **Regional specialization** through sectored placement
3. **Geological realism** via disk-based formations
4. **Strategic gameplay** through controlled scarcity

### Key Parameters

All scatter systems share these fundamental parameters:

- **`totalAmount`** - Number of veins to generate
- **`resourceRatio`** - Array defining which resources and their properties
- **`weight`** - Relative probability of spawning (higher = more likely)
- **`qtyMin/qtyMax`** - Resource quantity range per vein
- **`extractionLevel`** - Mining difficulty (1=surface, 2=deep, 3=rare deep)

---

## Scatter Pass Types

### 1. Base Starter Veins

**File:** `InitialSetup.yaml`
**Purpose:** Guaranteed resources around landing sites

```yaml
initializeAdditionalResourceVeins: true
clearAreaRadius: 300
baseStarterVeins:
  - { resource: !resource resource_aluminum, minRadius: 80, maxRadius: 120, qtyMin: 500, qtyMax: 1000}
  - { resource: !resource resource_silicon, minRadius: 120, maxRadius: 160, qtyMin: 500, qtyMax: 1000}
  - { resource: !resource resource_iron, minRadius: 160, maxRadius: 200, qtyMin: 500, qtyMax: 1000}
```

#### Parameters

- **`clearAreaRadius`** - Area around landing site cleared of obstacles
- **`minRadius/maxRadius`** - Concentric ring placement from landing site center
- **`initializeAdditionalResourceVeins`** - Enable/disable this system

#### Use Cases

- Ensure players have access to basic resources
- Create predictable early-game progression
- Balance difficulty across different starting locations

### 2. ScatterPassSector

**Purpose:** Regional resource specialization using latitude/longitude boundaries

```yaml
- !ScatterPassSector
  minLat: 30      # Southern boundary
  maxLat: 75      # Northern boundary  
  minLng: 30      # Western boundary
  maxLng: 90      # Eastern boundary
  totalAmount: 5
  resourceRatio:
    - { resource: !resource resource_titanium, extractionLevel: 1, weight: 2, qtyMin: 1000, qtyMax: 3000 }
    - { resource: !resource resource_lithium, extractionLevel: 2, weight: 1, qtyMin: 2000, qtyMax: 5000 }
```

#### Coordinate System

- **Latitude:** -90 (South Pole) to +90 (North Pole)
- **Longitude:** 0 to 360 (wraps around planet)
- **Sectors can overlap** for resource mixing

#### Strategic Applications

- Create **mineral-rich regions** (e.g., "Silicon Valley of Mars")
- Establish **trade incentives** between distant bases
- Simulate **geological formations** spanning large areas

### 3. ScatterPassWhole

**Purpose:** Planet-wide random distribution

```yaml
- !ScatterPassWhole
  totalAmount: 15
  resourceRatio:
    - { resource: !resource resource_hydrogen, extractionLevel: 1, weight: 3, qtyMin: 800, qtyMax: 2500 }
    - { resource: !resource resource_co2_deep, extractionLevel: 2, weight: 1, qtyMin: 1500, qtyMax: 4000 }
```

#### Characteristics

- **No geographical constraints**
- **Uniform probability** across entire planet
- **Excellent for common resources** needed everywhere
- **Scales with planet size** automatically

#### Best Practices

- Use for **abundant resources** like water or CO2
- Combine with **exclusion zones** to avoid polar regions
- Set appropriate **weight ratios** to control rarity

### 4. ScatterPassDisk

**Purpose:** Circular geological formations (volcanoes, craters, plateaus)

```yaml
- !ScatterPassDisk
  x: 125.1                    # Center longitude
  y: -14.5                    # Center latitude  
  radius: 500                 # Disk radius
  focalRadius: 10            # Central concentration zone
  showInOrbitalView: true    # Visible from orbit
  totalAmount: 8
  resourceRatio:
    - { resource: !resource resource_titanium_deep, extractionLevel: 2, weight: 1, qtyMin: 3000, qtyMax: 8000 }
```

#### Advanced Parameters

- **`showInOrbitalView`** - Highlights formation in orbital scanner
- **`focalRadius`** - Creates resource concentration at center
- **Radius scales** with zoom level for precise placement

#### Real-World Applications

- **Olympus Mons** - Rare metals from ancient volcanism
- **Impact craters** - Exposed deep crustal materials  
- **Polar ice caps** - Water and CO2 concentrations
- **Mineral plateaus** - Elevated ore formations

### 5. ScatterPassAnnulus

**Purpose:** Ring-shaped resource distribution around specific points

```yaml
- !ScatterPassAnnulus
  x: 60.1           # Center longitude (landing site)
  y: 7.5            # Center latitude
  minRadius: 300    # Inner ring boundary
  maxRadius: 800    # Outer ring boundary
  focalRadius: 10   # Resource clustering factor
  totalAmount: 6
  resourceRatio:
    - { resource: !resource resource_lithium, extractionLevel: 1, weight: 1, qtyMin: 1200, qtyMax: 3500 }
```

#### Strategic Design

- **Creates exploration incentives** beyond immediate area
- **Prevents resource starvation** while maintaining challenge
- **Scales with base expansion** - rings can be nested
- **Perfect for rare resources** at controlled distances

#### Gameplay Impact

- Forces **base expansion** for advanced resources
- Creates **natural progression gates** - need rovers/transport
- Enables **risk/reward balance** - further = better resources

### 6. Exclusion Zones

**Purpose:** Prevent resource spawning in specific areas

```yaml
exclusion:
  - description: North Pole Exclusion
    x: 0
    y: 90
    radius: 1000
  - description: South Pole Exclusion  
    x: 0
    y: -90
    radius: 1000
```

#### Common Exclusions

- **Polar regions** - Too harsh for early mining
- **Story locations** - Preserve narrative sites
- **Hazard zones** - Areas with environmental dangers
- **Existing structures** - Avoid conflicts with POIs

---

## Configuration Files

### ScatterSetup.yaml

Main resource distribution file containing:

```yaml
passesOffsetX: 0    # Global X coordinate offset
passesOffsetY: 0    # Global Y coordinate offset  
passes:
  # All scatter pass definitions
exclusion:
  # Exclusion zone definitions
```

#### File Structure Best Practices

1. **Group passes by type** - All sectors together, then disks, etc.
2. **Comment extensively** - Explain the purpose of each formation
3. **Use consistent naming** - Clear resource and location identifiers
4. **Test incrementally** - Add passes gradually and verify placement

### InitialSetup.yaml

Controls starting conditions:

```yaml
building: !building landing_site
longitude: 60.1              # Landing site coordinates
latitude: 7.5
clearAreaRadius: 300         # Clear zone around landing
initializeAdditionalResourceVeins: true
baseStarterVeins:
  # Guaranteed starting resources
```

---

## Extraction Levels

### Level Hierarchy

- **Level 0** - Surface deposits (rare, small quantities)
- **Level 1** - Regolith mining (standard surface operations)  
- **Level 2** - Deep mining (requires advanced equipment)
- **Level 3** - Ultra-deep (rare, very high yields)

### Gameplay Progression

```yaml
# Early game - accessible resources
{ extractionLevel: 1, weight: 5, qtyMin: 500, qtyMax: 1500 }

# Mid game - deeper deposits  
{ extractionLevel: 2, weight: 3, qtyMin: 1500, qtyMax: 4000 }

# End game - rare massive deposits
{ extractionLevel: 3, weight: 1, qtyMin: 5000, qtyMax: 15000 }
```

### Technology Gates

Link extraction levels to research requirements:
- **Level 1** - Basic mining drone
- **Level 2** - Advanced drill technology
- **Level 3** - Quantum excavation systems

---

## Advanced Techniques

### Layered Distribution

Combine multiple pass types for realistic geology:

```yaml
# Base layer - common resources everywhere
- !ScatterPassWhole
  totalAmount: 20
  resourceRatio:
    - { resource: !resource resource_iron, extractionLevel: 1, weight: 5 }

# Regional specialization
- !ScatterPassSector  
  minLat: 45
  maxLat: 90
  totalAmount: 8
  resourceRatio:
    - { resource: !resource resource_titanium, extractionLevel: 2, weight: 1 }

# Geological hotspots
- !ScatterPassDisk
  x: 180.5
  y: 22.3
  radius: 400
  totalAmount: 5
  resourceRatio:
    - { resource: !resource resource_lithium, extractionLevel: 3, weight: 1 }
```

### Dynamic Scarcity

Create resource scarcity gradients:

1. **Abundant near landing** - Easy early access
2. **Moderate in regions** - Encourages exploration  
3. **Rare in hotspots** - End-game objectives

### Narrative Integration

Align resource placement with story beats:

```yaml
# Pre-conflict - abundant basic resources
# During conflict - strategic resource shortages  
# Post-conflict - Access to rare advanced materials
```

---

## Best Practices

### Resource Balance

#### Quantity Guidelines

- **Starting resources:** 500-1500 units per vein
- **Regional deposits:** 1500-5000 units per vein
- **Rare formations:** 5000+ units per vein

#### Weight Distribution

```yaml
# Common resources (iron, silicon)
weight: 5-10

# Uncommon resources (chemicals, carbon)  
weight: 3-5

# Rare resources (uranium, custom materials)
weight: 1-2
```

### Geographical Realism

#### Scientifically Accurate Placement

- **Water** - Polar regions, crater bottoms
- **Iron** - Ancient impact sites, volcanic regions
- **Rare metals** - Deep crustal exposures, meteorite impacts
- **Gases** - Subsurface pockets, volcanic vents

#### Mars-Specific Considerations

- **Olympus Mons region** - Volcanic rare metals
- **Valles Marineris** - Exposed geological layers
- **Polar caps** - Water and CO2 ice
- **Impact basins** - Concentrated mineral deposits

### Performance Optimization

#### Vein Count Management

- **Total veins per save:** Stay under 1000 for performance
- **Veins per pass:** 2-20 depending on scope
- **Multiple small passes** vs **few large passes** - small passes give better control

#### Memory Considerations

```yaml
# Efficient - specific placement
totalAmount: 5
radius: 300

# Inefficient - massive random scatter
totalAmount: 100
# (across whole planet)
```

---

## Troubleshooting

### Common Issues

#### Resources Not Spawning

**Symptoms:** Expected veins don't appear in-game

**Solutions:**
1. Check **extraction level** matches available mining tech
2. Verify **resource references** use correct YAML syntax
3. Ensure **coordinates** are within valid ranges
4. Check for **exclusion zone conflicts**

#### Overlapping Distributions

**Symptoms:** Too many resources in same area

**Solutions:**
1. Adjust **totalAmount** values downward
2. Increase **radius/coordinate separation**
3. Use **exclusion zones** to create buffers
4. Reduce **weight** values for rare resources

#### Performance Problems

**Symptoms:** Slow loading, lag during exploration

**Solutions:**
1. Reduce **total vein count** across all passes
2. Combine **small passes into larger ones**
3. Use **exclusion zones** instead of zero-weight resources
4. Optimize **coordinate calculations**

### Testing Methodology

#### Incremental Testing

1. **Start with one pass type** - Verify basic functionality
2. **Add passes gradually** - Test each addition
3. **Check all extraction levels** - Ensure tech progression works
4. **Verify coordinates** - Test edge cases (poles, date line)

#### Debug Tools

```yaml
# Enable debug visualization
showInOrbitalView: true

# Temporary high quantities for testing
qtyMin: 10000
qtyMax: 50000

# Concentrated test areas
totalAmount: 1
radius: 100
```

### Validation Checklist

Before releasing scatter configurations:

- [ ] **All resources reference valid definitions**
- [ ] **Coordinates within planetary bounds**
- [ ] **No conflicting exclusion zones**
- [ ] **Balanced quantity distributions**
- [ ] **Appropriate extraction level progression**
- [ ] **Performance tested with full mod load**

---

## Conclusion

The Per Aspera scatter system provides powerful tools for creating engaging resource distribution patterns. By combining different pass types strategically, modders can create realistic geological formations that enhance both gameplay and narrative immersion.

Remember to always test thoroughly, document your intentions clearly, and consider the long-term gameplay impact of your resource placement decisions. The scatter system is one of the most impactful aspects of mod design, directly affecting player exploration patterns and strategic decisions.

For additional support, consult the [Resource Definition Guide](Resource-Definition-Guide.md) and [Building System Guide](Building-System-Guide.md) for comprehensive mod development workflows.