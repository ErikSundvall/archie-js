# PR Instructions

## Important: Base Branch Configuration

**This PR MUST be merged to the `javascript-experiments` branch, NOT to `main`.**

When creating/configuring the PR, ensure:
- **Base branch**: `javascript-experiments`
- **Compare branch**: `copilot/move-pr-to-javascript-experiments`

## What This PR Does

This PR restores the changes from PR #7 that were accidentally merged to `main` and then reverted. Those changes now belong in the `javascript-experiments` branch where they were originally intended.

## Verification

After merging this PR to `javascript-experiments`, the following files should be present:
- All documentation in `docs/` directory (5 new files)
- Updated `AI-guidance/readme.md` and other AI guidance files
- All task files in `tasks/` directory
- `branches.md` file
- `SOLUTION_SUMMARY.md` (this can be deleted after merge if desired)

## Why Not Push Directly?

The `javascript-experiments` branch has been updated locally with these changes, but we don't have direct push permissions. This PR is the proper mechanism to get the changes into the target branch.
