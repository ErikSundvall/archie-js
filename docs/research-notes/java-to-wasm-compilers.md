# Java-to-WebAssembly Compilation Options

## Overview
Research on viable Java-to-WASM compilers for converting Archie (openEHR library) to run in browsers.

## Compiler Options

### 1. GraalVM Native Image + WASM
**Status**: Experimental/Research Phase
- **Approach**: Compile Java to native code, then to WASM via LLVM
- **Maturity**: Experimental WASM support announced but not production-ready
- **Pros**: 
  - Strong AOT compilation support
  - Good performance potential
  - Backed by Oracle/strong community
- **Cons**:
  - WASM support still experimental
  - Large bundle sizes typical
  - Complex build configuration
- **References**: 
  - GraalVM WebAssembly announcement (2022)
  - Limited production examples

### 2. TeaVM
**Status**: Active Development
- **Approach**: Bytecode-to-JavaScript/WASM transpiler
- **Maturity**: Mature for JavaScript output, WASM support improving
- **Pros**:
  - Java → JavaScript proven and stable
  - Active development
  - Good tree-shaking support
  - Smaller bundle sizes than GraalVM
- **Cons**:
  - WASM support less mature than JS
  - Not all Java features supported
  - Community smaller than GraalVM
- **References**:
  - TeaVM GitHub: https://github.com/konsoletyper/teavm
  - Used in production by several projects

### 3. JWebAssembly
**Status**: Active but Smaller Community
- **Approach**: Direct Java bytecode to WASM compilation
- **Maturity**: Functional for basic Java code
- **Pros**:
  - Focused specifically on WASM target
  - Simpler than GraalVM
  - Reasonable bundle sizes
- **Cons**:
  - Limited Java API support
  - Smaller community/ecosystem
  - May not support all Archie dependencies
- **References**:
  - JWebAssembly GitHub: https://github.com/i-net-software/JWebAssembly
  - Focus on WebAssembly specification compliance

### 4. CheerpJ
**Status**: Commercial/Production-Ready
- **Approach**: Java-to-JavaScript/WASM with full JVM in browser
- **Maturity**: Production-ready, commercial product
- **Pros**:
  - Complete Java support including reflection
  - Battle-tested in production
  - Good performance
  - Full JVM emulation
- **Cons**:
  - **Commercial license required** (not open source for production use)
  - Larger bundle size (includes JVM)
  - Licensing costs
- **References**:
  - Leaning Technologies: https://leaningtech.com/cheerpj/
  - Used for running enterprise Java apps in browser

## Assessment for Archie

### Archie Requirements:
- OpenEHR ADL 2 parsing
- Complex object model (AOM)
- Reference model implementation
- Archetype validation
- Potential ANTLR grammar dependencies

### Compatibility Analysis:
1. **GraalVM**: High potential but experimental WASM support is a blocker
2. **TeaVM**: Best balance for now - proven track record, good for Archie's complexity
3. **JWebAssembly**: May struggle with complex dependencies (ANTLR, etc.)
4. **CheerpJ**: Technically capable but licensing costs are concern

## Recommendation for Further Investigation:
**Priority 1**: TeaVM (JavaScript output initially, WASM as it matures)
**Priority 2**: Monitor GraalVM WASM progress for future consideration
**Priority 3**: CheerpJ if licensing budget available and immediate WASM needed

## Similar Projects:
- **Minecraft (TeaVM)**: Demonstrates large-scale Java→JS conversion
- **JSweet**: Alternative transpiler (Java→TypeScript)
- **GWT**: Older but proven Java→JS (maintenance mode)

---
*Last Updated: October 2025*
