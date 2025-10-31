# Strategy 1: Java-to-WebAssembly (WASM) Conversion

## Executive Summary

Convert Archie Java library to WebAssembly using automated compilation tools, preserving the existing Java codebase while enabling browser execution. Primary candidates: TeaVM and CheerpJ, with GraalVM as future option.

**Critical Issue**: Archie uses Java reflection extensively (`ReflectionModelInfoLookup` for RM/AOM introspection). This affects compiler choice significantly.

**Recommendation**: 
- **CheerpJ** for immediate production use (full reflection support, commercial license)
- **TeaVM** for open-source approach (requires 2-4 weeks to implement metadata generation workaround)

## Architecture Overview

### High-Level Design

```
┌─────────────────────────────────────────────────────────┐
│                    Browser Environment                   │
│                                                          │
│  ┌────────────────────────────────────────────────┐    │
│  │          JavaScript Application Layer           │    │
│  │   (UI, API calls, initialization code)         │    │
│  └─────────────────┬──────────────────────────────┘    │
│                    │                                     │
│                    │ JavaScript Interop API              │
│                    ↓                                     │
│  ┌────────────────────────────────────────────────┐    │
│  │         WASM Module (Archie Core)              │    │
│  │  ┌──────────────────────────────────────────┐  │    │
│  │  │  Archie Java Code (compiled to WASM)      │  │    │
│  │  │  - ADL Parser (ANTLR runtime)            │  │    │
│  │  │  - AOM (Archetype Object Model)          │  │    │
│  │  │  - RM (Reference Model)                  │  │    │
│  │  │  - Validation Engine                     │  │    │
│  │  └──────────────────────────────────────────┘  │    │
│  │  ┌──────────────────────────────────────────┐  │    │
│  │  │  Java Runtime (minimal, embedded)         │  │    │
│  │  └──────────────────────────────────────────┘  │    │
│  └────────────────────────────────────────────────┘    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Component Breakdown

1. **WASM Core Module**
   - Compiled Archie Java bytecode
   - Embedded Java runtime (GC, threading support)
   - ANTLR parser runtime
   - All dependencies bundled

2. **JavaScript Bridge Layer**
   - Initialization and bootstrap code
   - Type marshalling (Java ↔ JavaScript)
   - API exposure for external use
   - Memory management coordination

3. **Build Pipeline**
   - Gradle plugin integration
   - WASM compilation step
   - Optimization passes
   - Bundle generation

## Technology Stack

### Compiler Options

#### Option A: TeaVM (Recommended for OSS)

**Core Components**:
- TeaVM Gradle Plugin v0.10.x
- WASM backend (experimental but functional)
- JavaScript bridge generator

**Build Configuration**:
```gradle
plugins {
    id 'org.teavm' version '0.10.0'
}

teavm {
    wasm {
        outFile = 'archie.wasm'
        targetType = 'WASM'
        minifying = true
        optimization = 'FULL'
    }
}
```

**Runtime Requirements**:
- TeaVM WASM runtime (~50KB)
- Browser with WebAssembly support

**Reflection Support**: ⚠️ **Limited**
- TeaVM supports basic reflection (getClass, Class.forName)
- Does NOT support runtime class scanning/discovery
- Reflections library (used by Archie) will NOT work
- **Impact on Archie**: See "Reflection Usage in Archie" section below

#### Option B: CheerpJ (Recommended for Enterprise)

**Core Components**:
- CheerpJ Compiler (commercial license)
- Full JVM emulation in browser
- Automatic JavaScript bindings

**Build Integration**:
```bash
/opt/cheerp/bin/cheerpjfy.py archie-all.jar
```

**Runtime Requirements**:
- CheerpJ runtime library (~200KB compressed)
- JVM emulation layer

**Reflection Support**: ✅ **Full**
- CheerpJ includes complete JVM emulation
- Full reflection support including runtime class scanning
- Reflections library works as-is
- **Impact on Archie**: No code changes needed for reflection

#### Option C: GraalVM (Future Consideration)

**Status**: Not production-ready for WASM as of 2025
**Timeline**: Monitor for updates in 2026+

### Build Tools

- **Primary**: Gradle 8.x
- **WASM Toolchain**: Emscripten (for optimizations)
- **Testing**: Selenium WebDriver for browser testing
- **CI/CD**: GitHub Actions with WASM caching

## Reflection Usage in Archie (Critical Issue)

### Overview

Archie makes **heavy use of Java reflection** for the Reference Model (RM) and Archetype Object Model (AOM) introspection. This is a **critical compatibility concern** for WASM compilation.

### Where Archie Uses Reflection

1. **`ReflectionModelInfoLookup` class** (aom/src/main/java/com/nedap/archie/rminfo/)
   - **Purpose**: Scans Java packages to discover RM/AOM classes at runtime
   - **Used by**: ArchieRMInfoLookup, ArchieAOMInfoLookup
   - **Reflection APIs used**:
     - `Reflections` library (org.reflections) for package scanning
     - `Class.forName()` for dynamic class loading
     - `getDeclaredFields()` for field introspection
     - `getDeclaredMethods()` for method introspection
     - `getInterfaces()` and `getSuperclass()` for type hierarchy

2. **Validation Engine**
   - Uses reflection to access RM object properties
   - Invokes getter methods dynamically
   - Inspects class annotations (`@Invariant`)

3. **Dynamic Object Creation**
   - Creates RM instances based on archetype constraints
   - Uses reflection to set field values

4. **Type Information System**
   - Builds runtime metadata about RM/AOM classes
   - Maps archetype type names to Java classes
   - Discovers attributes and their types

### Reflection Support by WASM Compiler

| Compiler | Reflection Support | Archie Compatibility | Mitigation Required |
|----------|-------------------|----------------------|---------------------|
| **TeaVM** | ⚠️ Partial | ❌ **Will NOT work as-is** | 🔴 **High effort** required |
| **CheerpJ** | ✅ Full | ✅ **Works as-is** | ✅ None needed |
| **JWebAssembly** | ❌ Minimal | ❌ **Will NOT work** | 🔴 **Very high effort** |
| **GraalVM** | ⚠️ Limited | ⚠️ Unknown (experimental) | 🟡 TBD |

### TeaVM Reflection Limitations

**What TeaVM Supports**:
- `obj.getClass()` - Getting class of an instance
- `Class.forName()` - Loading known classes (compile-time registered)
- Basic `instanceof` checks

**What TeaVM Does NOT Support**:
- ❌ Runtime package scanning (`Reflections` library)
- ❌ `getDeclaredMethods()` / `getDeclaredFields()` (runtime introspection)
- ❌ Dynamic method invocation via `Method.invoke()`
- ❌ Annotation processing at runtime
- ❌ ClassLoader-based discovery

**Impact**: Archie's `ReflectionModelInfoLookup` will **completely fail** with TeaVM.

### Mitigation Strategies for TeaVM

If using TeaVM, Archie code must be **significantly modified**:

#### Option 1: Pre-compiled Metadata (Recommended for TeaVM)

**Approach**: Generate metadata at build time instead of runtime reflection

```java
// Instead of runtime scanning with Reflections library
public class PrecompiledArchieRMInfoLookup extends ModelInfoLookup {
    public PrecompiledArchieRMInfoLookup() {
        // Manually register all RM classes (generated at build time)
        addClass(Composition.class);
        addClass(Observation.class);
        addClass(Element.class);
        // ... all RM classes (100+)
        
        // Manually register attributes (generated)
        addAttribute("Composition", "content", List.class, Entry.class);
        // ... all attributes
    }
}
```

**Build Process**:
1. Annotation processor scans Java code at compile time
2. Generates `PrecompiledArchieRMInfoLookup.java`
3. Replaces `ReflectionModelInfoLookup` in WASM build

**Effort**: 
- Initial: 2-4 weeks to create annotation processor
- Ongoing: Automatic (regenerates on build)

**Pros**:
- Works with TeaVM
- Potentially faster than runtime reflection
- Bundle may be smaller (no Reflections library)

**Cons**:
- Requires build-time code generation
- More complex build process
- Different code path for JVM vs WASM

#### Option 2: Static Registration

**Approach**: Manually register classes (simpler but more manual)

```java
public class StaticArchieRMInfoLookup extends ModelInfoLookup {
    public StaticArchieRMInfoLookup() {
        // Manually list all classes
        registerClass(Composition.class);
        registerClass(Observation.class);
        // ...
    }
}
```

**Effort**: 
- Initial: 1-2 weeks
- Ongoing: Manual updates when RM changes

**Pros**:
- Simple implementation
- No build tooling needed

**Cons**:
- Error-prone (easy to forget classes)
- Maintenance burden
- Manual sync with RM changes

#### Option 3: Hybrid Approach

**Approach**: Use reflection on JVM, pre-compiled metadata for WASM

```java
public class HybridInfoLookup extends ModelInfoLookup {
    public HybridInfoLookup() {
        if (Platform.isWasm()) {
            loadPrecompiledMetadata();
        } else {
            useReflectionScanning();
        }
    }
}
```

**Effort**: 2-3 weeks

**Pros**:
- Best of both worlds
- JVM keeps flexibility
- WASM gets working solution

**Cons**:
- Two code paths to maintain
- Testing complexity

### CheerpJ: No Mitigation Needed

CheerpJ includes **full JVM emulation**, so:
- ✅ Reflections library works unchanged
- ✅ All reflection APIs supported
- ✅ No code modifications needed
- ✅ Archie compiles and runs as-is

**Tradeoff**: Larger bundle size due to JVM emulation (~1-2MB extra)

### Recommendation Update Based on Reflection

**Original Recommendation**: TeaVM for open-source
**Revised Recommendation**: 

1. **For quick POC/MVP**: **CheerpJ**
   - Works immediately with no code changes
   - Accept commercial license cost
   - Accept larger bundle size
   - Fastest path to working browser version

2. **For production (open-source)**: **TeaVM + Pre-compiled Metadata**
   - Invest 2-4 weeks in build-time metadata generation
   - Smaller bundle than CheerpJ
   - Free and open source
   - One-time engineering investment

3. **For long-term**: **TypeScript Rewrite**
   - No reflection issues (design for JavaScript from start)
   - Smallest bundles
   - Best performance
   - Most maintainable

### POC Impact

The **POC plan must validate reflection workarounds**:

**TeaVM POC** (updated scope):
- Week 1-2: Setup + attempt compilation
- Week 3: **Implement pre-compiled metadata for ADL parser subset**
- Week 4: Test and validate approach
- **Success criteria includes**: Working metadata generation

**CheerpJ POC**:
- No changes needed to scope
- Reflection will work out of the box

## Build and Deployment Pipeline

### Development Workflow

```
┌─────────────┐      ┌──────────────┐      ┌───────────────┐
│ Java Source │─────>│ Gradle Build │─────>│ WASM Compiler │
└─────────────┘      └──────────────┘      └───────┬───────┘
                                                    │
                                                    ↓
┌─────────────┐      ┌──────────────┐      ┌───────────────┐
│   Browser   │<─────│ npm Package  │<─────│ Optimization  │
└─────────────┘      └──────────────┘      └───────────────┘
```

### Build Steps

1. **Compilation** (5-10 minutes)
   ```bash
   ./gradlew teavmWasm  # or CheerpJ equivalent
   ```

2. **Optimization** (2-5 minutes)
   ```bash
   wasm-opt -O3 archie.wasm -o archie.optimized.wasm
   ```

3. **Packaging** (1 minute)
   ```bash
   npm run bundle  # Create distributable package
   ```

4. **Testing** (5-15 minutes)
   ```bash
   npm test  # Run browser-based tests
   ```

### Deployment Artifacts

- `archie.wasm` - Main WebAssembly module (estimated 2-5MB)
- `archie.js` - JavaScript loader and bridge (50-100KB)
- `archie.d.ts` - TypeScript type definitions
- `package.json` - npm package metadata

## Module Structure and Loading Strategy

### Entry Points

```javascript
// Lazy loading example
import init, { ArchieAPI } from '@openehr/archie-wasm';

// Initialize WASM module
await init();

// Use Archie API
const archie = new ArchieAPI();
const archetype = archie.parseADL(adlString);
```

### Modular Loading (Challenging with WASM)

**Issue**: WASM bundles typically monolithic
**Solutions**:
1. **Code Splitting at Build**: Separate WASM modules for different features
   - `archie-parser.wasm` - ADL parsing only
   - `archie-validation.wasm` - Validation engine
   - `archie-full.wasm` - Complete functionality

2. **Lazy Initialization**: Load full module but expose APIs progressively
   ```javascript
   // Light initialization
   const parser = await archie.getParser();
   
   // Heavy features loaded on-demand
   const validator = await archie.getValidator(); // triggers additional init
   ```

## Performance Characteristics

### Startup Performance

**TeaVM**:
- **Initial Load**: 2-5 MB WASM download
- **Parse/Compile Time**: 100-500ms (browser JIT)
- **Initialization**: 50-200ms
- **Total Time to Interactive**: 1-2 seconds (fast connection)

**CheerpJ**:
- **Initial Load**: 3-8 MB (includes JVM)
- **Parse/Compile Time**: 200-800ms
- **Initialization**: 200-500ms (JVM startup)
- **Total Time to Interactive**: 2-3 seconds

### Runtime Performance

**Expected**:
- ADL Parsing: 80-95% of Java performance
- Validation: 70-90% of Java performance
- Object manipulation: 60-80% of Java performance

**Optimization Opportunities**:
- WASM SIMD instructions for data processing
- Shared memory for large data structures
- Ahead-of-time compilation with GraalVM (when available)

### Memory Footprint

**Baseline**:
- WASM Module: 30-50 MB (runtime)
- Heap: 64-256 MB (configurable)
- JavaScript Bridge: 5-10 MB
- **Total**: 100-300 MB typical usage

**Comparison to JavaScript**:
- WASM: Higher initial memory but predictable
- JavaScript/TypeScript: Lower initial but variable GC

## Bundle Size Analysis

### TeaVM Estimates:

| Component | Size (uncompressed) | Size (gzip) |
|-----------|---------------------|-------------|
| Core WASM | 3-5 MB | 1-2 MB |
| TeaVM Runtime | 200 KB | 50 KB |
| JavaScript Bridge | 100 KB | 30 KB |
| **Total** | **3.3-5.3 MB** | **1.1-2.1 MB** |

### CheerpJ Estimates:

| Component | Size (uncompressed) | Size (gzip) |
|-----------|---------------------|-------------|
| Core WASM | 4-6 MB | 1.5-2.5 MB |
| CheerpJ Runtime | 800 KB | 200 KB |
| JVM Emulation | 1-2 MB | 300-600 KB |
| **Total** | **5.8-8.8 MB** | **2-3.3 MB** |

### Optimization Strategies:
1. **Tree Shaking**: Difficult with WASM, limited effectiveness
2. **Dead Code Elimination**: Compiler-level optimizations
3. **Compression**: Brotli (better than gzip for WASM)
4. **CDN Caching**: First load slow, subsequent loads instant

## Browser Compatibility

### Minimum Requirements

- **WebAssembly Support**: Chrome 57+, Firefox 52+, Safari 11+, Edge 16+
- **JavaScript ES6**: For bridge code
- **Fetch API**: For loading WASM module

### Polyfills Needed:
- ❌ None for modern browsers (2020+)
- ⚠️ WebAssembly polyfill for older browsers (not recommended)

### Browser Support Matrix:

| Browser | Version | Status | Notes |
|---------|---------|--------|-------|
| Chrome | 90+ | ✅ Excellent | Best performance |
| Firefox | 88+ | ✅ Excellent | Good debugging tools |
| Safari | 14+ | ✅ Good | Slightly slower |
| Edge | 90+ | ✅ Excellent | Chromium-based |
| Mobile Chrome | 90+ | ✅ Good | Bundle size concern |
| Mobile Safari | 14+ | ✅ Good | Memory limits |
| IE 11 | N/A | ❌ Not Supported | No WASM support |

### Progressive Enhancement:
```javascript
if (!WebAssembly) {
    // Fallback: Server-side processing or error message
    showUnsupportedBrowserMessage();
} else {
    // Load WASM module
    initArchieWASM();
}
```

## Maintainability Analysis

### Update Process from Upstream Archie

**Workflow**:
1. Merge upstream openEHR/archie changes
2. Rebuild WASM module
3. Test in browser environment
4. Deploy new version

**Frequency**: Aligned with Archie releases (quarterly typical)

### Code Maintainability

**Pros**:
- ✅ Original Java code unchanged
- ✅ No need to learn new language
- ✅ Same IDE and tools as Java development

**Cons**:
- ⚠️ WASM-specific issues require specialized knowledge
- ⚠️ Debugging more complex than native JavaScript
- ⚠️ Performance tuning different from Java

### Team Structure:
- Java developers: Maintain core Archie logic (no change)
- 1-2 WASM specialists: Handle compilation and optimization
- Frontend developers: JavaScript bridge and integration

## Skill Requirements

### Development Team Needs:

1. **Java Developers** (existing team)
   - No additional training needed
   - Continue normal Archie development

2. **WASM Integration Specialist** (1-2 people)
   - **Required Skills**:
     - WebAssembly fundamentals
     - TeaVM or CheerpJ experience
     - Build system expertise (Gradle)
   - **Training Time**: 2-4 weeks
   - **Availability**: Moderate (growing field)

3. **Frontend Integration** (1-2 people)
   - **Required Skills**:
     - JavaScript/TypeScript
     - WASM JavaScript API
     - npm package development
   - **Training Time**: 1-2 weeks
   - **Availability**: High

### Learning Curve:
- **Java Team**: ✅ Low (no change)
- **WASM Team**: ⚠️ Medium (new technology)
- **Frontend Team**: ✅ Low-Medium (standard web dev)

## Development Timeline

### Phase 1: Setup and POC (2-3 months)
- Week 1-2: Environment setup, tool evaluation
- Week 3-6: Basic ADL parser compilation
- Week 7-10: Integration testing and optimization
- Week 11-12: POC demo and review

### Phase 2: Core Features (2-3 months)
- Month 1: Full AOM compilation
- Month 2: Validation engine
- Month 3: Testing and bug fixes

### Phase 3: Production Readiness (2-3 months)
- Month 1: Performance optimization
- Month 2: Documentation and examples
- Month 3: Production deployment and monitoring

**Total Timeline**: 6-9 months to production

### Resource Requirements:
- 1 Senior Java Developer (50% time)
- 1 WASM Specialist (100% time)
- 1 Frontend Developer (50% time)
- 1 QA Engineer (25% time)

## Cost Analysis

### TeaVM (Open Source):

**Development Costs**:
- Engineering: 6-9 months × 2.75 FTE = 16-25 person-months
- At $150K/year avg: $200K-$310K

**Tooling Costs**:
- TeaVM: Free (Apache License)
- Build infrastructure: $100-200/month
- Testing services: $200-500/month

**Ongoing Maintenance**:
- 0.5-1 FTE ongoing: $75K-$150K/year

**Total Year 1**: $220K-$330K

### CheerpJ (Commercial):

**Development Costs**:
- Engineering: 4-6 months × 2.75 FTE = 11-17 person-months
- At $150K/year avg: $138K-$212K

**Tooling Costs**:
- CheerpJ License: $5K-20K/year (estimate, contact vendor)
- Build infrastructure: $100-200/month
- Support contract: $5K-15K/year (optional)

**Ongoing Maintenance**:
- 0.25-0.5 FTE ongoing: $38K-$75K/year
- Annual license renewal: $5K-20K/year

**Total Year 1**: $155K-$245K + licensing

### 5-Year TCO:

**TeaVM**:
- Year 1: $220K-$330K
- Years 2-5: $300K-$600K (maintenance)
- **Total**: $520K-$930K

**CheerpJ**:
- Year 1: $155K-$265K
- Years 2-5: $170K-$380K + licensing
- **Total**: $325K-$645K + $20K-80K licensing

## Risks and Limitations

### Technical Risks

1. **Reflection Compatibility** (🔴 **CRITICAL** for TeaVM, ✅ Resolved for CheerpJ)
   - **Risk**: Archie's ReflectionModelInfoLookup will not work with TeaVM
   - **Impact**: Core functionality broken without code changes
   - **Mitigation (TeaVM)**: 
     - Implement build-time metadata generation (2-4 weeks effort)
     - OR use static class registration (1-2 weeks, ongoing maintenance)
   - **Mitigation (CheerpJ)**: None needed - full reflection support
   - **Validation**: MUST be tested in POC phase

2. **Bundle Size** (🔴 High)
   - **Risk**: 2-5 MB initial download may be unacceptable
   - **Mitigation**: CDN caching, code splitting exploration, accept limitation

2. **Performance** (🟡 Medium)
   - **Risk**: WASM may be slower than native JavaScript for some operations
   - **Mitigation**: Benchmark early, optimize hot paths, accept tradeoff

3. **Debugging Complexity** (🟡 Medium)
   - **Risk**: WASM debugging harder than JavaScript
   - **Mitigation**: Invest in tooling, source maps, logging infrastructure

4. **Browser Compatibility** (🟢 Low)
   - **Risk**: Older browsers don't support WASM
   - **Mitigation**: Define minimum browser requirements, provide fallback

### Business Risks

1. **Vendor Lock-in** (CheerpJ)
   - **Risk**: Dependency on commercial vendor
   - **Mitigation**: Ensure source code independence, evaluate alternatives

2. **Technology Maturity**
   - **Risk**: WASM ecosystem still evolving
   - **Mitigation**: Conservative technology choices, monitoring updates

3. **Skill Availability**
   - **Risk**: WASM specialists harder to hire than JavaScript developers
   - **Mitigation**: Training existing team, remote hiring

### Limitations

❌ **Cannot Support**:
- Dynamic class loading (WASM limitation)
- Some reflection use cases
- Very fine-grained module loading

⚠️ **Challenging**:
- Mobile devices (bundle size, memory)
- Offline-first applications (large download)
- Real-time performance requirements

## Proof-of-Concept Plan

### POC Objectives

1. **Validate reflection workaround** for ReflectionModelInfoLookup (CRITICAL)
2. Validate TeaVM can compile Archie ADL parser
3. Measure bundle size and load time
4. Benchmark parsing performance
5. Demonstrate JavaScript interop
6. Evaluate developer experience

### POC Scope

**In Scope**:
- ADL 2 parser compilation
- **Build-time metadata generation for RM classes** (reflection workaround)
- Parse sample archetypes
- Expose parsing API to JavaScript
- Basic error handling

**Out of Scope**:
- Full AOM implementation
- Validation engine
- Operational templates
- Production optimization

### POC Timeline: 5-7 weeks (updated for reflection work)

**Week 1**: Environment setup
- Install TeaVM Gradle plugin
- Configure minimal Archie subset
- Identify reflection usage in parser

**Week 2**: Reflection mitigation
- Implement annotation processor for metadata generation
- Generate static class registry for parser subset
- Test build-time code generation

**Week 3**: WASM compilation
- Compile parser with pre-generated metadata
- Verify TeaVM accepts modified code
- Initial WASM module generation

**Week 4**: Integration
- Create JavaScript bridge
- Load and initialize in browser
- Parse sample ADL files

**Week 5**: Testing
- Parse multiple archetypes
- Validate metadata completeness
- Compare with Java behavior

**Week 6-7**: Evaluation
- Performance benchmarks
- Bundle size analysis
- Documentation
- Decision recommendation

### Success Criteria

✅ **Must Have**:
- **Reflection workaround works correctly** (validates RM classes without runtime scanning)
- Successfully parse ADL 2 archetype in browser
- Bundle size < 3 MB (gzipped)
- Parse time within 2x of Java performance
- No critical compilation errors

⚠️ **Should Have**:
- Bundle size < 2 MB (gzipped)
- Parse time within 1.5x of Java
- Clear error messages in browser

🎯 **Nice to Have**:
- Bundle size < 1.5 MB (gzipped)
- Parse time equivalent to Java
- Source maps working in browser DevTools

### POC Deliverables

1. Working demo page (parsing ADL in browser)
2. Performance benchmark report
3. Bundle size analysis
4. Developer experience assessment
5. Recommendation: Go/No-Go for full implementation

---

**Last Updated**: October 2025
**Status**: Strategy Analysis Complete
**Next Step**: POC Development (if approved)
