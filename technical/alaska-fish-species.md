> *DeepSeek Chat*

# Alaska Fishing Species Database Design Document
## FishingLog.ai

### 1. Overview
This document outlines the database schema and structure for the Alaska fishing species module of FishingLog.ai. The system is designed to provide anglers with comprehensive, actionable data on 20 key Alaskan sportfish species. The database will serve as the authoritative source for species-specific information, powering logbook analytics, trip planning recommendations, and regulatory compliance features. Data will be sourced from the Alaska Department of Fish and Game (ADF&G), NOAA Fisheries, scientific publications, and aggregated user catch data from the FishingLog.ai platform.

### 2. Core Design Principles
- **Accuracy & Authority:** Regulatory and biological data must be precise and sourced from official agencies.
- **Seasonality & Locality:** Information must be specific to Alaskan waters, accounting for regional variations.
- **Actionability:** Data should directly inform fishing tactics, gear selection, and trip planning.
- **Maintainability:** Schema must allow for easy updates as regulations and scientific understanding change.
- **Extensibility:** Design should accommodate future addition of species, regions, or data points.

### 3. Database Schema & TypeScript Interface

```typescript
/**
 * FishingLog.ai - Alaska Species Database
 * Primary Interface Definition
 */

interface ConservationStatus {
  iucnStatus: 'LC' | 'NT' | 'VU' | 'EN' | 'CR' | 'NE'; // IUCN Red List Categories
  alaskaSpecificStatus?: 'Secure' | 'Sensitive' | 'Watchlist' | 'Not Assessed';
  notes?: string;
}

interface SeasonRating {
  months: number[]; // 1-12, e.g., [6,7] for June-July
  quality: 'Poor' | 'Fair' | 'Good' | 'Excellent' | 'Peak';
  reason?: string; // e.g., "Pre-spawn Aggregation", "Ocean Return"
}

interface Regulation {
  area: string; // e.g., "Southeast Alaska", "Kenai River"
  sizeLimitInches?: { min?: number; max?: number; slot?: { min: number; max: number } };
  bagLimit: number; // Daily possession limit
  seasonalClosures?: { start: string; end: string }[]; // ISO dates or month-day
  specialRules?: string[]; // e.g., "Single-hook, artificial only"
  source: string; // URL to ADF&G regulation page
  effectiveDate: string;
}

interface FightCharacteristics {
  strength: number; // 1-10 scale
  acrobatics: number; // 1-10 scale
  endurance: number; // 1-10 scale
  description: string; // e.g., "Powerful, deep runs with occasional jumps"
}

interface LocationHotspot {
  name: string; // e.g., "Kenai River", "Homer Spit"
  region: 'Southeast' | 'Southcentral' | 'Interior' | 'Southwest' | 'Arctic';
  type: 'River' | 'Stream' | 'Lake' | 'Bay' | 'Sound' | 'Ocean';
  coordinates?: { lat: number; lon: number }; // Approximate centroid
  peakMonths: number[];
  notes?: string; // Access details, specific holes
}

interface BehaviorPatterns {
  feedingTimes: ('Dawn' | 'Day' | 'Dusk' | 'Night')[];
  migrationMonths?: number[];
  spawning: {
    months: number[];
    habitat: string; // e.g., "Gravel-bottomed tributary streams"
    behaviorNotes?: string;
  };
  other?: string; // e.g., "Holds in deep pools in summer"
}

interface AlaskaFishingSpecies {
  // Core Identification
  id: string; // UUID or slug e.g., "king-salmon-chinook"
  scientificName: string;
  commonName: string;
  localNames: string[]; // e.g., ["Chinook", "Tyee", "Blackmouth"]
  family: string;
  imageUrl: string;

  // Temporal & Habitat Data
  bestSeasons: SeasonRating[];
  preferredDepth: { minFeet: number; maxFeet: number; optimalFeet?: number };
  preferredWaterTempF: { min: number; max: number; optimal?: number };

  // Size Data
  commonWeightLbs: { min: number; max: number };
  recordWeightLbs: { weight: number; location?: string; year?: number; source?: string };

  // Angling Data
  preferredBait: string[];
  preferredLures: string[];
  fightCharacteristics: FightCharacteristics;
  edibilityRating: 1 | 2 | 3 | 4 | 5; // 5 = excellent table fare
  edibilityNotes?: string; // e.g., "Best smoked", "Strong flavor"

  // Conservation & Regulations
  conservationStatus: ConservationStatus;
  regulations: Regulation[]; // Array allows for regional regulation differences

  // Geographic Data
  bestLocations: LocationHotspot[];

  // Biological Data
  behavior: BehaviorPatterns;

  // Metadata
  dataSources: string[];
  lastUpdated: string;
  version: number;
}
```

### 4. Detailed Field Specifications & Rationale

#### 4.1 Identification & Taxonomy
- `scientificName`: Binomial nomenclature for unambiguous global identification.
- `localNames`: Array accommodates regional vernacular (e.g., "Dog" for Chum, "Humpy" for Pink).
- `family`: Enables grouping for biological comparisons (e.g., all Salmonidae).

#### 4.2 Temporal & Habitat Data
- `bestSeasons`: Array of `SeasonRating` objects allows representation of multiple peaks (e.g., early-run vs. late-run kings). Quality ratings guide user priority.
- `preferredDepth` and `preferredWaterTempF`: Ranges with optional optimal points. Critical for electronics interpretation and seasonal pattern prediction.

#### 4.3 Size Data
- `commonWeightLbs`: Realistic expected catch range, distinct from potential maximum.
- `recordWeightLbs`: Includes provenance for credibility. Motivational data point for anglers.

#### 4.4 Angling Data
- `preferredBait/Lures`: Separate arrays for natural vs. artificial. Will be linked to gear recommendation engine.
- `fightCharacteristics`: Three-dimensional 1-10 rating allows for nuanced comparison (e.g., Steelhead: high acrobatics, Halibut: high strength).
- `edibilityRating`: Subjective but crucial for harvest-oriented anglers. Notes field adds nuance (preparation methods, flavor profile).

#### 4.5 Conservation & Regulations
- `conservationStatus`: Dual IUCN and Alaska-specific statuses provide global and local context.
- `regulations`: **Most complex and critical field.** Array structure is essential due to Alaska's highly regionalized management. Each entry is tied to a specific `area`. `effectiveDate` ensures automatic flagging of outdated rules. `source` links directly to ADF&G for verification.

#### 4.6 Geographic Data
- `bestLocations`: `LocationHotspot` objects provide structured, geocodable data. `region` allows for high-level filtering. `type` informs gear and technique.

#### 4.7 Biological Data
- `behavior`: Structured `feedingTimes`, `migrationMonths`, and `spawning` data drives predictive features in the app (e.g., "Best time to fish" notifications).

### 5. Example Instance: King Salmon (Chinook)

```typescript
const kingSalmon: AlaskaFishingSpecies = {
  id: "king-salmon-chinook",
  scientificName: "Oncorhynchus tshawytscha",
  commonName: "King Salmon",
  localNames: ["Chinook", "Tyee", "Blackmouth", "Spring"],
  family: "Salmonidae",
  imageUrl: "/assets/species/king-salmon.jpg",

  bestSeasons: [
    { months: [5, 6], quality: "Excellent", reason: "Early-run, large ocean-fresh fish" },
    { months: [7], quality: "Peak", reason: "Peak of Kenai River run" },
    { months: [8], quality: "Good", reason: "Late-run, maturing fish" }
  ],
  preferredDepth: { minFeet: 5, maxFeet: 120, optimalFeet: 25 },
  preferredWaterTempF: { min: 48, max: 58, optimal: 53 },

  commonWeightLbs: { min: 15, max: 40 },
  recordWeightLbs: { weight: 97.25, location: "Kenai River", year: 1985, source: "IGFA" },

  preferredBait: ["Herring", "Salmon Roe", "Squid"],
  preferredLures: ["Kwikfish", "Spoons", "Rotators", "Large Plugs"],
  fightCharacteristics: {
    strength: 9,
    acrobatics: 6,
    endurance: 8,
    description: "Powerful, sustained deep runs with occasional surface thrashing."
  },
  edibilityRating: 5,
  edibilityNotes: "Rich, high-fat flesh. Excellent