# Modular Computer Vision System

A comprehensive Jupyter notebook implementing **8 modular computer vision tasks**, each with reusable functions and clear design patterns. All tasks are built around real-world image processing challenges and are designed to stay modular for integration into other projects.

## Overview

This project provides production-ready implementations of essential computer vision pipelines:

| Task       | Focus                          | Key Techniques                                                |
| ---------- | ------------------------------ | ------------------------------------------------------------- |
| **Task 1** | Selective Object Enhancement   | HSV masking, morphological ops, soft alpha blending           |
| **Task 2** | X-Ray Image Sharpening         | Unsharp Masking, Laplacian, Gradient Injection                |
| **Task 3** | Auto Image Enhancement         | Adaptive enhancement selection, CLAHE, gamma correction       |
| **Task 4** | Document Cleaning              | Median blur, EDE morphology, adaptive thresholding            |
| **Task 5** | Panorama Stitching             | SIFT, RANSAC, homography, feather blending                    |
| **Task 6** | Transformed Object Recognition | Feature matching, geometric verification, object localization |
| **Task 7** | Stereo Depth Estimation        | Stereo rectification, StereoSGBM, left-right consistency      |
| **Task 8** | HDR Imaging                    | Image alignment, exposure fusion, Reinhard tone mapping       |

---

## Task 1: Selective Object Enhancement

**Objective:** Enhance objects of a specified color while blurring the background for visual contrast.

**Pipeline:**

1. Convert image to HSV color space
2. Build a soft mask using color-based thresholding
3. Apply morphological operations to clean the mask
4. Enhance foreground (boost saturation + sharpen)
5. Blur background and alpha blend the two layers

**Key Functions:**

- `get_soft_mask(img_hsv, target_color)` — Creates smooth mask for target color
- `enhance_foreground(img_bgr)` — Applies saturation boost and unsharp sharpening
- `selective_object_enhancement(image_path, target_color)` — Main pipeline
- `show_results(original, mask, result)` — Visualize triptych

**Supported Colors:** red, yellow, green, blue, purple, pink, orange

**Example:**

```python
from pathlib import Path
images_dir = Path("images")
orig, mask, result = selective_object_enhancement(
    images_dir / "rose.jpg",
    target_color="red"
)
show_results(orig, mask, result)
```

---

## Task 2: Comparative Study of X-Ray Image Sharpening

**Objective:** Compare three sharpening approaches designed for medical X-ray images.

**Three Pipelines:**

1. **Unsharp Masking** — Classic frequency-domain approach
2. **Laplacian + CLAHE** — Edge detection + local contrast
3. **Gradient Injection** — Sobel edges + aggressive contrast enhancement

**Key Functions:**

- `pipeline_unsharp_masking(img, kernel_size, sigma, amount)` — Traditional sharpening
- `pipeline_laplacian(img)` — 2nd-order derivative edge detection
- `pipeline_gradient_injection(img)` — Maximum edge emphasis
- `display_image(image_path)` — Side-by-side 4-image comparison

**Example:**

```python
display_image("images/chest xray.jpg")
```

---

## Task 3: Intelligent Auto Image Enhancement

**Objective:** Automatically analyze image statistics and select the best enhancement strategy.

**Strategy Selection:**

- **Underexposed** (mean < 90) → Gamma correction + optional CLAHE
- **Overexposed** (mean > 170) → Gamma correction + contrast stretching
- **Low Contrast** (std < 50) → CLAHE for local contrast
- **Normal** → Auto white-balance

**Key Functions:**

- `analyze_image_enhancement(image_bgr)` — Extract brightness/contrast statistics
- `select_enhancement_strategy(image_bgr)` — Choose best pipeline
- `apply_gamma_luminance(image_bgr, gamma)` — Adjust brightness in LAB space
- `apply_clahe(image_bgr, clip_limit, tile_grid_size)` — Contrast-limited adaptive histogram
- `auto_white_balance(image_bgr)` — Gray-world color balancing
- `process_auto_enhancement(image_path)` — Full pipeline with visualization

**Example:**

```python
original, enhanced, stats, strategy = process_auto_enhancement("images/rose.jpg")
```

---

## Task 4: Document Cleaning System

**Objective:** Restore scanned document images by removing noise, shadows, and uneven illumination.

**Three-Step Pipeline:**

1. **Median Background Subtraction** — Remove uneven lighting
2. **Morphological EDE** — Clean up noise and connect broken characters
3. **Adaptive Gaussian Thresholding** — Create crisp binary output

**Key Functions:**

- `median_background_subtraction(image, ksize=23)` — Background estimation
- `ede_method(image, kernel_size=3)` — Dilate-Erode morphology
- `adaptive_threshold(image, block_size=35, C=10)` — Gaussian-weighted thresholding
- `enhance_document(image)` — Full cleaning pipeline
- `show_pipeline(image_path)` — Visualize each processing step

**Example:**

```python
show_pipeline("images/dirty document.png")
```

---

## Task 5: Panorama Image Stitching

**Objective:** Merge multiple overlapping images into a seamless panoramic image.

**Pipeline:**

1. Extract SIFT features from each image
2. Match features between image pairs using FLANN
3. Estimate homography using RANSAC
4. Warp and blend images geometrically
5. Auto-crop to valid content

**Key Classes & Functions:**

- `FeatureExtractor` — SIFT detection, FLANN matching, RANSAC homography
- `stitch_pair(img1, img2, H, crop=True)` — Warp, blend, and crop two images
- `plot_inliers(img1, img2, pts1, pts2, title)` — Visualize matched keypoints

**Example:**

```python
fe = FeatureExtractor()
H, inliers1, inliers2 = fe.get_homo_matrix(img1, img2)
pano = stitch_pair(img1, img2, H)
```

---

## Task 6: Transformed Object Recognition

**Objective:** Detect whether a reference object appears in a query image, even if rotated, scaled, or translated.

**Pipeline:**

1. SIFT keypoint detection & descriptor extraction
2. FLANN-based feature matching
3. Lowe's ratio test for match filtering
4. RANSAC homography estimation for geometric verification
5. Transform object corners to locate it in query image

**Key Functions:**

- `recognize_transformed_object(ref_img, query_img, min_inliers=10, ratio_thresh=0.75)` — Full recognition
- Returns: (is_found, result_img, inlier_count, match_visualization)

**Example:**

```python
found, result, inlier_count, match_img = recognize_transformed_object(
    ref_img, query_img
)
print(f"Object found: {found}, Inliers: {inlier_count}")
```

---

## Task 7: Stereo Depth Estimation

**Objective:** Estimate depth maps from stereo image pairs using rectification and dense matching.

**Pipeline:**

1. SIFT feature matching with Lowe's ratio test
2. Fundamental matrix estimation (RANSAC)
3. Calibrated stereo rectification using camera intrinsics
4. StereoSGBM dense disparity computation
5. Left-right consistency check for outlier removal
6. Multi-stage hole filling (morphological + inpainting)
7. Metric depth conversion

**Key Functions:**

- `build_K(image_path, shape)` — Extract focal length from EXIF or estimate
- `match_features(img_l, img_r)` — SIFT + RANSAC feature matching
- `rectify(img_l, img_r, K, pts1, pts2, F, baseline_cm)` — Stereo rectification
- `compute_disparity(rect_l, rect_r)` — StereoSGBM with L-R check
- `fill_holes(disparity, rect_l)` — Multi-stage hole filling
- `to_metric_depth(disparity, f_px, baseline_cm)` — Convert to metric depth
- `run_pipeline(img_l, img_r, left_path, baseline_cm)` — Complete pipeline
- `display_results(result, title, baseline_cm)` — Professional 2x4 visualization

**Example:**

```python
result = run_pipeline(
    img_left, img_right,
    left_path="images/left.jpg",
    baseline_cm=8.0  # Real camera baseline in cm
)
display_results(result)
```

---

## Task 8: High Dynamic Range (HDR) Imaging

**Objective:** Recover detail in both shadows and highlights by merging multiple exposures into a single HDR radiance map.

**Pipeline:**

1. Load multiple exposure images (LDR)
2. Convert exposure values to exposure times
3. Align images using ECC (Enhanced Correlation Coefficient)
4. Convert to linear light space (inverse gamma)
5. Merge into HDR radiance map using weighted exposure fusion
6. Tone map back to displayable range using Reinhard algorithm

**Key Functions:**

- `align_images(images)` — ECC-based image alignment to middle reference frame
- `merge_hdr(images, exposure_times)` — Weighted exposure fusion into radiance map
- `tone_map_reinhard(hdr, key=0.18)` — Global Reinhard tone mapping
- `custom_hdr(key=0.10)` — Complete custom HDR pipeline

**Features:**

- Per-pixel weight favors well-exposed regions (mid-gray at 0.5 luminance)
- Inverse gamma correction for accurate linear light blending
- Configurable `key` parameter controls overall brightness (lower = moodier, higher = brighter)
- Comparison with OpenCV's reference HDR pipeline

**Example:**

```python
ldr_result = custom_hdr(key=0.18)
```

---

## Requirements

Install the required packages:

```bash
pip install opencv-python numpy matplotlib pillow
```

**For Task 7 (Stereo Depth):** Ensure OpenCV is compiled with `ximgproc` for guided filtering (optional; uses bilateral filter as fallback).

---

## Setup & Usage

1. **Place images** in the `images/` folder
2. **Open the notebook:**
   ```bash
   jupyter notebook Modular\ Computer\ Vision\ System.ipynb
   ```
3. **Run cells in order** — each task is independent but some depend on loaded images

---

## Project Structure

```
Modular Computer Vision System/
├── Modular Computer Vision System.ipynb   # Main notebook with all 8 tasks
├── README.md                              # This file
└── images/                                # Input image directory
    ├── rose.jpg
    ├── sunflower.jpg
    ├── cat skeleton xray.jpg
    ├── dirty document.png
    ├── Belvedere Gardens 1.png
    ├── Belvedere Gardens 2.png
    ├── Belvedere Gardens 3.png
    ├── book 1.png
    ├── matching book 1.png
    ├── orange chest chairs (left).jpeg
    ├── orange chest chairs (right).jpeg
    ├── teddy still life (left).jpeg
    ├── teddy still life (right).jpeg
    ├── memorial church 1.png
    ├── ... (up to memorial church 5.png)
```

---

## Design Principles

- **Modularity:** Each function solves a single problem and can be reused independently
- **Clarity:** Extensive docstrings explain the "why" behind each algorithm step
- **Flexibility:** Parameters are exposed and tunable for different datasets
- **Robustness:** Error handling and fallbacks for edge cases (e.g., extreme exposure differences in Task 7)

---

## Notes

- All tasks are designed to be imported and reused in other projects
- Color-based selection (Task 1) may need tuning for new datasets
- Stereo depth (Task 7) improves with accurate camera calibration
- HDR (Task 8) assumes exposure times are provided; EXIF fallback available for JPEG metadata

---

## Troubleshooting

**Task 1 (Color Enhancement):** If mask is too broad/narrow, adjust `COLOR_RANGES` or increase/decrease Gaussian blur radius.

**Task 2 (X-Ray Sharpening):** Different pipelines suit different types of medical images; test all three and choose visually.

**Task 3 (Auto Enhancement):** Adjust thresholds (`mean_val < 90`, `std_dev < 50`) for different lighting conditions.

**Task 5 (Panorama):** Ensure 30–50% overlap between consecutive images for reliable feature matching.

**Task 7 (Stereo Depth):** Use `baseline_cm` parameter for metric depth; without it, depth is in normalized units but still visualizes correctly.

**Task 8 (HDR):** If output looks dim, increase `key` parameter (e.g., 0.25–0.36) and verify exposure times are correct.
