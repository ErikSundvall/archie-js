# TypeScript Strategy Architecture Diagram

## Modular Package Architecture

```
@openehr/archie (TypeScript Library)
├─────────────────────────────────────────┐
│                                          │
│  ┌────────────┐  ┌─────────────────┐    │
│  │  @archie/  │  │  @archie/aom    │    │
│  │   parser   │─▶│  (Object Model) │    │
│  └─────┬──────┘  └────────┬────────┘    │
│        │                   │             │
│   ┌────▼────────────────▼─────────┐     │
│   │   @archie/validation           │     │
│   │   (Archetype Validator)        │     │
│   └────────────┬───────────────────┘     │
│                │                         │
│   ┌────────────▼───────────────────┐     │
│   │   @archie/rm                   │     │
│   │   (Reference Model)            │     │
│   └────────────────────────────────┘     │
│                                          │
│   ┌──────────────────────────────────┐   │
│   │  @archie/path (APath Queries)    │   │
│   └──────────────────────────────────┘   │
│                                          │
│   ┌──────────────────────────────────┐   │
│   │  @archie/template (OPT)          │   │
│   └──────────────────────────────────┘   │
│                                          │
└─────────────────────────────────────────┘

Tree-Shakable: Only import what you need!
```

## Build Pipeline

```
Source Code (TypeScript)
├── src/parser/*.ts
├── src/aom/*.ts
├── src/validation/*.ts
└── src/rm/*.ts
        ↓
    TypeScript Compiler (tsc)
        ↓
    JavaScript + Type Definitions
        ↓
    Vite/Rollup Bundler
    - Tree-shaking
    - Minification  
    - Code splitting
        ↓
    dist/
    ├── index.js (ES Module)
    ├── index.cjs (CommonJS)
    ├── index.d.ts (Types)
    ├── parser/ (Submodule)
    ├── aom/ (Submodule)
    └── ... (other modules)
        ↓
    npm publish
        ↓
    @openehr/archie@1.0.0
```

## Browser Integration

```
┌─────────────────────────────────────────┐
│         Web Application                  │
│                                          │
│  import { ADLParser, Validator }         │
│    from '@openehr/archie';               │
│                                          │
│  const parser = new ADLParser();         │
│  const archetype = parser.parse(adl);    │
│  const result = validator.validate(...); │
└────────────┬────────────────────────────┘
             │
             ↓
┌────────────────────────────────────────┐
│   @openehr/archie (ES Module)          │
│                                        │
│   ┌──────────────────────────────┐    │
│   │  Parser Module (55 KB gz)    │    │
│   │  - ANTLR4-ts runtime         │    │
│   │  - ADL grammar               │    │
│   └──────────────────────────────┘    │
│                                        │
│   ┌──────────────────────────────┐    │
│   │  AOM Module (40 KB gz)       │    │
│   │  - Archetype classes         │    │
│   │  - CObject hierarchy         │    │
│   └──────────────────────────────┘    │
│                                        │
│   ┌──────────────────────────────┐    │
│   │  Validator (95 KB gz)        │    │
│   │  - Constraint checking       │    │
│   │  - Rules engine             │    │
│   └──────────────────────────────┘    │
└────────────────────────────────────────┘
```

## Development Workflow

```
Developer writes TypeScript
        ↓
    VS Code / IntelliJ
    - Type checking (instant)
    - Autocomplete
    - Refactoring tools
        ↓
    Save file
        ↓
    Vite HMR (<100ms)
        ↓
    Browser updates (instant)
        ↓
    Test in browser DevTools
    - Source maps work
    - TypeScript visible
    - Easy debugging
```

## Deployment Options

```
Option 1: npm Package
┌────────────────────────┐
│ npm install            │
│ @openehr/archie        │
└───────────┬────────────┘
            │
            ↓
    Application bundles it
    (Webpack/Vite/Rollup)

Option 2: CDN
┌────────────────────────┐
│ <script type="module"> │
│ import {...} from      │
│ 'https://cdn.../archie'│
└────────────────────────┘
```

## Type Safety Example

```typescript
// Strong typing throughout

interface Archetype {
  archetypeId: ArchetypeId;
  definition: CComplexObject;
  terminology: ArchetypeTerminology;
}

// IDE knows all properties
const archetype: Archetype = parser.parse(adl);
archetype.terminology.// <-- autocomplete!

// Compile-time errors
archetype.invalidProperty; // ❌ Error!

// Null safety
const id: string = archetype.archetypeId.value ?? 'default';
```

---
**Last Updated**: October 2025
