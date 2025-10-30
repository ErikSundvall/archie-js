# WASM Strategy Architecture Diagram

## Browser Integration Architecture

```
┌───────────────────────────────────────────────────────────────────┐
│                        Browser Environment                         │
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │               Web Application (JavaScript/TypeScript)         │ │
│  │  ┌────────────┐  ┌─────────────┐  ┌──────────────┐          │ │
│  │  │ UI Layer   │  │ API Client  │  │ Local Storage│          │ │
│  │  └──────┬─────┘  └──────┬──────┘  └──────┬───────┘          │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                     │
│            └────────────────┼────────────────┘                     │
│                             │                                      │
│  ┌──────────────────────────┼──────────────────────────────────┐  │
│  │         Archie JavaScript Bridge API                         │  │
│  │  ┌──────────────────────────────────────────────────────┐   │  │
│  │  │  Type Marshalling (JS ↔ Java Objects)               │   │  │
│  │  │  - String conversion                                 │   │  │
│  │  │  - Object serialization                              │   │  │
│  │  │  - Error handling                                    │   │  │
│  │  └──────────────────────────────────────────────────────┘   │  │
│  │  ┌──────────────────────────────────────────────────────┐   │  │
│  │  │  Public API                                           │   │  │
│  │  │  - parseADL(adlString): Archetype                    │   │  │
│  │  │  - validateArchetype(archetype): ValidationResult    │   │  │
│  │  │  - flattenTemplate(template): FlatArchetype          │   │  │
│  │  └──────────────────────────────────────────────────────┘   │  │
│  └──────────────────────────┬───────────────────────────────────┘  │
│                             │ WASM Interop                         │
│  ┌──────────────────────────┼───────────────────────────────────┐  │
│  │           WebAssembly Module (Archie Core)                   │  │
│  │  ┌──────────────────────────────────────────────────────┐   │  │
│  │  │  Archie Java Code (Compiled to WASM)                 │   │  │
│  │  │  ┌────────────────┐  ┌──────────────┐                │   │  │
│  │  │  │ ADL Parser     │  │ AOM Classes  │                │   │  │
│  │  │  │ (ANTLR Runtime)│  │              │                │   │  │
│  │  │  └────────────────┘  └──────────────┘                │   │  │
│  │  │  ┌────────────────┐  ┌──────────────┐                │   │  │
│  │  │  │ Validation     │  │ RM Classes   │                │   │  │
│  │  │  │ Engine         │  │              │                │   │  │
│  │  │  └────────────────┘  └──────────────┘                │   │  │
│  │  │  ┌─────────────────────────────────┐                 │   │  │
│  │  │  │  Java Dependencies               │                 │   │  │
│  │  │  │  - ANTLR 4 Runtime               │                 │   │  │
│  │  │  │  - Jackson (subset)              │                 │   │  │
│  │  │  │  - Guava (minimal)               │                 │   │  │
│  │  │  └─────────────────────────────────┘                 │   │  │
│  │  └──────────────────────────────────────────────────────┘   │  │
│  │  ┌──────────────────────────────────────────────────────┐   │  │
│  │  │  Embedded Java Runtime                                │   │  │
│  │  │  - Garbage Collector                                 │   │  │
│  │  │  - Memory Management                                 │   │  │
│  │  │  - Threading Support (limited)                       │   │  │
│  │  │  - Standard Library (subset)                         │   │  │
│  │  └──────────────────────────────────────────────────────┘   │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Browser APIs Used                         │ │
│  │  - WebAssembly Instantiation                                 │ │
│  │  - Memory (SharedArrayBuffer)                                │ │
│  │  - Console (for debugging/logging)                           │ │
│  └──────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────┘
```

## Build Pipeline Architecture

```
┌─────────────┐
│ Java Source │
│   (Archie)  │
└──────┬──────┘
       │
       ↓
┌─────────────────┐
│ Gradle Build    │
│ - Compile Java  │
│ - Run tests     │
│ - Create JAR    │
└──────┬──────────┘
       │
       ↓
┌──────────────────────┐
│ WASM Compiler        │
│ (TeaVM / CheerpJ)    │
│ - Bytecode → WASM    │
│ - Optimize           │
│ - Generate JS Bridge │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ WASM Optimization    │
│ - wasm-opt -O3       │
│ - Dead code removal  │
│ - Size reduction     │
└──────┬───────────────┘
       │
       ├─────────────────────┬──────────────────┐
       ↓                     ↓                  ↓
┌──────────────┐   ┌─────────────────┐   ┌──────────────┐
│ archie.wasm  │   │ archie.js       │   │ archie.d.ts  │
│ (2-5 MB)     │   │ (JS Bridge)     │   │ (TypeScript  │
│              │   │ (50-100 KB)     │   │  Definitions)│
└──────┬───────┘   └────────┬────────┘   └──────┬───────┘
       │                    │                    │
       └────────────────────┼────────────────────┘
                            ↓
                   ┌─────────────────┐
                   │ npm Package     │
                   │ @openehr/archie │
                   └────────┬────────┘
                            │
                            ↓
                   ┌─────────────────┐
                   │ Published to:   │
                   │ - npm Registry  │
                   │ - CDN (jsDelivr)│
                   └─────────────────┘
```

## Data Flow: Parsing ADL File

```
┌──────────────┐
│ User Action  │
│ (Parse ADL)  │
└──────┬───────┘
       │
       ↓
┌────────────────────┐
│ JavaScript Call    │
│ archie.parseADL()  │
└──────┬─────────────┘
       │
       ↓ (1) Marshal ADL string to WASM memory
┌────────────────────────┐
│ WASM Bridge            │
│ - Allocate WASM memory │
│ - Copy string data     │
│ - Get WASM function ptr│
└──────┬─────────────────┘
       │
       ↓ (2) Call WASM function
┌─────────────────────────────┐
│ WASM: ADL Parser            │
│ - ANTLR Lexer               │
│ - ANTLR Parser              │
│ - Build Parse Tree          │
│ - Create AOM Objects        │
└──────┬──────────────────────┘
       │
       ↓ (3) Return AOM object reference
┌────────────────────────────┐
│ WASM Bridge                │
│ - Serialize AOM to JSON    │
│ - Copy to JS memory        │
│ - Free WASM memory         │
└──────┬─────────────────────┘
       │
       ↓ (4) Deserialize JSON to JS object
┌────────────────────────┐
│ JavaScript             │
│ - Parse JSON           │
│ - Create Archetype obj │
│ - Return to caller     │
└──────┬─────────────────┘
       │
       ↓
┌──────────────┐
│ Result       │
│ (Archetype)  │
└──────────────┘
```

## Memory Layout

```
Browser Memory
┌───────────────────────────────────────────────────────┐
│                                                        │
│  JavaScript Heap                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │ - JavaScript objects                            │  │
│  │ - Archie Bridge API code                        │  │
│  │ - Application state                             │  │
│  │ - Serialized data from WASM                     │  │
│  └────────────────────────────────────────────────┘  │
│                                                        │
│  WebAssembly Linear Memory                            │
│  ┌────────────────────────────────────────────────┐  │
│  │ Stack (local variables, function calls)        │  │
│  ├────────────────────────────────────────────────┤  │
│  │ Heap (Java objects, allocated memory)          │  │
│  │ - Archetype instances                           │  │
│  │ - Parser state                                  │  │
│  │ - Temporary objects                             │  │
│  ├────────────────────────────────────────────────┤  │
│  │ Static Data (Java class metadata, constants)   │  │
│  └────────────────────────────────────────────────┘  │
│                                                        │
│  WASM Module Code (Read-only)                        │
│  ┌────────────────────────────────────────────────┐  │
│  │ - Compiled Java bytecode                        │  │
│  │ - ANTLR runtime                                 │  │
│  │ - Java runtime functions                        │  │
│  └────────────────────────────────────────────────┘  │
│                                                        │
└───────────────────────────────────────────────────────┘
```

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      Production CDN                      │
│                                                          │
│  ┌───────────────────────────────────────────────────┐  │
│  │  Static Assets (CDN Edge Locations)               │  │
│  │  ┌─────────────────────────────────────────────┐  │  │
│  │  │ /archie/v1.0.0/archie.wasm (cached, gzip)   │  │  │
│  │  │ Size: 1-2 MB (compressed)                    │  │  │
│  │  │ Cache-Control: max-age=31536000, immutable   │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  │  ┌─────────────────────────────────────────────┐  │  │
│  │  │ /archie/v1.0.0/archie.js (JS bridge)        │  │  │
│  │  │ Size: 30-50 KB (compressed)                  │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS
                            ↓
┌─────────────────────────────────────────────────────────┐
│                    Client Browser                        │
│  ┌───────────────────────────────────────────────────┐  │
│  │  Application Code                                  │  │
│  │  <script src="https://cdn.../archie.js"></script> │  │
│  │  - Loads archie.wasm automatically                │  │
│  │  - Initializes WASM module                        │  │
│  │  - Exposes API to application                     │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

---
**Note**: These diagrams are ASCII art representations. For production documentation, consider creating professional diagrams using tools like:
- Mermaid.js (can be embedded in Markdown)
- Draw.io / diagrams.net
- Lucidchart
- PlantUML

**Last Updated**: October 2025
