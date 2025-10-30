# Strategy 2: Kotlin/JS Transpilation

## Executive Summary

Convert Archie from Java to Kotlin, then compile to JavaScript using Kotlin/JS compiler. This strategy requires a full Java→Kotlin conversion before browser deployment becomes possible.

**Recommendation**: ⚠️ Not recommended for Archie unless there's a broader organizational strategy to adopt Kotlin for JVM development.

**Key Challenge**: Kotlin/JS compiles **Kotlin code**, not Java code - requiring complete codebase conversion first.

## Architecture Overview

```
┌──────────────────────────────────────────────────────────┐
│                    Browser Environment                    │
│                                                           │
│  ┌─────────────────────────────────────────────────────┐ │
│  │          JavaScript Application Layer                │ │
│  │     (UI, API calls, initialization code)            │ │
│  └──────────────────┬──────────────────────────────────┘ │
│                     │ JavaScript API                      │
│                     ↓                                     │
│  ┌─────────────────────────────────────────────────────┐ │
│  │    Archie-Kotlin/JS Module (ES6/CommonJS/UMD)       │ │
│  │  ┌──────────────────────────────────────────────┐   │ │
│  │  │  Archie Logic (Kotlin → JavaScript)          │   │ │
│  │  │  - ADL Parser                                │   │ │
│  │  │  - AOM Classes                               │   │ │
│  │  │  - Validation Engine                         │   │ │
│  │  │  - RM Implementation                         │   │ │
│  │  └──────────────────────────────────────────────┘   │ │
│  │  ┌──────────────────────────────────────────────┐   │ │
│  │  │  Kotlin Standard Library (JS subset ~200KB)  │   │ │
│  │  └──────────────────────────────────────────────┘   │ │
│  └─────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### Two-Phase Conversion

**Phase 1**: Java → Kotlin (JVM)
- Convert all Java code to Kotlin
- Maintain JVM compatibility
- Update build system
- Extensive testing

**Phase 2**: Kotlin → JavaScript
- Configure Kotlin/JS compiler
- Handle JavaScript interop
- Bundle for browser
- Browser-specific testing

## Technology Stack

### Core Components
- **Kotlin**: 1.9+ (with IR backend)
- **Kotlin/JS Plugin**: Gradle plugin
- **Webpack/Rollup**: JavaScript bundling
- **npm**: Package management
- **TypeScript Definitions**: For type safety in consuming apps

### Build Configuration

```kotlin
// build.gradle.kts
plugins {
    kotlin("multiplatform") version "1.9.20"
}

kotlin {
    js(IR) {
        browser {
            commonWebpackConfig {
                cssSupport {
                    enabled.set(true)
                }
            }
        }
        binaries.executable()
    }
    
    jvm {
        // JVM target for server-side use
    }
    
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation(kotlin("stdlib-common"))
            }
        }
        val jsMain by getting {
            dependencies {
                implementation(kotlin("stdlib-js"))
                implementation(npm("antlr4", "4.13.0"))
            }
        }
        val jvmMain by getting {
            dependencies {
                implementation(kotlin("stdlib-jdk8"))
            }
        }
    }
}
```

## Build and Deployment Pipeline

### Development Workflow

```
Java Source (Archie) → [Convert] → Kotlin Source
                                          ↓
                                    Kotlin/JVM ← test on JVM
                                          ↓
                                    Kotlin/JS Compiler
                                          ↓
                                    JavaScript Bundle
                                          ↓
                                    npm Package
```

### Conversion Process

1. **Automated Conversion** (2-3 months)
   ```bash
   # Use IntelliJ IDEA Kotlin converter
   # Or: https://github.com/JetBrains/kotlin/tree/master/j2k
   ```
   - Converts Java syntax to Kotlin
   - Requires manual review and fixes
   - ~70-80% automated, 20-30% manual

2. **Manual Refinement** (2-4 months)
   - Fix conversion issues
   - Update to idiomatic Kotlin
   - Handle Java-specific patterns
   - Update tests

3. **Kotlin/JS Configuration** (1-2 months)
   - Set up multiplatform project
   - Configure JS compilation
   - Handle JavaScript interop
   - Create browser bundles

## Module Structure

### Multiplatform Architecture

```
archie-kotlin/
├── commonMain/           # Shared Kotlin code
│   ├── aom/             # AOM classes (works on JVM & JS)
│   ├── rm/              # RM implementation
│   └── validation/      # Validation logic
├── jvmMain/             # JVM-specific code
│   ├── parser/          # ANTLR parser (JVM)
│   └── utils/           # JVM utilities
└── jsMain/              # JS-specific code
    ├── parser/          # ANTLR JS equivalent
    ├── interop/         # JavaScript bridge
    └── utils/           # Browser-specific utilities
```

### Loading Strategy

```javascript
// ES6 module import
import { ArchieKt } from '@openehr/archie-kotlin';

// Initialize
const archie = new ArchieKt();
const archetype = archie.parseADL(adlString);
```

## Performance Characteristics

### Compilation Performance
- **Kotlin/JS Compilation**: 1-3 minutes (incremental: 10-30 seconds)
- **Webpack Bundling**: 30-60 seconds
- **Development HMR**: <5 seconds

### Runtime Performance
- **Parsing**: 90-100% of Java performance (depends on ANTLR JS)
- **Object Operations**: 85-95% of Java
- **Validation**: 80-90% of Java

### Bundle Size Estimates

| Component | Size (uncompressed) | Size (gzip) |
|-----------|---------------------|-------------|
| Kotlin stdlib-js | 500 KB | 130 KB |
| Archie logic | 800 KB - 1.2 MB | 200-300 KB |
| ANTLR runtime | 150 KB | 40 KB |
| Dependencies | 200-400 KB | 50-100 KB |
| **Total** | **1.6-2.3 MB** | **420-570 KB** |

**Comparison**:
- Smaller than WASM (1-2MB gzipped)
- Larger than pure TypeScript (300-600KB est.)

## Browser Compatibility

### Requirements
- **ES6+ Support**: Chrome 61+, Firefox 60+, Safari 11+, Edge 79+
- **Or ES5 Transpilation**: Add Babel for older browsers

### Polyfills
- Minimal (Kotlin stdlib handles most differences)
- May need Promise polyfill for very old browsers

## Maintainability Analysis

### Dual Codebase Challenge

**Scenario 1: Fork from openEHR/archie**
- Convert Java Archie → Kotlin Archie
- Maintain separate Kotlin fork
- **Problem**: Syncing updates from Java upstream difficult
- **Effort**: High ongoing maintenance burden

**Scenario 2: Convince openEHR to adopt Kotlin**
- Upstream project converts to Kotlin
- Everyone uses Kotlin for both JVM and JS
- **Problem**: Requires community consensus
- **Effort**: Political challenge, high initial conversion

**Scenario 3: Wrapper Approach**
- Keep Java Archie for JVM
- Write Kotlin/JS wrapper/facade
- **Problem**: Duplicate logic, not full Archie
- **Effort**: Medium but incomplete solution

### Update Process

**If Maintaining Fork**:
1. Monitor openEHR/archie releases
2. Cherry-pick relevant changes
3. Convert changes to Kotlin
4. Test both JVM and JS targets
5. Release updated version

**Frequency**: Quarterly (or as needed)
**Risk**: Drift from upstream over time

## Skill Requirements

### Development Team Needs

1. **Kotlin Developers** (2-3 people)
   - Strong Kotlin knowledge
   - Java background helpful
   - Multiplatform experience
   - **Availability**: Growing but less than Java/TypeScript
   - **Training**: 1-2 months for Java developers

2. **Kotlin/JS Specialist** (1 person)
   - Kotlin/JS specifics
   - JavaScript interop
   - Browser deployment
   - **Availability**: Rare
   - **Training**: 2-3 months

3. **Frontend Integration** (1-2 people)
   - JavaScript/TypeScript
   - npm ecosystem
   - **Availability**: High

### Learning Curve
- **Java → Kotlin**: ✅ Low-Medium (2-4 weeks)
- **Kotlin/JS specifics**: ⚠️ Medium (4-8 weeks)
- **Overall**: ⚠️ Medium (Higher than pure TS, lower than WASM)

## Development Timeline

### Phase 1: Java → Kotlin Conversion (4-6 months)
- **Month 1-2**: Automated conversion + initial fixes
- **Month 3-4**: Manual refinement, idiom improvements
- **Month 5-6**: Testing, validation, JVM parity

### Phase 2: Kotlin/JS Setup (2-3 months)
- **Month 1**: Multiplatform configuration
- **Month 2**: JavaScript compilation and testing
- **Month 3**: Browser integration, npm packaging

### Phase 3: Production (1-2 months)
- **Month 1**: Performance optimization
- **Month 2**: Documentation, deployment

**Total Timeline**: 7-11 months

### Resource Requirements
- 2 Senior Kotlin Developers (100% time, 6 months)
- 1 Kotlin/JS Specialist (100% time, 3 months)
- 1 Frontend Developer (50% time, 2 months)
- 1 QA Engineer (25% time, ongoing)

## Cost Analysis

### Development Costs

**Java → Kotlin Conversion**:
- 2 Kotlin Devs × 6 months = 12 person-months
- Cost: $150K-$200K

**Kotlin/JS Implementation**:
- 1 Specialist × 3 months = 3 person-months
- 1 Frontend × 1 month (50%) = 0.5 person-months
- Cost: $40K-$60K

**Testing & QA**:
- 1 QA × 3 months (25%) = 0.75 person-months
- Cost: $10K-$15K

**Total Development**: $200K-$275K

### Tooling Costs
- IntelliJ IDEA licenses: $150-300/year/developer
- CI/CD: $200-500/month
- **Annual**: $3K-$6K

### Ongoing Maintenance

**If Forked**:
- 1-1.5 FTE maintaining Kotlin fork: $125K-$190K/year
- Higher than WASM or TypeScript (due to upstream sync)

**If Upstream Adopts Kotlin**:
- 0.5 FTE for JS-specific work: $63K-$95K/year

### 5-Year TCO

**Forked Approach**:
- Year 1: $200K-$280K
- Years 2-5: $500K-$760K (maintenance)
- **Total**: $700K-$1,040K

**Upstream Adoption** (best case):
- Year 1: $200K-$280K
- Years 2-5: $250K-$380K
- **Total**: $450K-$660K

## Risks and Limitations

### Critical Risks

1. **Upstream Sync Complexity** (🔴 High)
   - **Risk**: Drift from Java Archie over time
   - **Impact**: Missing features, bugs, security issues
   - **Mitigation**: Automated sync tools, dedicated maintainer

2. **Kotlin/JS Limitations** (🟡 Medium)
   - **Risk**: Not all Kotlin features work in JS
   - **Impact**: Code restructuring needed
   - **Mitigation**: Early POC validation

3. **Community Adoption** (🟡 Medium)
   - **Risk**: openEHR community may not embrace Kotlin
   - **Impact**: Less collaboration, fewer contributors
   - **Mitigation**: Strong technical justification

4. **Skill Availability** (🟡 Medium)
   - **Risk**: Kotlin/JS developers harder to find than TS
   - **Impact**: Higher hiring costs, longer onboarding
   - **Mitigation**: Train existing Java developers

### Limitations

❌ **Cannot Avoid**:
- Requires Java → Kotlin conversion (major undertaking)
- Dual maintenance if forked
- Kotlin/JS bundle larger than pure TypeScript

⚠️ **Challenging**:
- Some Java libraries won't work in Kotlin/JS
- Reflection usage needs refactoring
- Debugging across JVM and JS platforms

## Proof-of-Concept Plan

### POC Objectives

1. Validate Java → Kotlin conversion feasibility
2. Test Kotlin/JS compilation of converted code
3. Measure bundle size and performance
4. Assess development workflow

### POC Scope

**In Scope**:
- Convert ADL parser subset to Kotlin
- Compile to JavaScript
- Parse sample archetype in browser
- Measure bundle size

**Out of Scope**:
- Full Archie conversion
- Production optimization
- Complete testing

### POC Timeline: 3-4 weeks

**Week 1**: Convert ADL parser to Kotlin (JVM)
**Week 2**: Configure Kotlin/JS, compile to JavaScript
**Week 3**: Browser integration and testing
**Week 4**: Evaluation and recommendation

### Success Criteria

✅ **Must Have**:
- ADL parser works in both JVM and JavaScript
- Bundle size < 800KB (gzipped)
- Parse performance within 1.5x of Java

⚠️ **Should Have**:
- Conversion mostly automated (>70%)
- Bundle size < 600KB (gzipped)
- Clear path for full conversion

### POC Deliverables

1. Converted Kotlin code (ADL parser subset)
2. Working browser demo
3. Performance and bundle size report
4. Conversion effort analysis
5. Go/No-Go recommendation

## Comparison to Alternatives

### vs. WASM
- **Smaller Bundles**: Kotlin/JS ~500KB vs WASM 1-2MB
- **Better Debugging**: Source maps in JavaScript
- **But**: Requires full conversion vs. compile existing code

### vs. TypeScript
- **Type Safety**: Similar to TypeScript
- **JVM Sharing**: Can share code with JVM (unique benefit)
- **But**: Larger bundles, higher conversion effort

### vs. Keep Java
- **Browser Support**: Enables browser usage
- **Modern Language**: Kotlin more modern than Java
- **But**: Major conversion effort, ongoing dual maintenance

## Recommendation

### When Kotlin/JS Makes Sense

✅ **Choose Kotlin/JS if**:
- Organization is already adopting Kotlin for JVM
- Want to share substantial code between server (JVM) and browser (JS)
- Team has strong Kotlin expertise
- Can convince openEHR community to adopt Kotlin upstream

❌ **Avoid Kotlin/JS if**:
- Goal is just browser support (TypeScript is simpler)
- Need quick delivery (WASM faster with existing Java)
- Limited Kotlin expertise
- openEHR community prefers Java

### For Archie Specifically

**Verdict**: ⚠️ **Not Recommended**

**Reasoning**:
1. **High Effort**: Full Java→Kotlin conversion is 4-6 months before even starting browser work
2. **Maintenance Burden**: Maintaining Kotlin fork separate from Java Archie is ongoing challenge
3. **Better Alternatives**:
   - WASM (TeaVM) for quick delivery with existing Java
   - TypeScript for best long-term browser-native solution
4. **Community**: openEHR community unlikely to adopt Kotlin for upstream Archie

**Exception**: If Archie team decides to adopt Kotlin for JVM development anyway (unrelated to browser support), then Kotlin/JS becomes attractive for code sharing.

---

**Last Updated**: October 2025
**Status**: Strategy Analysis Complete
**Recommendation**: Not recommended unless broader Kotlin adoption planned
