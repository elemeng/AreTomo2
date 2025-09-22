# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AreTomo2 is a multi-GPU accelerated software package for automated motion-corrected marker-free tomographic alignment and reconstruction in cryo-electron microscopy. It integrates CTF (Contrast Transfer Function) estimation and is designed for high-throughput processing pipelines.

## Build Commands

### Main Build
```bash
# For GPUs with compute capability 6.0 and above (recommended)
make exe -f makefile11 [CUDAHOME=path/cuda-xx.x]

# For older GPUs with compute capability 5.x
make exe -f makefile [CUDAHOME=path/cuda-xx.x]
```

### Library Dependencies
Before building the main executable, build the required libraries:
```bash
# Build utility library
cd LibSrc/Util
make clean && make all

# Build MRC file library  
cd ../Mrcfile
make clean && make all
```

### Clean Build
```bash
make clean -f makefile11  # or makefile
```

## Architecture Overview

### Core Processing Pipeline
The main processing flow is orchestrated by `CProcessThread` which implements these key stages:

1. **Preprocessing**: Dark frame removal, CTF estimation
2. **Alignment**: Coarse alignment, tilt finding, stretch alignment, projection matching, patch alignment  
3. **Correction**: Dose weighting, motion correction
4. **Reconstruction**: Weighted back projection (WBP) or SART reconstruction
5. **Output**: Volume generation, IMOD compatibility files

### Key Components

- **CInput**: Command-line argument parsing and parameter management
- **CProcessThread**: Main processing orchestration (`CProcessThread.h:8`)
- **CTomoStack**: Manages tilt series data and metadata (`MrcUtil/CTomoStack.cpp`)
- **CAlignParam**: Global alignment parameters (`MrcUtil/CAlignParam.cpp`)
- **CLocalAlignParam**: Local alignment parameters for patch-based correction

### Module Organization

- **CommonLine/**: Tilt axis determination using common line methods
- **ProjAlign/**: Projection matching alignment
- **PatchAlign/**: Local patch-based alignment for motion correction
- **FindCtf/**: CTF parameter estimation from tilt images
- **Recon/**: Tomographic reconstruction (WBP and SART)
- **Correct/**: Motion correction and interpolation
- **ImodUtil/**: IMOD software compatibility output
- **Util/**: GPU-accelerated utility functions (FFT, correlation, etc.)

### GPU Architecture

- CUDA-based implementation with multi-GPU support
- Compute capabilities: 5.2, 5.3, 6.0, 6.1, 7.0, 7.5, 8.0, 8.6 (makefile11)
- Key GPU kernels in `Util/G*.cu` files for FFT, correlation, image processing
- Thread-safe GPU context management across modules

### Data Flow

1. Input MRC tilt series → Dark frame detection/removal
2. CTF estimation on raw images → CTF parameter files
3. Global alignment → Transformation matrices (.xf files)
4. Local patch alignment → Local motion vectors
5. Motion-corrected stack → Reconstruction → 3D volume
6. IMOD-compatible outputs (.tlt, .xf, .csv files)

## Important Implementation Details

- **Coordinate System**: Compatible with IMOD - left edge is 0, pixel coordinates are 0.5-based
- **Tilt Axis Handling**: Supports both fixed and variable tilt axis refinement
- **Memory Management**: Careful GPU memory management with cudaSetDevice calls
- **Multi-threading**: Uses pthread for CPU parallelization alongside GPU acceleration
- **File Formats**: MRC input/output, custom .aln alignment files, IMOD-compatible exports