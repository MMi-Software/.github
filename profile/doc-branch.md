# Code Versioning Process

## Branch Types
There are four types of branches:
1. **Main**: main and dev
2. **Development (d)**
3. **Pre-release (pr)**
4. **Hot-fix (hf)**

## Branch Descriptions
### Main Branches
- **main**: Main and stable branch. Contains the production version of the code.
- **dev**: Main development branch. Initially a copy of the main branch and the base for all development branches.

### Development Branches
- Created from the dev branch to work on specific requirements.
- Named using the prefix `D`, followed by the requirement identifier and a description.
- Example: `D-123456-fix-device-redirect`.

### Pre-Release Branches
- Created from dev to consolidate and test changes made in various development branches.
- They do not contain original changes, only merge changes from the development branches.
- Named using the prefix `PR`, followed by the projected version and a sequential branch number.
- Example: `PR-v1.5.0-0`.

### Hot-Fix Branches
- Created directly from the main branch to fix critical issues in production.
- The changes made in these branches are merged directly into the main branch, and a patch version is applied.
- Example: `HF-123456-fix-critical-bug`.

## Development Process
1. **Start of Development**:
   - Create a new development branch from dev.
   - Name the branch following the format: `D-[requirement ID]-[requirement description]`.
   - Example: `D-123456-fix-device-redirect`.

2. **Solution Development**:
   - Make the necessary changes in the development branch.
   - Commit frequently with descriptive messages.

3. **Consolidation and Testing**:
   - When one or more developments need to be tested, create a pre-release (pr) branch from dev.
   - Name the branch following the format: `PR-[projected version]-[sequential number]`.
   - Example: `PR-v1.5.0-0`.
   - Merge the necessary development branches into the pr branch.

4. **Adjustments and Modifications in Pre-Release**:
   - If the planned release requires adjustments, create a new pr branch from dev.
   - Merge only the necessary developments into the new pr branch.
   - Name the branch following the same format but incrementing the sequential number.
   - Example: `PR-v1.5.0-1`.

5. **Approval and Merge to main**:
   - Once the pr branch is approved, create a pull request to main.
   - Upon acceptance, a commit with the changes is generated and tagged with the corresponding version.
   - Example of version: `v1.5.12` to `v1.6.0` (if it’s a major change) or `v1.5.12` to `v1.5.13` (if it’s a minor change).

6. **Merge to dev**:
   - Once the pr branch is merged into main, it should also be merged into dev.
   - This ensures that dev is always updated with the latest changes from main.

7. **Rebase of Development Branches**:
   - If a development branch falls behind, perform a rebase onto dev to keep it updated.

## Hot-Fix Process
1. **Start of Hot-Fix**:
   - Create a hot-fix branch directly from the main branch.
   - Name the branch following the format: `HF-[requirement ID]-[requirement description]`.

2. **Hot-Fix Development**:
   - Make the necessary changes to fix the critical issue.
   - Commit with descriptive messages and perform tests.

3. **Merge to main**:
   - Once the hot-fix is complete, create a pull request to main.
   - Upon acceptance, apply a patch to the current version.
   - Example of version: `v1.5.12` to `v1.5.13`.

4. **Update dev**:
   - Merge the changes from the hot-fix branch into dev to keep it updated.

## Versioning
- Versions are defined by a major version number, followed by a minor version, and a patch value: `v[major].[minor].[patch]`.
- Example: `v1.5.8`.

### Branch Naming
- **Development Branches**: `D-[requirement ID]-[description]`.
  - Example: `D-123456-fix-device-redirect`.
- **Pre-Release Branches**: `PR-[projected version]-[sequential number]`.
  - Example: `PR-v1.5.0-0`.
- **Hot-Fix Branches**: `HF-[requirement ID]-[description]`.
  - Example: `HF-123456-fix-critical-bug`.

This process ensures an orderly and controlled flow for code development, testing, and release, maintaining the stability and quality of the product in production.
