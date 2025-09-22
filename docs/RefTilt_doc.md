
## Key Features

### Reference Tilt Angle Option (`-RefTilt`)
AreTomo2 now supports specifying a custom reference tilt angle for all operations using the `-RefTilt` option:

```bash
# Standard usage (uses zero-tilt image as reference)
AreTomo2 -InMrc input.mrc -OutMrc output.mrc -AngFile angles.tlt

# Specify custom reference tilt angle (in degrees)
AreTomo2 -InMrc input.mrc -OutMrc output.mrc -AngFile angles.tlt -RefTilt -15
```

**When to use `-RefTilt`:**
- **FIB lamella datasets**: When your tilt series doesn't start from 0° (e.g., -12° to +12°)
- **Offset tilt series**: When the optimal reference frame is not at zero degrees
- **Non-standard acquisitions**: Any case where zero-tilt is not the ideal reference

**Default behavior**: If `-RefTilt` is not specified, AreTomo2 uses 0.0° (zero-tilt image) as the reference, maintaining backward compatibility.

**Comprehensive integration**: The `-RefTilt` parameter now affects all operations including:
- Tilt axis determination and refinement
- Projection matching alignment
- Local patch-based alignment
- CTF estimation
- Volume cropping and center calculation
- Mass normalization
- Object center finding

**Note:** Not affect the behavior of **-TiltCor**.

**Default behavior**: If `-RefTilt` is not specified, AreTomo2 uses 0.0° (zero-tilt image) as the reference, maintaining backward compatibility.

Updated:

  1. Core Alignment ✅
    - CRemoveDarkFrames.cpp - Dark frame detection
    - CLocalAlign.cpp - Local patch alignment
    - CProjAlignMain.cpp - Main projection alignment
    - CFitPatchShifts.cpp - Patch shift fitting
  2. Preprocessing & Normalization ✅
    - CLinearNorm.cpp - Mass normalization
    - CCropVolume.cpp - Volume cropping
    - CFindObjectCenter.cpp - Object center finding
  3. Tilt Calculations ✅
    - CTiltAxisMain.cpp - Tilt axis determination
    - CTiltOffsetMain.cpp - Tilt offset measurement
  4. Advanced Processing ✅
    - CFindCtfMain.cpp - CTF estimation (for FIB lamella compatibility)
    - CCommonLineParam.cpp - Common line methods

  Key Benefits for FIB Lamella Users:

  1. True Zero Reference: The -RefTilt parameter now correctly identifies the actual physical zero-degree tilt, not just the file's nominal zero-tilt
  2. Milling Angle Compensation: Accounts for FIB milling angles that offset the tilt series from true zero
  3. Consistent Reference: All processing stages use the same user-specified reference tilt
  4. Backward Compatible: Default 0.0° maintains existing behavior for standard datasets