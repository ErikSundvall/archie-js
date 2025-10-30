# Product Requirements Document: Feature Pack 0001 - Setup

## Introduction/Overview

Feature Pack 0001 establishes the foundational setup for the Archie-to-JavaScript/TypeScript conversion project. This setup phase ensures the development environment is properly configured, tests are operational, and documentation resources are accessible before proceeding with conversion strategy development in subsequent feature packs.

The primary problem this feature pack solves is establishing a verified baseline state for the project, ensuring that:
1. The codebase is based on the correct upstream branch (adl_2.4_support)
2. Existing test infrastructure is functional
3. Documentation resources, particularly deepwiki MCP, are available for reference

## Goals

1. Verify that the current branch is properly based on the upstream `adl_2.4_support` branch from https://github.com/openEHR/archie/tree/adl_2.4_support
2. Validate that all existing tests can run successfully (both Java and JavaScript/TypeScript if present)
3. Confirm access to all necessary documentation resources, including the deepwiki MCP `ask_question` tool
4. Document the baseline state and any issues discovered during setup
5. Establish a verified starting point for Feature Pack 0002 (planning phase)

## User Stories

**As a** developer working on the Archie JavaScript conversion project,  
**I want to** verify the branch is based on the correct upstream version,  
**so that** I can ensure all subsequent work includes the latest ADL 2.4 support features.

**As a** developer preparing for the conversion project,  
**I want to** confirm all existing tests work,  
**so that** I can establish a baseline for quality and understand any pre-existing issues.

**As a** developer needing reference documentation,  
**I want to** verify access to deepwiki MCP and other documentation sources,  
**so that** I can efficiently look up specifications and implementation details during development.

**As a** project stakeholder,  
**I want to** have documented verification results,  
**so that** I understand the current state and can make informed decisions about proceeding.

## Functional Requirements

1. **Branch Verification**: The system must verify that the current branch contains all commits from the upstream `adl_2.4_support` branch at https://github.com/openEHR/archie/tree/adl_2.4_support. If the branch is not properly based on it or if divergence exists, rebase/include the upstream changes immediately.

2. **Java Test Execution**: The system must execute all existing Java-based tests using the Gradle build system and document the results (pass/fail counts, execution time, any errors).

3. **JavaScript/TypeScript Test Execution**: If JavaScript or TypeScript tests exist in the repository, these must be executed and results documented.

4. **Test Results Documentation**: All test runs must be documented with:
   - Total number of tests executed
   - Number of passing tests
   - Number of failing tests
   - List of any failing tests with brief description
   - Whether failures are pre-existing or related to the setup
   - Developer will decide if any extra testing is needed after initial test run is complete

5. **Deepwiki MCP Access Verification**: The system must:
   - Verify connection to the deepwiki MCP server
   - Execute a sample query using the `ask_question` tool
   - Confirm access to the documented resources:
     - https://deepwiki.com/openEHR/archie
     - https://deepwiki.com/openEHR/specifications-ITS-BMM
   - Document successful connection and sample query results
   - Note: This is a one-time verification, but ensure future agents understand how to use the deepwiki MCP and find other documentation in subsequent steps

6. **Documentation Resource Inventory**: Create an inventory of available documentation resources, including:
   - Deepwiki resources (if accessible)
   - Project README.md
   - Any other relevant documentation files in the repository

7. **Fallback Plan Documentation**: If deepwiki MCP is not accessible, document:
   - Alternative documentation sources
   - How to proceed without deepwiki access
   - Impact on development workflow

8. **Setup Summary Report**: Generate a comprehensive summary report documenting:
   - Branch verification status
   - Test execution results
   - Documentation access status
   - Any issues or blockers identified
   - Recommendations for proceeding

## Non-Goals (Out of Scope)

1. **No Implementation Work**: This feature pack does not include any implementation of JavaScript/TypeScript conversion code.

2. **No Test Fixes**: Existing failing tests should be documented but not fixed, unless they are critical blockers for the setup verification itself.

3. **No New Test Creation**: This phase does not involve creating new tests; it only validates existing test infrastructure.

4. **No Architecture Decisions**: Decisions about conversion approaches (WASM, Kotlin, pure JS/TS) are deferred to Feature Pack 0002.

5. **No Code Refactoring**: Existing code should not be refactored or modified during setup.

6. **No Dependency Updates**: Package dependencies should not be updated unless absolutely required for test execution.

## Design Considerations

N/A - This is a verification and documentation feature pack with no user-facing design components.

## Technical Considerations

1. **Build System**: The repository uses Gradle for Java builds. Ensure Gradle wrapper (`gradlew`) is functional.

2. **Git Operations**: Branch comparison with upstream requires git operations to fetch and compare commit histories.

3. **MCP Integration**: Deepwiki MCP access requires proper MCP server configuration as described in https://docs.devin.ai/work-with-devin/deepwiki-mcp.

4. **Environment Setup**: May require Java JDK installation for running Java tests.

5. **Fallback Strategy**: If deepwiki MCP is unavailable, proceed with alternative documentation sources (project README, direct access to specification sites if available).

## Success Metrics

1. **Branch Verification Complete**: Successfully compared current branch with upstream `adl_2.4_support` branch and documented any differences.

2. **Test Execution Complete**: All existing tests (Java and JavaScript/TypeScript) have been executed at least once with results documented.

3. **Documentation Access Verified**: Confirmed ability to access necessary documentation (deepwiki MCP if available, or documented fallback plan).

4. **Setup Report Generated**: Comprehensive setup summary report exists documenting all verification results.

5. **Readiness for Feature Pack 0002**: All prerequisites met and documented, enabling the team to proceed with planning phase.

6. **No Critical Blockers**: Any issues discovered are documented with severity assessment; no critical blockers prevent proceeding to Feature Pack 0002.

## Development Context

**Working Branch**: All development for this project should be done in the `javascript-experiments` branch. This branch contains the necessary setup files, documentation, and will be the base for all feature pack implementation work.
