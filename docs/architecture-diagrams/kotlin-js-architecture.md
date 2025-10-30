# Kotlin/JS Strategy Architecture Diagram

## Multiplatform Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Kotlin Multiplatform Project                     │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │              commonMain (Shared Kotlin Code)                │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │ │
│  │  │  AOM Classes │  │ RM Classes   │  │  Validation  │     │ │
│  │  │              │  │              │  │   Engine     │     │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘     │ │
│  │  ┌────────────────────────────────────────────────────┐   │ │
│  │  │  Core Logic (Platform-agnostic)                     │   │ │
│  │  │  - Data structures                                  │   │ │
│  │  │  - Business logic                                   │   │ │
│  │  │  - Algorithms                                       │   │ │
│  │  └────────────────────────────────────────────────────┘   │ │
│  └───────────────────┬────────────────────┬───────────────────┘ │
│                      │                    │                     │
│         ┌────────────┴────────┐  ┌────────┴────────────┐       │
│         ↓                     ↓  ↓                     ↓       │
│  ┌─────────────┐       ┌─────────────────────────────────┐    │
│  │  jvmMain    │       │           jsMain                 │    │
│  │  (JVM)      │       │        (JavaScript)              │    │
│  ├─────────────┤       ├─────────────────────────────────┤    │
│  │ - ANTLR     │       │ - ANTLR JS                       │    │
│  │   Runtime   │       │   Runtime                        │    │
│  │ - JVM Utils │       │ - Browser                        │    │
│  │ - File I/O  │       │   Interop                        │    │
│  │             │       │ - DOM Access                     │    │
│  └──────┬──────┘       └──────┬──────────────────────────┘    │
│         │                     │                                │
└─────────┼─────────────────────┼────────────────────────────────┘
          │                     │
          ↓                     ↓
   ┌──────────────┐      ┌──────────────────┐
   │  JVM JAR     │      │  JavaScript Bundle│
   │  (Server)    │      │   (Browser/Node)  │
   └──────────────┘      └──────────────────┘
```

## Conversion Workflow

```
┌─────────────────────┐
│   Java Source       │
│   (Archie)          │
│   - 944 files       │
│   - 88K LOC         │
└──────┬──────────────┘
       │
       ↓ (1) Automated Conversion
┌─────────────────────────────┐
│ IntelliJ IDEA Converter     │
│ or J2K Tool                 │
│ - Syntax conversion         │
│ - ~70-80% automated         │
│ - Basic type inference      │
└──────┬──────────────────────┘
       │
       ↓ (2) Generated Kotlin Code
┌─────────────────────────────┐
│ Kotlin Source (Draft)       │
│ - Compilable but not ideal  │
│ - Many warnings             │
│ - Not idiomatic Kotlin      │
└──────┬──────────────────────┘
       │
       ↓ (3) Manual Refinement
┌─────────────────────────────┐
│ Kotlin Source (Refined)     │
│ - Idiomatic Kotlin          │
│ - Null safety               │
│ - Data classes              │
│ - Extension functions       │
└──────┬──────────────────────┘
       │
       ├──────────────┬────────────────┐
       ↓              ↓                ↓
┌────────────┐  ┌───────────┐  ┌──────────────┐
│ commonMain │  │  jvmMain  │  │   jsMain     │
│  (Shared)  │  │   (JVM)   │  │ (JavaScript) │
└──────┬─────┘  └─────┬─────┘  └──────┬───────┘
       │              │                │
       └──────────────┼────────────────┘
                      │
                      ↓
             ┌─────────────────┐
             │ Gradle Build     │
             └────────┬─────────┘
                      │
        ┌─────────────┴─────────────┐
        ↓                           ↓
┌────────────────┐        ┌──────────────────┐
│ Kotlin/JVM     │        │  Kotlin/JS       │
│  Compiler      │        │   Compiler (IR)  │
└────────┬───────┘        └──────┬───────────┘
         │                       │
         ↓                       ↓
  ┌─────────────┐        ┌──────────────────┐
  │ archie.jar  │        │ archie.js        │
  │ (JVM)       │        │ + kotlin-stdlib  │
  └─────────────┘        └──────┬───────────┘
                                │
                                ↓
                        ┌──────────────────┐
                        │ Webpack/Rollup   │
                        │ - Bundle         │
                        │ - Optimize       │
                        │ - Source maps    │
                        └──────┬───────────┘
                               │
                               ↓
                        ┌──────────────────┐
                        │ npm Package      │
                        │ @openehr/archie  │
                        └──────────────────┘
```

## Browser Integration

```
┌────────────────────────────────────────────────────────────┐
│                      Browser Environment                    │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │         Web Application (TypeScript/JavaScript)        │ │
│  │  import { ArchieKt } from '@openehr/archie-kotlin'    │ │
│  └────────────────────────┬──────────────────────────────┘ │
│                           │                                 │
│                           ↓                                 │
│  ┌───────────────────────────────────────────────────────┐ │
│  │      Archie Kotlin/JS Module (ES6/UMD/CommonJS)       │ │
│  │  ┌─────────────────────────────────────────────────┐  │ │
│  │  │  Transpiled Kotlin Code (JavaScript)            │  │ │
│  │  │  ┌──────────┐  ┌───────────┐  ┌──────────┐     │  │ │
│  │  │  │  Parser  │  │    AOM    │  │Validation│     │  │ │
│  │  │  └──────────┘  └───────────┘  └──────────┘     │  │ │
│  │  │  ┌──────────────────────────────────────────┐  │  │ │
│  │  │  │  Business Logic (from commonMain)        │  │  │ │
│  │  │  └──────────────────────────────────────────┘  │  │ │
│  │  └─────────────────────────────────────────────────┘  │ │
│  │  ┌─────────────────────────────────────────────────┐  │ │
│  │  │  Kotlin Standard Library (JS) ~200KB           │  │ │
│  │  │  - Collections                                  │  │ │
│  │  │  - Coroutines                                   │  │ │
│  │  │  - Utilities                                    │  │ │
│  │  └─────────────────────────────────────────────────┘  │ │
│  │  ┌─────────────────────────────────────────────────┐  │ │
│  │  │  JavaScript Interop Layer                       │  │ │
│  │  │  - Type conversions                             │  │ │
│  │  │  - External declarations                        │  │ │
│  │  └─────────────────────────────────────────────────┘  │ │
│  └───────────────────────────────────────────────────────┘ │
│                                                             │
└────────────────────────────────────────────────────────────┘
```

## Data Flow: Java to Kotlin to JavaScript

```
Original Java Code:
┌────────────────────────────────────────────┐
│ public class Archetype {                   │
│   private String archetypeId;              │
│   private List<CAttribute> attributes;     │
│                                            │
│   public String getArchetypeId() {         │
│     return archetypeId;                    │
│   }                                        │
│ }                                          │
└──────────────┬─────────────────────────────┘
               │ Automated Conversion
               ↓
Converted Kotlin Code:
┌────────────────────────────────────────────┐
│ class Archetype {                          │
│   private var archetypeId: String? = null  │
│   private var attributes:                  │
│       MutableList<CAttribute>? = null      │
│                                            │
│   fun getArchetypeId(): String? {          │
│     return archetypeId                     │
│   }                                        │
│ }                                          │
└──────────────┬─────────────────────────────┘
               │ Manual Refinement
               ↓
Idiomatic Kotlin Code:
┌────────────────────────────────────────────┐
│ data class Archetype(                      │
│   val archetypeId: String,                 │
│   val attributes: List<CAttribute> = emptyList()│
│ )                                          │
└──────────────┬─────────────────────────────┘
               │ Kotlin/JS Compiler
               ↓
Generated JavaScript:
┌────────────────────────────────────────────┐
│ function Archetype(archetypeId, attributes)│
│   this.archetypeId = archetypeId;          │
│   this.attributes = attributes !== undefined│
│     ? attributes : Kotlin.emptyList();     │
│ }                                          │
│ Archetype.prototype.component1 = function()│
│   return this.archetypeId;                 │
│ }                                          │
└────────────────────────────────────────────┘
```

## Dual-Target Deployment

```
┌──────────────────────────────────────────────────────────┐
│             Single Kotlin Codebase                        │
└────────────────┬─────────────────────────────────────────┘
                 │
     ┌───────────┴───────────┐
     │                       │
     ↓                       ↓
┌──────────┐          ┌────────────┐
│   JVM    │          │ JavaScript │
│  Target  │          │   Target   │
└────┬─────┘          └─────┬──────┘
     │                      │
     ↓                      ↓
┌──────────┐          ┌────────────┐
│  Server  │          │  Browser   │
│    OR    │          │     OR     │
│ Desktop  │          │   Node.js  │
└──────────┘          └────────────┘
```

## Maintenance Workflow (Forked Scenario)

```
┌───────────────────┐
│ openEHR/archie    │
│ (Java, upstream)  │
└────────┬──────────┘
         │ New release
         ↓
┌────────────────────┐
│ Review changes     │
│ - Bug fixes        │
│ - New features     │
│ - Breaking changes │
└────────┬───────────┘
         │
         ↓
┌────────────────────┐
│ Convert to Kotlin  │
│ - Manual or tool   │
│ - Adapt to Kotlin  │
│   idioms           │
└────────┬───────────┘
         │
         ↓
┌────────────────────┐
│ Test JVM target    │
│ - Unit tests       │
│ - Integration tests│
└────────┬───────────┘
         │
         ↓
┌────────────────────┐
│ Test JS target     │
│ - Browser tests    │
│ - Node tests       │
└────────┬───────────┘
         │
         ↓
┌────────────────────┐
│ Release new version│
│ - JVM artifact     │
│ - npm package      │
└────────────────────┘
```

## Memory Layout Comparison

### JVM (Kotlin/JVM):
```
┌──────────────────────────────┐
│     JVM Heap                 │
│  ┌────────────────────────┐  │
│  │ Kotlin Objects          │  │
│  │ (similar to Java)       │  │
│  │ - Efficient GC          │  │
│  │ - Compact representation│  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

### JavaScript (Kotlin/JS):
```
┌──────────────────────────────┐
│   JavaScript Heap            │
│  ┌────────────────────────┐  │
│  │ JavaScript Objects      │  │
│  │ (from Kotlin)           │  │
│  │ - Standard JS GC        │  │
│  │ - Kotlin stdlib overhead│  │
│  └────────────────────────┘  │
│  ┌────────────────────────┐  │
│  │ Kotlin Runtime (~200KB) │  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

---
**Note**: Diagrams show the multiplatform nature of Kotlin/JS strategy and the two-phase conversion required.

**Last Updated**: October 2025
