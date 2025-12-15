# POI System Reference

## Overview

Per Aspera features a comprehensive **Points of Interest (POI)** system that provides authentic Martian geographic features and historical Mars mission sites. The system consists of approximately **2,000 geographic POIs** and **60+ special sites** representing real Mars missions.

## System Architecture

The POI system is implemented across three main datamodel files:

- **`poi.yaml`** - Geographic features (craters, mountains, valleys, etc.)
- **`poi-adaptable-craters.yaml`** - Unnamed craters for crater dome placement
- **`site.yaml`** - Historical Mars mission sites

## Geographic POI System

### POI Types (30 Total)

Per Aspera uses official Martian geographic nomenclature with 30 different POI types:

| Type | Count | Description | Example |
|------|-------|-------------|---------|
| **Crater** | 1,114 | Impact craters | Gale Crater |
| **Vallis** | 149 | Valleys | Valles Marineris |
| **Albedo** | 126 | Light/dark surface regions | Syrtis Major |
| **Dorsum** | 34 | Ridges | Solis Dorsum |
| **Planum** | 32 | Plateaus | Hesperia Planum |
| **Chaos** | 30 | Chaotic terrain | Aram Chaos |
| **Cavus** | 28 | Irregular depressions | Juventae Cavus |
| **Patera** | 28 | Volcanic craters | Tyrrhena Patera |
| **Rupes** | 26 | Escarpments | Ogygis Rupes |
| **Chasma** | 25 | Canyons | Candor Chasma |
| **Collis** | 22 | Hills | Deuteronilus Collis |
| **Tholus** | 22 | Volcanic domes | Tharsis Tholus |
| **Catena** | 16 | Crater chains | Acheron Catena |
| **Sulcus** | 14 | Grooves | Gigas Sulcus |
| **Scopulus** | 13 | Irregular scarps | Nilokeras Scopulus |
| **Mare** | 11 | Dark plains (historical) | Mare Acidalium |
| **Terra** | 11 | Large land masses | Arabia Terra |
| **Planitia** | 10 | Low plains | Utopia Planitia |
| **Labes** | 7 | Landslides | Ophir Labes |
| **Labyrinthus** | 6 | Valley complexes | Noctis Labyrinthus |
| **Unda** | 6 | Dunes | Siton Undae |
| **Lingula** | 5 | Tongue-shaped features | Aurorae Lingula |
| **Palus** | 5 | Marshes (historical) | Lunae Palus |
| **Fluctus** | 3 | Flow terrain | Galaxias Fluctus |
| **Serpens** | 3 | Sinuous features | Anguis Serpens |
| **Macula** | 1 | Spot/dark feature | Sirenum Macula |
| **Vastitas** | 1 | Vast lowland | Vastitas Borealis |
| **Mons** | 52 | Mountains | Olympus Mons |
| **Mensa** | 35 | Mesas | Nilosyrtis Mensae |
| **Fossa** | 59 | Long narrow depressions | Ceraunius Fossae |

### YAML Structure (Geographic POI)

```yaml
poi_Crater_Gale:
  # Geographic coordinates
  centerLatitude: -5.4          # Center point latitude (-90 to 90)
  centerLongitude: 137.8         # Center point longitude (-180 to 180 or 0 to 360)
  diameter: 154.0                # Diameter in kilometers
  
  # Bounding box coordinates
  northernLatitude: -4.4         # Northern boundary
  southernLatitude: -6.4         # Southern boundary
  easternLongitude: 138.8        # Eastern boundary
  westernLongitude: 136.8        # Western boundary
  
  # Identification
  name: Gale                     # Display name
  poiType: Crater                # Type from POIType enum
```

### Data Properties

Each geographic POI contains the following properties:

**Position Data:**
- `centerLatitude` - Center point latitude (-90 to 90)
- `centerLongitude` - Center point longitude (-180 to 180 or 0 to 360)
- `diameter` - Feature diameter in kilometers

**Bounding Box:**
- `northernLatitude` - Northern boundary
- `southernLatitude` - Southern boundary
- `easternLongitude` - Eastern boundary
- `westernLongitude` - Western boundary

**Identification:**
- `name` - Display name of the feature
- `poiType` - Geographic feature type (one of 30 types)

### Adaptable Craters

**File:** `poi-adaptable-craters.yaml` (1,291 lines, ~180 craters)

These are **unnamed craters** specifically designed for crater dome placement:

```yaml
poi_crater_02-000007:
  centerLatitude: 39.133
  centerLongitude: -135.387
  diameter: 57.16
  name: null                    # No name (unnamed crater)
  poiType: Crater
```

**Characteristics:**
- **No names** (`name: null`)
- **Diameter range:** ~55-60 km (optimal for crater domes)
- **Purpose:** Procedural crater dome site selection
- **Commented entries:** Some craters are commented out (disabled)

## Special Sites System

### Site Classes

Special sites represent historical Mars missions and discoveries:

| Class | Count | Description | Example |
|-------|-------|-------------|---------|
| **RUINS** | 38 | Ancient ruins (fictional) | Various archaeological sites |
| **LANDER** | 16 | Mars landers | Viking 1, Perseverance |
| **ROVER** | 4 | Mars rovers | Spirit, Opportunity, Curiosity |
| **CRASH** | 4 | Crashed missions | Mars 2, Mars 6 |

**Total Special Sites:** ~62

### YAML Structure (Special Site)

```yaml
special_site_viking_1:
  # Site Information
  actions: []                                      # Available actions (empty = research only)
  description: BE_special_site_viking_1_description # Localized description key
  year: BE_special_site_year_1976                  # Mission year (localized)
  organization: BE_special_site_organization_US     # Space agency (localized)
  
  # Discovery Settings
  discoveryType: ALWAYS_KNOWN                      # Discovery mechanism
  
  # Position
  geoPosition:
    x: -48.222                                     # Longitude (x-coordinate)
    y: 22.697                                      # Latitude (y-coordinate)
  
  # Knowledge Rewards
  knowledgeOnDiscovery: !knowledge knowledge_viking_1_basic    # Initial discovery reward
  knowledgeOnResearch: !knowledge knowledge_viking_1           # Research completion reward
  
  # Distance Constraints (for random placement - not used with FIXED_POSITION)
  maxDistance: 0.0
  minDistance: 0.0
  otherSite: null
  
  # Position Type
  positionType: FIXED_POSITION                     # FIXED_POSITION or random
  
  # Research Cost
  requiredResearch: 30.0                           # Research points required
  
  # Identification
  name: BE_special_site_viking_1_name              # Localized name key
  siteClass: LANDER                                # Site type
  prefabName: OldLander                            # 3D model to display
```

### Discovery Types

- **`ALWAYS_KNOWN`** - Visible from game start (historical missions)
- Other types may include procedural discovery mechanisms

### Position Types

- **`FIXED_POSITION`** - Exact coordinates defined (all historical sites)
- Random placement options available via `minDistance`/`maxDistance`

### Real Mars Missions Included

The game features authentic Mars exploration history:

**Early Missions (1970s)**
- Mars 2 (USSR, 1971) - CRASH
- Mars 3 (USSR, 1971) - LANDER
- Mars 6 (USSR, 1974) - CRASH
- Viking 1 (USA, 1976) - LANDER
- Viking 2 (USA, 1976) - LANDER

**Pathfinder Era (1990s-2000s)**
- Mars Pathfinder (USA, 1997) - LANDER
- Spirit Rover (USA, 2004) - ROVER
- Opportunity Rover (USA, 2004) - ROVER

**Modern Missions (2010s-2020s)**
- Curiosity Rover (USA, 2012) - ROVER
- InSight (USA, 2018) - LANDER
- Tianwen-1 (China, 2021) - LANDER
- Perseverance (USA, 2021) - ROVER
- Zhurong (China, 2021) - ROVER
- ExoMars (Europe/Russia, 2023) - LANDER
- Terex (Japan, 2023) - LANDER
- Mangalyaan 2 (India, 2023) - LANDER

## Prefab Models

Special sites use 3D models for visual representation:

| Prefab Name | Description | Used By |
|-------------|-------------|---------|
| **OldLander** | Generic lander model | Viking, InSight, etc. |
| **CrashedSatellite** | Crash site debris | Mars 2, Mars 6 |
| **Rover** | Rover model | Spirit, Opportunity, Curiosity, Perseverance |
| **RuinsPrefab** | Ancient ruins (various) | Ruins sites |

## Coordinate System

### Geographic POI Coordinates

- **Latitude Range:** -90° (South Pole) to +90° (North Pole)
- **Longitude Range:** -180° to +180° or 0° to 360° (mixed usage)
- **Diameter:** Kilometers (actual Martian measurements)

### Special Site Coordinates

- **geoPosition.x** - Longitude
- **geoPosition.y** - Latitude
- Uses precise coordinates matching real Mars mission landing sites

## Knowledge System Integration

Special sites provide **knowledge rewards** in two stages:

1. **Discovery Knowledge** (`knowledgeOnDiscovery`)
   - Awarded when site is first discovered
   - Basic information about the mission

2. **Research Knowledge** (`knowledgeOnResearch`)
   - Awarded after completing research
   - Requires `requiredResearch` points (typically 30.0)
   - Extended/detailed information

**Example:**
```yaml
knowledgeOnDiscovery: !knowledge knowledge_viking_1_basic
knowledgeOnResearch: !knowledge knowledge_viking_1
requiredResearch: 30.0
```

## Modding the POI System

### Adding Missing or New Geographic POIs

Since Per Aspera's release, new Martian features have been discovered and officially named. You can add these to your mod:

**Step 1: Find Official Data**
- **USGS Gazetteer:** https://planetarynames.wr.usgs.gov/
- Search for Mars features by name or coordinates
- Get accurate latitude, longitude, and diameter

**Step 2: Create POI Entry**

```yaml
poi_Crater_Jezero:
  centerLatitude: 18.38
  centerLongitude: 77.58
  diameter: 45.0
  northernLatitude: 18.85
  southernLatitude: 17.91
  easternLongitude: 77.85
  westernLongitude: 77.31
  name: Jezero
  poiType: Crater
```

**Calculating Bounding Box:**
```
Approximate formula (adjust for Mars radius):
northernLatitude = centerLatitude + (diameter / 111.0 km/degree)
southernLatitude = centerLatitude - (diameter / 111.0 km/degree)
easternLongitude = centerLongitude + (diameter / (111.0 * cos(latitude)))
westernLongitude = centerLongitude - (diameter / (111.0 * cos(latitude)))
```

**Naming Convention:**
- Key format: `poi_<Type>_<Name>` (spaces become underscores)
- Use official IAU (International Astronomical Union) approved names
- For unnamed features, use descriptive names or coordinates

**Important:**
- Use unique key name to avoid conflicts
- Ensure bounding box encompasses the entire feature
- Choose appropriate `poiType` from the 30 available types
- Verify coordinates are in correct format (consistent longitude range)

### cent Missions to Add (Post-Game Launch):**

Since the game's release, these real missions could be added:

```yaml
# Example: Perseverance Landing Site (already in game, but shows format)
special_site_perseverance_2021:
  actions: []
  description: BE_special_site_perseverance_description
  year: BE_special_site_year_2021
  organization: BE_special_site_organization_US
  discoveryType: ALWAYS_KNOWN
  geoPosition:
    x: 77.45    # Jezero Crater
    y: 18.44
  knowledgeOnDiscovery: !knowledge knowledge_perseverance_basic
  knowledgeOnResearch: !knowledge knowledge_perseverance
  maxDistance: 0.0
  minDistance: 0.0
  otherSite: null
  positionType: FIXED_POSITION
  requiredResearch: 30.0
  name: BE_special_site_perseverance_name
  siteClass: ROVER
  prefabName: Rover
```

**Requirements:**
- Create corresponding knowledge entries in `knowledge.yaml`
- Add localization strings in CSV files for:
  - `name` - Mission name
  - `description` - Mission details and significance
  - `year` - Mission year
  - `organization` - Space agency
- Ensure coordinates match real landing sites
- Use appropriate `siteClass` (LANDER, ROVER, CRASH)
- Choose existing `prefabName` or create custom 3D model

### Adaptable Crater Selection

Craters in `poi-adaptable-craters.yaml` are filtered by:
- **Diameter range** (~55-60 km optimal)
- **Geographic suitability** (flat terrain, good accessibility)
- **Distribution** (spread across Mars for variety)

To add more crater dome sites, add unnamed craters:
```yaml
poi_crater_custom_001:
  centerLatitude: 25.0
  centerLongitude: -110.0
  diameter: 58.0
  name: null
  poiType: Crater
```

## Creating a POI Update Mod

**Recommended Mod Structure:**

```
YourMod/
├── manifest.yaml
└── datamodel/
    ├── poi.yaml              # Add new geographic features
    └── site.yaml             # Add new mission sites
```

**Example Workflow:**

1. **Research New Features**
   - Check USGS Planetary Nomenclature for newly named features
   - Find NASA/ESA mission data for recent landings
   - Verify coordinates and dimensions

2. **Create YAML Additions**
   - Add only new entries (game will merge with base data)
   - Test coordinates in-game to verify placement

3. **Add Supporting Data** (if needed)
   - Create knowledge entries for mission sites
   - Add localization strings
   - Include custom prefabs if desired

4. **Test Compatibility**
   - Ensure no key conflicts with base game
   - Test with existing saves
   - Verify POIs appear correctly on map

**Example: New Crater Discovery Mod**

```yaml
# poi.yaml - Add newly discovered/named craters
poi_Crater_NewDiscovery2024:
  centerLatitude: 12.5
  centerLongitude: 88.3
  diameter: 32.0
  northernLatitude: 12.79
  southernLatitude: 12.21
  easternLongitude: 88.59
  westernLongitude: 88.01
  name: New Discovery 2024
  poiType: Crater
```

## Sources for Missing POIs

**Official Naming Authority:**
- **IAU Working Group for Planetary System Nomenclature**
- **USGS Astrogeology Science Center Gazetteer**
  - Direct link: https://planetarynames.wr.usgs.gov/
  - Filter by "Mars" and sort by approval date to find recent additions

**Mars Mission Data:**
- **NASA Mars Exploration:** https://mars.nasa.gov/
- **ESA Mars Express:** https://www.esa.int/Science_Exploration/Space_Science/Mars_Express
- **Landing Coordinates:** Available in mission documentation
- **China National Space Administration (CNSA)** - Tianwen/Zhurong data
- **ISRO (India)** - Mangalyaan mission data

**Recent Discoveries to Consider:**

Since the game's development (~2020), these categories may need updates:
- Craters named after notable scientists (ongoing)
- Features discovered by active missions (Perseverance, Curiosity findings)
- Newly approved nomenclature from 2020-2025
- Updated landing site coordinates for recent missions
public static List<PointOfInterest> habitableCraters;  // Craters suitable for habitation
```

These are populated by filtering the full POI dataset based on game logic criteria.

## Geographic Queries

The `PointOfInterest` class provides spatial query methods:

```csharp
// Check if coordinates are inside POI boundaries
bool IsPointInside(Planet planet, Vector2 geoPos);

// Get actual map sizes accounting for latitude distortion
float GetVerticalSize(Planet planet);
float GetHorizontalSize(Planet planet);
```

These methods account for Mars's spherical geometry when calculating distances and boundaries.

## POI Collections

The game maintains filtered collections of POIs for gameplay purposes:

- **Small Craters** - Craters below a certain size threshold
- **Habitable Craters** - Craters suitable for settlement/crater domes

These collections are populated at game initialization by filtering the full POI dataset based on game logic criteria.

## Spatial Queries

The POI system supports geographic queries to determine:

- Whether specific coordinates fall within a POI's boundaries
- The actual map size of a POI (accounting for latitude distortion on spherical Mars)
- Vertical and horizontal dimensions for rendering and gameplay

These calculations account for Mars's spherical geometry when compu
3. Comment out (`#`) instead of deleting to preserve data
4. Ensure good geographic distribution

## Related Systems

- **Knowledge System** - POI discoveries grant knowledge
- **Research System** - Special sites require research investment
- **Localization** - All names/descriptions use localization keys
- **3D Prefabs** - Visual representation of special sites
- **Planet System** - Geographic calculations and rendering
- **Building System** - Crater domes use adaptable craters

## References

- Official Martian nomenclature: [USGS Gazetteer of Planetary Nomenclature](https://planetarynames.wr.usgs.gov/)
- Mars mission data: NASA, ESA, and international space agencies
- Coordinate system: Areographic (Mars-centric) coordinates

---

**Last Updated:** December 2025  
**Game Version:** Per Aspera 1.x  
**Documentation Status:** Complete
