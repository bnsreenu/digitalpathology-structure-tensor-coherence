# Structure Tensor Coherence Map for Collagen Organization in H&E Images

**DigitalSreeni Tutorial**

This repository contains the Colab notebook, slides, and supporting materials for the tutorial on quantifying collagen fiber organization in breast carcinoma H&E stained tissue using the structure tensor coherence map - without deep learning, without training data, and without special staining.

---

## Clinical motivation

Collagen fiber organization in the tumor microenvironment actively directs cancer cell invasion. The Tumor-Associated Collagen Signatures (TACS) framework classifies collagen relative to the tumor boundary:

| Grade | Pattern | Clinical meaning |
|---|---|---|
| TACS-1 | Dense, locally straightened | Early stromal remodeling |
| TACS-2 | Parallel to tumor boundary | Tumor constrained, less aggressive |
| TACS-3 | Perpendicular to tumor boundary | Invasion highways, poor prognosis |

Manual TACS scoring requires a specialist pathologist and is not scalable. SHG microscopy is the gold standard for automated quantification but requires specialized equipment not available in routine workflows. This tutorial demonstrates how the structure tensor coherence map extracts equivalent information from standard H&E slides.

---

## Method overview

The structure tensor coherence map assigns a value C between 0 and 1 to every pixel:

- **C near 1** - all local fiber edges point the same direction (aligned collagen stroma)
- **C near 0** - gradients cancel because fibers run in every direction (tumor nests, disorganized stroma)

The orientation map gives the dominant fiber direction at every pixel as a free bonus from the same computation.

**Pipeline:**

```
H&E image
    → Gaussian gradients (Ix, Iy)          sigma = 2 px
    → Structure tensor (Jxx, Jxy, Jyy)
    → Gaussian smoothing                    rho = 20 px
    → Coherence map C + orientation map theta
    → Otsu threshold (automatic)
    → Morphological cleanup
    → Segmentation + per-zone metrics
```

No hyperparameters to tune beyond sigma (matched to fiber width) and rho (matched to fiber spacing). Both scale with magnification.



---

## Notebook walkthrough

The notebook is organized into 12 cells covering the full pipeline:

| Cell | Content |
|---|---|
| 0 | Imports |
| 1 | Load and visualize the H&E image |
| 2 | Annotate tissue zones (for tutorial illustration) |
| 3 | Compute image gradients (sigma = 2) |
| 4 | Build the structure tensor (rho = 20) |
| 5 | Coherence and orientation maps |
| 6 | Coherence profile across the image |
| 7 | Bimodal histogram and Otsu threshold |
| 8 | Segmentation and connected component labeling |
| 9 | Overlay on original H&E |
| 10 | Orientation rose plots per tissue zone |
| 11 | Quantitative TACS metrics report |
| 12 | Clinical interpretation and references |

---

## Key results on the tutorial image

```
TACS QUANTIFICATION REPORT
============================
Tumor / disorganized stroma (left 70%)
  CAI (mean coherence):          0.254 +/- 0.126
  Resultant vector R:            0.373
  Circular std of orientation:   40.2 degrees
  Dominant fiber angle:          88.9 degrees

Aligned collagen stroma (right 30%)
  CAI (mean coherence):          0.658 +/- 0.207
  Resultant vector R:            0.864
  Circular std of orientation:   15.5 degrees
  Dominant fiber angle:          82.0 degrees

Overall
  CAI ratio (fiber / tumor):     2.59x
  Disorganized area fraction:    71.4%
  Organized area fraction:       28.6%
```

---

## Running the notebook

Open directly in Google Colab:

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/bnsreenu/digitalpathology-structure-tensor-coherence/blob/main/notebook/TACS_collagen_coherence.ipynb)

All dependencies (numpy, scipy, scikit-image, matplotlib, Pillow) come pre-installed on Colab. No pip installs needed.

To run locally:

```bash
pip install numpy scipy scikit-image matplotlib pillow
jupyter notebook notebook/TACS_collagen_coherence.ipynb
```

---

## Parameters

| Parameter | Value used | What it controls | How to adjust |
|---|---|---|---|
| `SIGMA` | 2 px | Gradient smoothing | Match to fiber width. Increase at lower magnification |
| `RHO` | 20 px | Tensor neighbourhood | Match to 1-2 fiber cycles. Increase for coarser fibers |
| `ZONE_SPLIT` | int(W * 0.7) | Zone boundary for metrics comparison | Tutorial only. Automated detection uses Otsu |

The Otsu threshold for segmentation is computed automatically from the bimodal coherence histogram. No manual threshold needed.

---

## About the H&E image

The image used in this tutorial was generated using ChatGPT image generation with the following prompt:

> A realistic H&E (hematoxylin and eosin) stained histology image at 20x magnification, simulating invasive breast carcinoma with tumor stroma. Irregular fragmented tumor nests distributed across the left two-thirds of the field, with collagen organization varying continuously: highly disorganized immediately adjacent to tumor nests, transitioning to partially aligned in the mid-stroma, and densely parallel-aligned at the far right margin. Add a small thin-walled vessel in the mid-stroma. Include scattered lymphocytes between tumor nests. The tumor-stroma boundary should be irregular and invasive-looking, not a smooth line. Slight variation in eosin intensity across the field as seen in real sections. The overall color palette should be authentic H&E: blue-purple nuclei, pink cytoplasm and collagen, white background. No labels, no annotations, no scale bars. Flat 2D microscopy view, sharp focus across the entire image, square crop, realistic tissue texture. No text of any kind in the image.

---

## References

- Provenzano PP, Eliceiri KW, Campbell JM, Inman DR, White JG, Keely PJ. Collagen reorganization at the tumor-stromal interface facilitates local invasion. *BMC Medicine* 2006;4(1):38.

- Schedin P, Keely PJ. Mammary gland ECM remodeling, stiffness, and mechanosignaling in normal development and tumor progression. *Cold Spring Harbor Perspectives in Biology* 2011;3(1):a003228.

- Bredfeldt JS, Liu Y, Pehlke CA, Conklin MW, Szulczewski JM, Inman DR, Keely PJ, Nowak RD, Mackie TR, Eliceiri KW. Computational segmentation of collagen fibers from second-harmonic generation images of breast cancer. *Journal of Biomedical Optics* 2014;19(1):016007.

- Avila FJ, Bueno JM. Analysis and quantification of collagen organization with the structure tensor in second harmonic microscopy images of ocular tissues. *Applied Optics* 2015;54(33):9848-9854.

---

## Related videos

This tutorial is a standalone video. [YouTube Link](https://youtu.be/xzgSLx1J0Go)
It connects to the **AI in Digital Pathology** series:

- The patch tiling approach for WSI analysis is covered in earlier videos in that series
- Tumor boundary detection (needed for TACS-2 vs TACS-3 classification) uses the tissue segmentation methods from that series

Full playlist: [DigitalSreeni YouTube channel](https://www.youtube.com/@DigitalSreeni)

---

## License

MIT License. See LICENSE file for details.
