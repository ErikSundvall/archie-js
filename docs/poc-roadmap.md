# Proof-of-Concept Roadmap

## Overview

POC plans for each conversion strategy to validate feasibility before full implementation commitment.

## POC Strategy: Staged Validation

Recommend executing POCs in priority order, using learnings from each to inform decisions.

### Recommended POC Sequence

1. **TypeScript POC** (3-4 weeks) - Primary recommendation
2. **WASM POC** (4-6 weeks) - Backup/quick delivery option
3. **Kotlin/JS POC** (3-4 weeks) - Only if Kotlin adoption planned

## 1. TypeScript POC (Primary Priority)

### Objectives
1. Validate ANTLR4-ts can handle ADL grammar
2. Implement basic AOM structure in TypeScript
3. Demonstrate parsing sample archetypes
4. Measure bundle size and performance
5. Assess developer experience and tooling

### Scope

**In Scope**:
- Set up TypeScript project (Vite, Vitest, ESLint)
- Integrate ANTLR4-ts parser generator
- Generate TypeScript parser from ADL grammar
- Implement core AOM classes (Archetype, CAttribute, CObject)
- Parse 10 sample ADL archetypes
- Basic browser demo page
- Bundle size analysis
- Performance benchmarks vs. Java Archie

**Out of Scope**:
- Complete AOM implementation
- Reference Model classes
- Validation engine
- Production optimization

### Timeline: 3-4 Weeks

#### Week 1: Setup and Parser Generation
**Days 1-2**: Project scaffold
- Initialize TypeScript project with Vite
- Configure Vitest for testing
- Set up ESLint, Prettier
- Create CI/CD pipeline (GitHub Actions)

**Days 3-5**: ANTLR Integration
- Install ANTLR4-ts
- Obtain ADL grammar from https://github.com/openEHR/adl-antlr
- Generate TypeScript parser from grammar
- Verify parser compiles and imports

#### Week 2: Core Implementation
**Days 6-8**: AOM Classes
- Implement Archetype class
- Implement CAttribute class
- Implement CObject hierarchy (CComplexObject, CPrimitiveObject)
- TypeScript interfaces for core types

**Days 9-10**: Parse Tree → AOM
- Visitor pattern for ANTLR parse tree
- Convert parse tree to AOM objects
- Basic error handling

#### Week 3: Testing and Demo
**Days 11-13**: Test with Sample Archetypes
- Parse 10 different ADL archetypes
- Verify correct AOM structure generated
- Unit tests for core classes
- Handle parse errors gracefully

**Days 14-15**: Browser Demo
- Create simple HTML demo page
- Load archetype from text input
- Display parsed structure
- Show bundle size and load time

#### Week 4: Evaluation
**Days 16-17**: Performance Benchmarks
- Compare parse time to Java Archie
- Memory usage analysis
- Bundle size breakdown
- Profiling hot paths

**Days 18-20**: Documentation & Decision
- Write POC report
- Document findings and recommendations
- Present to stakeholders
- Go/No-Go decision

### Success Criteria

✅ **Must Have** (Go/No-Go):
- Successfully parse valid ADL 2 archetypes
- Bundle size ≤ 150 KB (gzipped, parser only)
- Parse performance within 2x of Java Archie
- Type-safe AOM structure
- No blocking technical issues

⚠️ **Should Have** (Quality indicators):
- Bundle size ≤ 100 KB (gzipped)
- Parse performance within 1.5x of Java
- Clear error messages for invalid ADL
- Good IDE support (autocomplete, type hints)

🎯 **Nice to Have** (Exceeds expectations):
- Bundle size ≤ 75 KB (gzipped)
- Parse performance equal to or better than Java
- Source maps working perfectly
- Excellent documentation

### Deliverables

1. **Working Code**: GitHub repository with TypeScript POC
2. **Demo**: Live browser demo parsing archetypes
3. **Performance Report**:
   - Parse time comparison (TypeScript vs. Java)
   - Memory usage analysis
   - Bundle size breakdown
4. **Developer Experience Assessment**:
   - Ease of development
   - Tooling quality
   - Learning curve
5. **Decision Document**:
   - Go/No-Go recommendation
   - Full implementation roadmap
   - Risk assessment

### Resource Requirements

- 1 Senior TypeScript Developer (100% time, 4 weeks)
- 1 openEHR Expert (25% time, for guidance)
- Access to: ADL grammar files, sample archetypes, Java Archie for comparison

### Budget: $15K-$20K

## 2. WASM (TeaVM) POC (Secondary Priority)

### Objectives
1. Validate TeaVM can compile Archie ADL parser
2. Measure bundle size and load time
3. Test JavaScript interop
4. Assess debugging capabilities
5. Compare performance to Java

### Scope

**In Scope**:
- Set up TeaVM Gradle plugin
- Compile minimal Archie subset (parser + AOM)
- Create JavaScript wrapper/bridge
- Load WASM module in browser
- Parse sample archetype
- Bundle size and performance analysis

**Out of Scope**:
- Full Archie compilation
- Production optimization
- Multiple WASM compilers (stick with TeaVM for POC)

### Timeline: 4-6 Weeks

#### Week 1-2: Setup
- Install and configure TeaVM
- Create minimal Archie subset
- Configure build for WASM target
- First successful WASM compilation

#### Week 3-4: Integration
- Create JavaScript bridge
- Load WASM in browser
- Wire up parser API
- Handle data marshalling

#### Week 5-6: Testing & Analysis
- Parse sample archetypes
- Performance benchmarks
- Bundle size analysis
- Debugging experience assessment
- Documentation and recommendation

### Success Criteria

✅ **Must Have**:
- Successfully compile and load WASM module
- Parse valid ADL archetype in browser
- Bundle size measured and documented
- JavaScript interop working

⚠️ **Should Have**:
- Bundle size ≤ 2 MB (gzipped)
- Parse time within 1.5x of Java
- Clear path to debugging

### Deliverables

1. Working WASM module with demo
2. Bundle size and performance report
3. Developer experience assessment (especially debugging)
4. Go/No-Go recommendation

### Resource Requirements

- 1 Java Developer (50% time, 6 weeks)
- 1 WASM Specialist or willing-to-learn developer (100% time, 6 weeks)
- 1 Frontend Developer (25% time, 2 weeks)

### Budget: $25K-$35K

## 3. Kotlin/JS POC (Conditional Priority)

**Execute only if**: Planning to adopt Kotlin for JVM development.

### Objectives
1. Assess Java → Kotlin conversion effort
2. Validate Kotlin/JS compilation
3. Measure bundle size
4. Test multiplatform feasibility

### Scope

**In Scope**:
- Convert ADL parser subset to Kotlin
- Configure Kotlin multiplatform project
- Compile to both JVM and JavaScript
- Test in browser
- Measure conversion effort and bundle size

**Out of Scope**:
- Full Archie conversion
- Production-ready code

### Timeline: 3-4 Weeks

#### Week 1: Conversion
- Use IntelliJ IDEA to convert ADL parser to Kotlin
- Manual cleanup and refinement
- Ensure compiles on JVM

#### Week 2: Kotlin/JS Setup
- Configure multiplatform project
- Set up JS target
- Compile to JavaScript

#### Week 3: Testing
- Test parser on JVM
- Test in browser
- Performance comparison

#### Week 4: Analysis
- Assess conversion effort
- Bundle size analysis
- Multiplatform viability
- Recommendation

### Success Criteria

✅ **Must Have**:
- Successful Java → Kotlin conversion
- Compiles to both JVM and JS
- Parses archetypes in browser

⚠️ **Should Have**:
- <30% manual effort in conversion
- Bundle size ≤ 600 KB (gzipped)

### Deliverables

1. Converted Kotlin code
2. Multiplatform demo
3. Conversion effort analysis
4. Bundle size report
5. Recommendation

### Resource Requirements

- 1 Kotlin Developer (100% time, 4 weeks)
- 1 Java Developer (25% time, for reference)

### Budget: $15K-$20K

## POC Execution Strategy

### Option A: Sequential POCs

Execute POCs one at a time in priority order:

1. **TypeScript POC** (4 weeks)
   - If success → Proceed to full TypeScript implementation
   - If failure → Execute WASM POC

2. **WASM POC** (6 weeks, if needed)
   - Only if TypeScript POC inconclusive/failed
   - Provides backup option

3. **Kotlin/JS POC** (4 weeks, optional)
   - Only if broader Kotlin adoption

**Timeline**: 4-14 weeks (depending on results)
**Cost**: $15K-$70K

### Option B: Parallel POCs (Fast Track)

Execute TypeScript and WASM POCs in parallel:

**Advantages**:
- Faster decision (6 weeks vs 10 weeks)
- Direct comparison of results
- Lower risk (two options validated)

**Disadvantages**:
- Higher cost (both teams at once)
- More coordination needed

**Timeline**: 6 weeks
**Cost**: $40K-$55K

### Recommended: Sequential Approach

**Rationale**:
1. TypeScript is primary recommendation
2. If TypeScript POC succeeds, no need for others
3. Lower cost if first POC successful
4. Allows learning from first POC to inform second

## POC Decision Framework

### After TypeScript POC:

```
TypeScript POC Results
    │
    ├─ All success criteria met?
    │   └─ YES → ✅ Approve full TypeScript implementation
    │   └─ NO → Analyze gaps
    │           │
    │           ├─ Minor issues (fixable)?
    │           │   └─ YES → ✅ Approve with mitigation plan
    │           │   └─ NO → Execute WASM POC
    │           │
    │           └─ Major blockers?
    │               └─ YES → Execute WASM POC
```

### After WASM POC (if needed):

```
WASM POC Results
    │
    ├─ Success criteria met?
    │   └─ YES → ✅ Approve WASM implementation
    │   │        Consider TypeScript for v2.0
    │   │
    │   └─ NO → ⚠️ Re-evaluate requirements
    │           Consider: Reduce scope, hybrid approach,
    │           or defer browser support
```

## Risk Mitigation

### POC Risks

| Risk | Mitigation |
|------|------------|
| POC takes longer than planned | Time-box strictly, reduce scope if needed |
| Technical blocker discovered | Have backup POC ready (WASM) |
| Resource unavailability | Line up backup developers |
| Budget overrun | Set hard budget limits, monitor weekly |

### Post-POC Risks

| Risk | Mitigation |
|------|------------|
| Full implementation more complex than POC | POC validates core technical risk, expect 10-20% variance |
| POC success doesn't guarantee production success | Include performance, bundle size, and debugging in POC |
| Scope creep after POC | Lock requirements before full implementation |

## Next Steps After POC Approval

### If TypeScript Approved:

1. **Week 1-2**: Detailed implementation planning
   - Finalize architecture
   - Define module boundaries
   - Create project roadmap
2. **Week 3-4**: Team ramp-up
   - Hire additional developers if needed
   - Onboarding and training
   - Development environment setup
3. **Month 2**: MVP Development begins

### If WASM Approved:

1. **Week 1-2**: Build pipeline setup
   - Production TeaVM configuration
   - Optimize compilation
   - Set up CI/CD
2. **Week 3-4**: Full compilation
   - Compile complete Archie
   - JavaScript bridge development
3. **Month 2**: Testing and optimization

## Conclusion

The POC phase is critical for de-risking the full implementation. By validating key technical assumptions early, we ensure informed decision-making and set up the project for success.

**Recommended Next Action**: Execute TypeScript POC (4 weeks, $15K-$20K)

---
**Last Updated**: October 2025
**Status**: POC Roadmap Complete
**Next Step**: Stakeholder approval to proceed with POC
