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
