# Documentation Resource Inventory

**Last Updated:** 2025-10-30  
**Branch:** javascript-experiments / copilot/update-prd-with-clarifications

## Available Documentation Resources

### Primary Project Documentation

#### 1. Main README
- **File:** `README.md`
- **Location:** `/home/runner/work/archie-js/archie-js/README.md`
- **Description:** Comprehensive guide to the Archie openEHR Library
- **Content Includes:**
  - Library overview and purpose
  - Build and dependency instructions
  - Usage examples for archetypes
  - Reference model tools documentation
  - JSON/XML serialization examples
  - Experimental features

#### 2. Project Branch Documentation
- **File:** `branches.md`
- **Location:** `/home/runner/work/archie-js/archie-js/branches.md`
- **Description:** Documentation of repository branches and their purposes

### Module-Specific Documentation

#### 3. ADL Checker
- **File:** `adlchecker/README.md`
- **Description:** Documentation for the ADL checker tool

#### 4. Internationalization (i18n)
- **File:** `i18n/README.md`
- **Description:** Internationalization module documentation

#### 5. JSON Schema
- **File:** `json-schema/README.md`
- **Description:** JSON schema definitions and usage

#### 6. OpenEHR Terminology
- **File:** `openehr-terminology/README.md`
- **Description:** Terminology service documentation

#### 7. Tools Module
- **File:** `tools/README.md`
- **Description:** Additional tools and utilities
- **Supplementary:** `tools/src/test/resources/ckm-mirror/README.md`

### Task Management Documentation

#### 8. AI Guidance Directory
- **Location:** `AI-guidance/`
- **Files:**
  - `readme.md` - Overview and working branch information
  - `create-prd.md` - PRD creation guidelines
  - `generate-tasks.md` - Task list generation process
  - `process-task-list.md` - Task execution protocol
- **Purpose:** Guidelines for AI-assisted development workflow

#### 9. Tasks Directory
- **Location:** `tasks/`
- **Files:**
  - `README.md` - Task directory overview
  - `0001-prd-feature-pack-0001-setup.md` - Feature Pack 0001 PRD
  - `tasks-0001-prd-feature-pack-0001-setup.md` - Feature Pack 0001 task list
- **Purpose:** Project requirements and task tracking

### Verification Artifacts (Created During Setup)

#### 10. Branch Verification Report
- **File:** `docs/branch-verification.md`
- **Description:** Documentation of branch alignment with upstream adl_2.4_support

#### 11. Deepwiki Access Report
- **File:** `docs/deepwiki-access.md`
- **Description:** Deepwiki MCP access verification and fallback plan

#### 12. This Inventory
- **File:** `docs/documentation-inventory.md`
- **Description:** Complete listing of available documentation resources

## External Documentation Resources

### OpenEHR Specifications (Recommended)
- **Official Specifications:** https://specifications.openehr.org/
- **ADL Documentation:** https://specifications.openehr.org/releases/AM/latest/
- **BMM Specification:** https://specifications.openehr.org/releases/LANG/latest/bmm.html
- **Reference Model:** https://specifications.openehr.org/releases/RM/latest/

### Upstream Repository
- **GitHub:** https://github.com/openEHR/archie
- **ADL 2.4 Support Branch:** https://github.com/openEHR/archie/tree/adl_2.4_support
- **Issues:** https://github.com/openEHR/archie/issues
- **Wiki:** May contain additional documentation

### Deepwiki Resources (If Accessible)
- **Archie Documentation:** https://deepwiki.com/openEHR/archie
- **BMM Specifications:** https://deepwiki.com/openEHR/specifications-ITS-BMM
- **Access Status:** Not directly available in current environment (see deepwiki-access.md)

## Source Code Documentation

### Javadoc Comments
- **Location:** Throughout source files in all modules
- **Access:** Via IDE or code review
- **Quality:** Well-documented public APIs

### Test Examples
- **Location:** `*/src/test/` directories in each module
- **Purpose:** Practical usage examples
- **Value:** Shows real-world implementation patterns

## Documentation Usage Guide

### For New Developers
1. Start with main `README.md`
2. Review relevant module README files
3. Examine test cases for usage examples
4. Consult PRD and task lists for project context

### For Feature Development
1. Check PRD in `tasks/` directory
2. Follow task list guidance
3. Reference module-specific READMEs
4. Review source code and tests

### For Troubleshooting
1. Check main README for common issues
2. Review module documentation
3. Examine test cases for expected behavior
4. Consult upstream repository issues

## Maintenance Notes

- **Update Frequency:** As new documentation is created
- **Owner:** Project team
- **Location Changes:** Update paths if files are reorganized
- **New Resources:** Add to appropriate section when discovered

## Summary

**Total Documentation Files:** 17+ markdown files  
**Coverage:** Comprehensive project and module documentation  
**External Resources:** OpenEHR specifications and upstream repository  
**Status:** ✅ Sufficient for Feature Pack 0001 verification

All necessary documentation resources are available to proceed with setup verification and subsequent feature pack development.
