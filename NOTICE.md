# NOTICE: third-party work used by the OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System

This list matches Section 4 (Attribution list) of [Ethics_Statement.md](Ethics_Statement.md). Licenses use SPDX identifiers.

## Libraries and models

| Name | Source | License |
|---|---|---|
| Ultralytics YOLO11 (code and `yolo11m.pt` weights) | https://github.com/ultralytics/ultralytics | AGPL-3.0-or-later |
| SAHI | https://github.com/obss/sahi | MIT |
| PyTorch, torchvision | https://github.com/pytorch/pytorch | BSD-3-Clause |
| Hugging Face transformers | https://github.com/huggingface/transformers | Apache-2.0 |
| Depth Anything V2 Small | https://huggingface.co/depth-anything/Depth-Anything-V2-Small-hf | Apache-2.0 |
| NumPy | https://numpy.org | BSD-3-Clause |
| Pillow | https://python-pillow.org | MIT-CMU |
| PyYAML | https://pyyaml.org | MIT |
| scikit-learn, scikit-image, SciPy, pandas, seaborn, joblib, Jupyter, ipywidgets | PyPI project pages | BSD-3-Clause |
| matplotlib | https://matplotlib.org | LicenseRef-Matplotlib (PSF-style) |
| opencv-python, XGBoost | PyPI project pages | Apache-2.0 |
| SHAP, grad-cam (https://github.com/jacobgil/pytorch-grad-cam) | PyPI project pages | MIT |
| tqdm | https://github.com/tqdm/tqdm | MPL-2.0 AND MIT |
| CVAT (labeling tool) | https://github.com/cvat-ai/cvat | MIT |
| Flutter SDK and Dart | https://github.com/flutter/flutter | BSD-3-Clause |
| Node.js | https://nodejs.org | MIT |
| firebase-functions | https://github.com/firebase/firebase-functions | MIT |
| firebase-admin | https://github.com/firebase/firebase-admin-node | Apache-2.0 |
| FlutterFire (`firebase_core`, `firebase_auth`, `cloud_firestore`) | https://github.com/firebase/flutterfire | BSD-3-Clause |

Disease and pest models were trained by the team. The pest classifier uses an ImageNet-pretrained ResNet-18 from torchvision (BSD-3-Clause).

## Code we build on

- `GrapeBunchDetection` notebooks (`Deep Learning/`, `Machine Learning/`): written by Hoang Nhat Duy Le. Upstream: https://github.com/hoangnhatduyle/GrapeBunchDetection. `grapevision/` refactors their method.

## Datasets

| Dataset | Source | License | Collection | Restriction |
|---|---|---|---|---|
| Grapevine Bunch Detection Dataset, I. Pinheiro (INESC TEC), 2023, v2 | https://doi.org/10.5281/zenodo.7717055 | CC-BY-4.0 | 720×540 images with YOLO bunch boxes; the record does not state a collection method | Credit the author and state changes |
| CERTH Grape Dataset, A. Blekos et al. (CERTH), 2024, v2; cite as *Agronomy* 2023, 13, 1995 | https://doi.org/10.5281/zenodo.10777647 | CC-BY-4.0 | 2,502 iPhone 11 Pro photos of a Crimson Seedless vineyard, 2022–2023; COCO annotations converted to YOLO by the team | Credit the authors and state changes |
| UAV multispectral imagery dataset over a vineyard affected by Botrytis, S. Vélez, M. Ariza-Sentís, J. Valente (WUR), 2022; *Data in Brief* 46 (2023) 108876 | https://doi.org/10.5281/zenodo.7064894 | CC-BY-4.0 | 16,504 MicaSense RedEdge 3 drone images (5 bands, 1280×960), Tomiño, Spain, September 16, 2021 | Credit the authors and state changes |
| Pest training images | Prepared by a former team member; source not recorded | Unknown | — | — |
| West Zone 2 capture and LiveImages | Vineyard of the professor who manages the project | Not open | Phone photos taken at the vineyard | Project use only |

## Fonts and other assets

- DejaVu Sans (bundled with matplotlib), Bitstream-Vera license.
- Pillow's built-in default font.
