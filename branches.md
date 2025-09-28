# Archie-js Repository Branches

This document describes the most active and interesting branches in the archie-js repository, focusing on those that have been modified within the last year or two.

## Main Branch

### `master`
- **Purpose**: Main development branch containing the latest stable code
- **Last Activity**: September 2025 (very recent)
- **Key Recent Changes**: 
  - Gradle files reformatting (#721)
  - Exception handling improvements in validation (#719)
  - Gradle Version Catalog implementation (#716)
  - Gradle 8.14.3 update and deprecated features fixes (#715)
- **Status**: Actively maintained

## Dependabot Branches (Automated Dependency Updates)

The project uses Dependabot for automated dependency management, with several recent branches:

### `dependabot/gradle/jacksonVersion-2.20.0`
- **Purpose**: Updates Jackson JSON library from 2.19.2 to 2.20.0
- **Last Activity**: September 2025
- **Status**: Recent dependency update

### `dependabot/gradle/com.google.guava-guava-33.5.0-jre`
- **Purpose**: Updates Google Guava library to version 33.5.0-jre
- **Last Activity**: September 2025
- **Status**: Recent dependency update

### `dependabot/gradle/org.apache.commons-commons-lang3-3.19.0`
- **Purpose**: Updates Apache Commons Lang3 library to version 3.19.0
- **Last Activity**: Recent
- **Status**: Dependency maintenance

### `dependabot/gradle/org.gradle.toolchains.foojay-resolver-convention-1.0.0`
- **Purpose**: Updates Gradle toolchains plugin for automatic JDK management
- **Last Activity**: Recent
- **Status**: Build tooling improvement

## Feature and Development Branches

### `adl_2.4_support`
- **Purpose**: Adds support for ADL (Archetype Definition Language) version 2.4
- **Last Activity**: March 2025 (merged from master)
- **Description**: Important feature branch for supporting the latest ADL specification
- **Status**: Active development for OpenEHR standards compliance

### `migrate_to_jakarta`
- **Purpose**: Migration from javax to jakarta namespace for XML binding
- **Last Activity**: July 2023
- **Description**: Important modernization effort to migrate from deprecated javax packages to jakarta EE
- **Status**: Migration branch for Java EE to Jakarta EE transition

### `javascript-experiments`
- **Purpose**: Experimental JavaScript-related functionality
- **Last Activity**: Same as master branch (appears to be in sync)
- **Description**: Exploring JavaScript integration or transpilation capabilities
- **Status**: Experimental/research branch

## ADL Conversion and Processing Branches

### `4264_adl14_to_adl24_conversion`
- **Purpose**: Conversion utilities from ADL 1.4 to ADL 2.4 format
- **Description**: Critical tooling for migrating legacy ADL content to newer specifications
- **Status**: Feature development for archetype migration

### `ADL-conv-correct_atcode_numbering`
- **Purpose**: Fixes for archetype term code numbering during ADL conversion
- **Description**: Ensures proper at-code (archetype term codes) numbering consistency
- **Status**: Bug fixes for conversion process

## Other Notable Branches

### `android_compatible`
- **Purpose**: Modifications for Android platform compatibility
- **Description**: Adaptations to make the library work on Android runtime
- **Status**: Platform-specific compatibility work

### `bmm_jackson`
- **Purpose**: Jackson JSON integration for BMM (Basic Meta-Model) processing
- **Description**: JSON serialization improvements for openEHR meta-models
- **Status**: Integration improvement

### `fix-quantity-rules` / `fix-quantity-rules-2.0.1`
- **Purpose**: Bug fixes for quantity validation rules
- **Description**: Fixes for handling numeric and measurement validation in archetypes
- **Status**: Bug fixes for validation engine

### `v1.0.2-branch`
- **Purpose**: Version 1.0.2 release branch
- **Description**: Maintenance branch for specific version release
- **Status**: Release maintenance

## Branch Activity Summary

The repository shows active development with:
- **50+ total branches** indicating extensive feature development and experimentation
- **Very recent activity** on master branch (September 2025)
- **Active dependency management** via Dependabot
- **Focus on OpenEHR standards compliance** (ADL 2.4 support)
- **Modernization efforts** (Jakarta migration, Gradle updates)
- **Platform compatibility** work (Android support)

## Recommendations for Contributors

1. **Start with `master`** - Most up-to-date and stable code
2. **Check `adl_2.4_support`** - For latest ADL specification work
3. **Review Dependabot branches** - For understanding current dependencies
4. **Consider `javascript-experiments`** - For web/JS integration needs
5. **Examine conversion branches** - For ADL migration tooling

This repository appears to be actively maintained with focus on keeping dependencies current, supporting latest OpenEHR standards, and maintaining compatibility across different platforms.