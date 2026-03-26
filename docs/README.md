# NeedleFinder

Fast interactive needle detection for 3D Slicer. Provides tools to segment needles in MR/CT images, primarily tested on MRI from gynecological brachytherapy cases.

## Requirements

- 3D Slicer 5.10+ (Python 3.12)

## Quick Start

1. **Load your MR/CT image** into Slicer
2. **Switch to NeedleFinder** from the Modules dropdown (under IGT)

## Automatic Needle Detection

### Step 1: Set the segmentation boundary

- Navigate to the axial slice where you want the search to stop (typically the template/base)
- Click **"1. Select Current Axial Slice as Seg. Limit"**
- A green marker appears showing the boundary

### Step 2: Click on needle tips

- Click **"2. Start Giving Needle Tips"** (or press **Ctrl+Enter**)
- Click on each visible **needle tip** in the slice views
- For each click, the algorithm automatically tracks the needle from tip downward and renders a 3D tube

### Step 3: Review results

- Each detected needle appears as a colored tube in the 3D view
- The **Segmentation Report** table shows needle statistics
- **Ctrl+Z** -- undo/delete the last needle
- **Ctrl+Y** -- accept the tip estimate
- **Ctrl+N** -- reject and retry
- Click the button again to **stop** giving tips

## Key Parameters

Found under "Needle Detection Parameters (Developers)":

| Parameter | Default | Description |
|-----------|---------|-------------|
| Needle detection version | 3 | Algorithm variant (3 = MICCAI 2015, recommended) |
| Radius of cone base (mm) | 5 | Search radius around tip -- increase if missing needles |
| Number of Control Points | 6 | Points along needle shaft for curve fitting |
| Sigma Value | 20 | Gaussian attenuation width -- lower = tighter fit |
| Gradient Ponderation | 5 | Weight factor for gradient component |
| Center Ponderation | 2 | Weight for center-to-edge distance (higher = more attraction to center) |
| Number of rotating steps | 35 | Angle discretization (higher = finer search) |
| Length of needles (mm) | 100 | Maximum needle length |
| Radius of needles (mm) | 2 | Tube radius in 3D visualization |
| Search for bright needles (CT)? | off | Enable for CT images (needles are bright, not dark) |
| Tracking in both directions | off | Track both up and down from tip |
| Auto correct tip position? | off | Refine tip location automatically |
| Compute gradient? | on | Calculate intensity gradients for edge detection |
| Add Gaussian Prob. Attenuation? | on | Apply Gaussian weighting based on distance from tip |
| Max Needle Length? | on | Limit needle length to configured maximum |
| Extend Needle | off | Artificially extend needle to "Real Needle Length" |
| Real Needle Length (mm) | 240 | Target length when "Extend Needle" is enabled |

Use **Load/Save/Reset Parameters** buttons to manage configuration profiles. Default configuration is loaded from `Config/default.cfg`.

### Algorithm Versions

| Version | Description |
|---------|-------------|
| 0 | Current development algorithm |
| 1 | MICCAI 2013 algorithm |
| 2 | Alternative MICCAI 2013 |
| 3 | MICCAI 2015 algorithm (default, recommended) |
| 4 | MICCAI 2013 + Ruibin's post-processing |
| 5 | MICCAI 2015 + Ruibin's post-processing |

### Tuning Tips

If needles don't segment well, adjust these in order:

1. **Wrong algorithm?** Change "Needle detection version" (try 0 or 1)
2. **Missing needle section?** Increase "Number of Control Points" or "Radius of cone base"
3. **Needle too thin?** Decrease "Sigma Value" (tighter gaussian)
4. **Needle too thick?** Increase "Sigma Value" (wider gaussian)
5. **Tracking in wrong direction?** Enable "Tracking in both directions"
6. **Tip location wrong?** Enable "Auto correct tip position"
7. **CT image?** Enable "Search for bright needles (CT)"

## Manual Validation

For comparing automatic detection against manual ground truth:

1. Set **"Choose Needle"** spinner to the needle number (starting from 0)
2. Click **"Start Giving Control Points"** -- click along a needle manually
3. Click **"Next Validation Needle"** to move to the next needle
4. Click **"Render Manual Needle X"** to visualize the manual segmentation
5. Click **"Start Evaluation"** to run automatic detection from manual tips and compute metrics

Results are printed to the Python console as Hausdorff distances (mm) between manual and automatic needle segmentations.

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl+Enter | Start/stop giving needle tips |
| Ctrl+Z | Delete last segmented needle |
| Ctrl+Y | Accept needle tip estimate |
| Ctrl+N | Reject needle tip estimate |
| Ctrl+U | Accept tip estimate as new temporary marker |

## References

- Validation of Catheter Segmentation for MR-guided Gynecologic Cancer Brachytherapy (MICCAI 2013). [SPL publication](https://www.spl.harvard.edu/publications/item/view/2459)
- Labeled Needle Rendering Solution for Image Guided Brachytherapy. [SPL publication](https://www.spl.harvard.edu/publications/item/view/2316)
