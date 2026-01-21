# Pailheres Fork - Added Drivers

## Purpose

This fork adds HAL and BSP drivers that are **missing from ST's official GitHub repository** but are included in ST's official download package.

## What Was Added

### HAL Drivers (238 files)
- **Location**: `STM32N6xx_HAL_Driver/Inc/` and `STM32N6xx_HAL_Driver/Src/`
- **Content**: Complete STM32N6xx Hardware Abstraction Layer
  - Header files (.h): Peripheral definitions and APIs
  - Source files (.c): Peripheral driver implementations
  - Documentation: Release notes, license files

### BSP Component Drivers (139 files)
- **Location**: `BSP/Components/`
- **Content**: Board Support Package drivers for various components:
  - `lan8742/` - Ethernet PHY (LAN8742A)
  - `rtl8211/` - Ethernet PHY (RTL8211F)
  - `aps256xx/` - PSRAM memory
  - `cs42l51/` - Audio codec
  - `gt911/` - Touch screen controller
  - `imx335/` - Camera sensor
  - `mx25um51245g/` - Flash memory
  - `mx66uw1g45g/` - Flash memory
  - `rk050hr18/` - LCD panel
  - `tcpp0203/` - USB Type-C controller
  - `wm8904/` - Audio codec
  - `Common/` - Shared BSP utilities

## Source

**Downloaded from**: https://www.st.com/en/embedded-software/stm32cuben6.html
**Version**: STM32Cube_FW_N6_V1.3.0
**Date**: 2026-01-21

## License

All added drivers maintain their original licenses:
- **HAL Drivers**: BSD-3-Clause (STMicroelectronics)
- **BSP Components**: BSD-3-Clause (STMicroelectronics)

See individual LICENSE files in component directories and the main repository LICENSE.md.

## Why This Fork Exists

ST's GitHub repository (`STMicroelectronics/STM32CubeN6`) has **placeholder directories** for HAL and BSP drivers, but the actual driver source files are only distributed in the downloadable ZIP package.

This fork makes the repository **complete and usable** by including these drivers, eliminating the need for manual file copying from ZIP downloads.

## Additional Changes

### ThreadX Cortex-M55 Build Fixes
- Fixed missing `tx_thread_secure_stack_initialize.S` in CMakeLists.txt
- Fixed incorrect register names in `tx_initialize_low_level.S`

These fixes enable ThreadX to build correctly for Cortex-M55 targets.

## Future: Transfer to R2-inc

**Planned**: This fork will be transferred to the R2-inc organization when management approves:
```
pailheres/STM32CubeN6  →  R2-inc/STM32CubeN6
```

**Why personal fork first**:
- Manager approval pending (out of office)
- Validate working setup before company adoption
- Transfer preserves full history and all commits

## Upstream Updates

To merge future updates from ST's official repository:

```bash
# Add ST's repo as upstream
git remote add upstream https://github.com/STMicroelectronics/STM32CubeN6.git
git fetch upstream

# Merge updates (resolve conflicts if needed)
git merge upstream/main

# Re-add drivers if they get removed
# (ST's repo won't have them, so they'll be deleted in merge)
```

## Usage in fw-system

This fork is used as a git submodule in the `fw-system` mono-repository:

```bash
# Clone with complete drivers
git clone --recursive https://github.com/R2-inc/fw-system
```

The submodule reference in `.gitmodules`:
```
[submodule "external/STM32CubeN6"]
    path = external/STM32CubeN6
    url = https://github.com/pailheres/STM32CubeN6.git
```

---

**Maintained by**: [@pailheres](https://github.com/pailheres)
**For**: R2-inc embedded firmware development
