# Updating from ST's Upstream Releases

This document explains how to update this fork when STMicroelectronics releases a new version (e.g., v1.4.0, v1.5.0).

---

## Branch Strategy

This fork maintains **two branches**:

- **`main`** - Pure ST releases (empty driver directories, easy to sync)
- **`add-missing-drivers`** - ST releases + complete HAL/BSP drivers (393 files added)

**Why separate?** ST's GitHub has empty driver directories. Keeping `main` pure makes upstream updates conflict-free.

---

## Update Process

### When ST Releases a New Version (e.g., v1.4.0):

#### Step 1: Update `main` Branch (No Conflicts)

```bash
# Switch to main branch
git checkout main

# Add ST's official repo as upstream (if not already added)
git remote add upstream https://github.com/STMicroelectronics/STM32CubeN6.git

# Fetch latest from ST
git fetch upstream

# Merge ST's new release into main
git merge upstream/main
# ✅ This should be clean - no conflicts (both have empty driver dirs)

# Push updated main
git push origin main
```

#### Step 2: Merge `main` into `add-missing-drivers`

```bash
# Switch to our branch with drivers
git checkout add-missing-drivers

# Merge updated main into our branch
git merge main
# ⚠️ EXPECT CONFLICTS in Drivers/ directories
```

#### Step 3: Resolve Conflicts

Git will report conflicts in:
- `Drivers/STM32N6xx_HAL_Driver/` (ST has empty, we have 238 files)
- `Drivers/BSP/Components/` (ST has empty placeholders, we have 139 files)

**Resolution Strategy:**
```bash
# Keep OUR drivers (the real files, not ST's empty dirs)
git checkout --ours Drivers/STM32N6xx_HAL_Driver
git checkout --ours Drivers/BSP/Components

# Mark as resolved
git add Drivers/

# Complete the merge
git commit -m "Merge main (ST v1.4.0) into add-missing-drivers"
```

#### Step 4: Update Drivers from ST's Download Package

ST ships drivers in their ZIP download, not GitHub. Update them:

1. **Download** STM32Cube_FW_N6_V1.4.0.zip from:
   https://www.st.com/en/embedded-software/stm32cuben6.html

2. **Extract and compare** driver versions:
   ```bash
   # Compare file counts
   ls STM32Cube_FW_N6_V1.4.0/Drivers/STM32N6xx_HAL_Driver/Src/ | wc -l
   ls Drivers/STM32N6xx_HAL_Driver/Src/ | wc -l
   ```

3. **Copy updated/new drivers** (if any):
   ```bash
   # Backup current drivers
   cp -r Drivers Drivers.backup

   # Copy HAL drivers from package
   cp -r STM32Cube_FW_N6_V1.4.0/Drivers/STM32N6xx_HAL_Driver/* \
         Drivers/STM32N6xx_HAL_Driver/

   # Copy BSP drivers from package
   cp -r STM32Cube_FW_N6_V1.4.0/Drivers/BSP/Components/* \
         Drivers/BSP/Components/

   # Review changes
   git status
   git diff --stat
   ```

4. **Commit driver updates** (if any changed):
   ```bash
   git add Drivers/
   git commit -m "Update HAL and BSP drivers to STM32Cube_FW_N6_V1.4.0"
   ```

#### Step 5: Push and Update fw-system

```bash
# Push updated branch
git push origin add-missing-drivers

# Update fw-system submodule reference
cd ../../../  # Back to fw-system root
git add external/STM32CubeN6
git commit -m "Update STM32CubeN6 submodule to v1.4.0"
git push origin main
```

---

## Release History

| ST Version | Release Date | Our Update Commit | Notes |
|------------|--------------|-------------------|-------|
| v1.3.0 | 2024-12-19 | 36b07898 | Initial fork with drivers added |
| v1.2.0 | 2024-07-19 | - | Not tracked |
| v1.4.0 | TBD | - | To be updated |

**Expected Update Frequency:** Every 5-6 months based on ST's release pattern.

---

## Troubleshooting

### Problem: Merge conflicts in files we added

**Symptom:** Git reports conflicts in our driver files (not just directories)

**Cause:** ST added the same driver files in their new release

**Solution:**
```bash
# Use ST's version (they're now providing it officially)
git checkout --theirs path/to/conflicted/file.c

# Or keep ours if ST's is incomplete
git checkout --ours path/to/conflicted/file.c

# Review and decide case-by-case
git diff --ours --theirs path/to/conflicted/file.c
```

### Problem: New drivers in v1.4.0 not in our fork

**Symptom:** fw-system build fails with missing headers

**Solution:** Download ST's package and copy new driver directories as shown in Step 4.

---

## Why This Fork Exists

ST's GitHub repository has **placeholder directories** but ships actual driver source files only in the downloadable ZIP package.

This fork makes the repository **complete and usable** by including:
- 238 HAL driver files (`STM32N6xx_HAL_Driver/Inc/` and `/Src/`)
- 139 BSP component files (lan8742, rtl8211, and 10 other components)

See `Drivers/README_PAILHERES_FORK.md` for complete details.

---

**Maintained by:** [@pailheres](https://github.com/pailheres)
**For:** R2-inc embedded firmware development
**Transfer to R2-inc organization:** Planned when management approves
