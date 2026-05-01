# Modular Computer Vision System

This project is a notebook-based computer vision workflow built around a modular image enhancement pipeline. The current notebook implements **Task 1: Selective Object Enhancement**, which detects regions that match a target color, builds a soft mask, sharpens and boosts saturation on the target region, and blurs the background for contrast.

### What it does

- Loads images from the `images/` folder.
- Builds a color-based mask using HSV thresholding.
- Cleans the mask with morphological operations and softens edges with Gaussian blur.
- Enhances the target region with sharpening and saturation boosting.
- Applies a blurred background and blends both regions into a final image.
- Displays the original image, mask, and enhanced result side by side.

### Current notebook structure

- `SelectiveEnhancementConfig` stores the enhancement parameters.
- `build_color_mask()` creates the soft target mask.
- `sharpen_image()` applies unsharp-mask style sharpening.
- `boost_saturation()` increases color intensity in HSV space.
- `selective_object_enhancement()` runs the full pipeline.
- `show_triptych()` visualizes the original image, mask, and output.
- `process_and_display()` loads an image and runs the full demo.

# Task 5: Panorama Image Stitching Library

## Task Overview

Modular computer vision library that stitches multiple overlapping images into a seamless panorama using SIFT, RANSAC homography estimation, and adaptive feather blending.

## Features

- SIFT feature detection + Lowe's ratio matching
- RANSAC-based homography estimation
- Geometric mask-based blending (no black edges)
- Auto-cropping to valid content
- Sequential multi-image stitching
- Fully modular & reusable design

### Requirements

The notebook uses:

- Python
- OpenCV
- NumPy
- Matplotlib

If you need to install the core packages manually, use:

```bash
pip install opencv-python numpy matplotlib
```

### How to run

1. Open `Modular Computer Vision System.ipynb`.
2. Make sure the sample images are available in the `images/` folder.
3. Run the notebook cells in order.
4. Call `process_and_display(image_path, target_color)` with one of the supported colors:
   - red
   - yellow
   - green
   - blue
   - purple
   - pink
   - orange

Example usage:

```python
from pathlib import Path

images_dir = Path("images")
process_and_display(images_dir / "rose.jpg", target_color="red")
```

### Project direction

This README is the first project pass and will be expanded as additional notebook tasks are added. Future sections can document new modules, task-specific methods, and comparison results as the system grows.

### Notes

- The notebook is designed to stay modular so later tasks can reuse the same structure.
- The current implementation is tuned for simple color-based selection and may need adjustment for new datasets.

## Task 8: High Dynamic Range (HDR) Imaging

- Overview: implements an HDR pipeline that aligns exposures, computes per-pixel weights, merges exposures into a linear radiance map, and applies Reinhard tone mapping to produce a displayable LDR image.
- Key implementation notes:
  - Input images are converted from sRGB to linear light before merging (this fixes dim/incorrect radiance estimates).
  - A Reinhard tone-mapper with configurable `key`, `exposure_boost`, and `gamma` is applied; these parameters control overall brightness and contrast.
  - Per-pixel weighting favors well-exposed pixels; the weight `sigma` can be increased if darker exposures are over-weighted.
- Quick run (from the notebook):

```python
# in the notebook Task 8 cells
run_example()  # runs merge + tone map and saves images/memorial hdr.jpg
```

- Troubleshooting: if HDR output looks dim, try:
  - Verifying `images/` are being read and that exposure times correspond to files.
  - Increasing `key` (e.g., 0.36) and `exposure_boost` (e.g., 1.5).
  - Ensuring linearization is present (sRGB→linear) before merge and converting back to sRGB for display.

This README will expand with example outputs and parameter guidance as Task 8 is refined.
