# Aspire

Bubble image processing, annotation, segmentation, and tracking workflow.

This project is for organizing raw bubble image data, converting it into PNGs, generating segmentation masks with Cellpose, converting those masks into YOLO segmentation labels, and training a YOLO-seg model for bubble detection and tracking.

In normal human words:

```text
Raw images go in.
Cellpose helps annotate bubbles.
YOLO learns from those annotations.
The GPU hopefully does not burst into flames.
```

CUDA setup is not fully automated because CUDA is less of a dependency and more of a lifestyle choice.

---

## Table of Contents

1. [Storage Requirements](#storage-requirements)
2. [Repository Structure](#repository-structure)
3. [Folder Overview](#folder-overview)
4. [What Gets Committed](#what-gets-committed)
5. [Data Setup](#data-setup)
6. [Recommended Conda Environments](#recommended-conda-environments)
7. [Environment Setup](#environment-setup)
8. [Workflow Overview](#workflow-overview)
9. [Step 1: Convert Raw BMP Images to PNG](#step-1-convert-raw-bmp-images-to-png)
10. [Step 2: Annotate with Cellpose](#step-2-annotate-with-cellpose)
11. [Step 3: Convert Cellpose Masks to YOLO Labels](#step-3-convert-cellpose-masks-to-yolo-labels)
12. [Step 4: Train YOLO-Seg](#step-4-train-yolo-seg)
13. [Before Pushing](#before-pushing)
14. [After Pulling](#after-pulling)
15. [CUDA Notes](#cuda-notes)
16. [Git Ignore Recommendations](#git-ignore-recommendations)
17. [Troubleshooting](#troubleshooting)
18. [Project Status](#project-status)

---

## Storage Requirements

Allocate **at least 20 GB+** for dependencies and conda environments.

You will need additional storage for:

```text
Raw image data
Converted PNGs
Cellpose masks
YOLO labels
Tiled datasets
Training outputs
Model weights
Validation images
Prediction results
```

The raw files are big as the Super Bowl, except instead of football, it is just two guys playing stuff they made in the studio.

Recommended rough minimum:

```text
20 GB+    conda environments / dependencies
??? GB    raw image data
??? GB    converted PNGs
??? GB    Cellpose masks and annotations
??? GB    YOLO labels and tiled datasets
??? GB    training outputs, model weights, and predictions
```

The exact amount depends on how many raw images you have, how large they are, and whether you generate tiled datasets.

Do **not** commit large datasets or generated training outputs to Git unless there is a very specific reason.

GitHub is not a hard drive. It is a filing cabinet with anxiety.

---

## Repository Structure

Recommended project layout:

```text
aspire/
├── Data/
│   ├── Raw Data/
│   └── PNGs/
│
├── Environments/
│   ├── base/
│   ├── cellpose/
│   └── yolo-seg/
│
├── Meta Scripts/
│   ├── bmp_to_png.ipynb
│   ├── update_environment_exports.ipynb
│   └── sync_conda_scripts.ipynb
│
├── Training Scripts/
│   ├── cellpose_to_yolo.ipynb
│   └── yolo_seg_train.ipynb
│
├── .gitignore
└── README.md
```

This structure separates:

```text
Data                 image files and datasets
Meta Scripts         organization and maintenance notebooks
Training Scripts     annotation conversion and model training notebooks
Environments         exported dependency files
```

---

## Folder Overview

### `Data/Raw Data/`

Stores the original raw image files.

Use this folder if you are starting from raw `.bmp` images.

Example:

```text
Data/Raw Data/
├── experiment_001.bmp
├── experiment_002.bmp
└── experiment_003.bmp
```

These files should generally be ignored by Git because they are large and GitHub does not need to be emotionally involved.

---

### `Data/PNGs/`

Stores converted `.png` images.

These images are used for:

```text
Cellpose annotation
Cellpose training
YOLO label conversion
YOLO training
Visualization
```

Example:

```text
Data/PNGs/
├── experiment_001.png
├── experiment_002.png
└── experiment_003.png
```

If you already have PNGs, place them directly into this folder and skip the raw conversion step.

---

### `Meta Scripts/`

Contains notebooks for data organization and project maintenance.

Typical notebooks:

```text
bmp_to_png.ipynb
update_environment_exports.ipynb
sync_conda_scripts.ipynb
```

Use this folder for scripts that:

```text
Convert file formats
Organize folders
Update environment exports
Sync environment scripts
Prepare project structure
```

These are not the main model training notebooks.

---

### `Training Scripts/`

Contains notebooks for annotation conversion, model training, and visualization.

Typical notebooks:

```text
cellpose_to_yolo.ipynb
yolo_seg_train.ipynb
```

Use this folder for scripts that:

```text
Convert Cellpose masks to YOLO labels
Create YOLO dataset folders
Train YOLO-seg models
Run predictions
Visualize model outputs
```

---

### `Environments/`

Contains exported dependency files for each recommended conda environment.

This folder should store environment files, not the actual conda environments themselves.

Recommended structure:

```text
Environments/
├── base/
│   ├── environment.yml
│   └── pip_freeze.txt
│
├── cellpose/
│   ├── environment.yml
│   └── pip_freeze.txt
│
└── yolo-seg/
    ├── environment.yml
    └── pip_freeze.txt
```

The README does not list every package because that would turn it into a CVS receipt.

Use the files inside `Environments/` for exact dependency information.

---

## What Gets Committed

The repo should commit:

```text
Notebook scripts
README
.gitignore
Environment export files
Small configuration files
Small example files if needed
```

The repo should usually **not** commit:

```text
Raw images
Converted PNGs
Cellpose generated masks
YOLO training outputs
Model weights
Large tiled datasets
Cache folders
Temporary files
```

In other words:

```text
Commit code.
Commit instructions.
Commit environment records.

Do not commit the entire data swamp.
```

---

## Data Setup

There are two normal starting points.

---

### Option 1: Starting from Raw BMP Images

Use this if you only have the raw image files.

Place raw files here:

```text
Data/Raw Data/
```

Create this folder if it does not already exist:

```text
Data/PNGs/
```

Then run:

```text
Meta Scripts/bmp_to_png.ipynb
```

This converts files from:

```text
Data/Raw Data/
```

into:

```text
Data/PNGs/
```

The converted PNGs are then used for Cellpose and YOLO workflows.

---

### Option 2: Starting from Existing PNGs

Use this if you already have converted PNG images.

Place PNG files directly here:

```text
Data/PNGs/
```

Then skip `bmp_to_png.ipynb` and continue with Cellpose annotation.

---

## Recommended Conda Environments

This project works best with separate conda environments.

Recommended environments:

```text
base        general scripts, environment syncing, repo maintenance
cellpose    Cellpose annotation and semi-automatic segmentation
yolo-seg    YOLO segmentation training and inference
```

Keeping Cellpose and YOLO separate is strongly recommended because PyTorch, CUDA, NumPy, OpenCV, and friends enjoy turning dependency resolution into a group project nobody asked for.

---

## Environment Setup

Environment files are stored in:

```text
Environments/
```

Each environment should have its own folder.

Example:

```text
Environments/yolo-seg/environment.yml
Environments/yolo-seg/pip_freeze.txt
```

---

### Creating an Environment from an Export

Example for the YOLO environment:

```bash
conda env create -f Environments/yolo-seg/environment.yml
```

Example for the Cellpose environment:

```bash
conda env create -f Environments/cellpose/environment.yml
```

---

### Updating an Existing Environment

If the environment already exists:

```bash
conda env update -f Environments/yolo-seg/environment.yml --prune
```

or:

```bash
conda env update -f Environments/cellpose/environment.yml --prune
```

The `--prune` flag removes packages that are no longer listed in the environment file.

That is useful when your environment has accumulated random packages like a junk drawer.

---

### Activating an Environment

Example:

```bash
conda activate yolo-seg
```

or:

```bash
conda activate cellpose
```

---

### Checking Installed Packages

To list conda packages:

```bash
conda list
```

To export conda packages:

```bash
conda env export > environment.yml
```

To list pip packages:

```bash
pip freeze
```

To export pip packages:

```bash
pip freeze > pip_freeze.txt
```

---

### Updating Environment Export Files

Before pushing, run:

```text
Meta Scripts/update_environment_exports.ipynb
```

This notebook should update the exported environment files inside:

```text
Environments/
```

The goal is that someone else can pull the repo and recreate the same environments without needing to guess which package caused the GPU to stop existing.

---

## Workflow Overview

General workflow:

```text
Raw BMP images
    ↓
Convert BMP to PNG
    ↓
Annotate / segment using Cellpose
    ↓
Convert Cellpose masks to YOLO-seg labels
    ↓
Train YOLO-seg model
    ↓
Run segmentation / tracking analysis
```

Notebook order:

```text
1. Meta Scripts/bmp_to_png.ipynb
2. Cellpose annotation / training workflow
3. Training Scripts/cellpose_to_yolo.ipynb
4. Training Scripts/yolo_seg_train.ipynb
```

---

# Step 1: Convert Raw BMP Images to PNG

Run this step only if starting from raw `.bmp` images.

Input folder:

```text
Data/Raw Data/
```

Output folder:

```text
Data/PNGs/
```

Notebook:

```text
Meta Scripts/bmp_to_png.ipynb
```

Expected result:

```text
Data/PNGs/
├── image_001.png
├── image_002.png
└── image_003.png
```

After this step, the PNG folder should contain converted images ready for annotation.

If you already have PNGs, skip this step.

---

# Step 2: Annotate with Cellpose

Use Cellpose to segment bubbles in the PNG images.

Input folder:

```text
Data/PNGs/
```

Environment:

```text
cellpose
```

The goal of this step is to generate masks or segmentation outputs that identify bubbles in each image.

General Cellpose workflow:

```text
1. Open images from Data/PNGs/
2. Run Cellpose segmentation
3. Manually inspect and correct masks if needed
4. Train or fine-tune Cellpose if needed
5. Save Cellpose outputs
```

Cellpose is used because manually labeling thousands of bubbles per image would be a cry for help.

Recommended approach:

```text
Use Cellpose for semi-automatic annotation.
Check the generated masks.
Fix obvious errors.
Use the masks as the starting point for YOLO-seg labels.
```

---

# Step 3: Convert Cellpose Masks to YOLO Labels

After Cellpose masks are created, run:

```text
Training Scripts/cellpose_to_yolo.ipynb
```

Environment:

```text
yolo-seg
```

This notebook should convert Cellpose segmentation masks into YOLO segmentation label format.

YOLO segmentation labels usually look like this:

```text
class_id x1 y1 x2 y2 x3 y3 ...
```

where the coordinates are normalized between `0` and `1`.

Expected output should include a YOLO-compatible dataset structure, such as:

```text
yolo_seg/
├── images/
│   ├── train/
│   └── val/
│
├── labels/
│   ├── train/
│   └── val/
│
└── data.yaml
```

The exact folder name may vary depending on the notebook, but the important part is that YOLO receives:

```text
Training images
Matching label files
A data.yaml file
```

---

# Step 4: Train YOLO-Seg

After labels are converted, run:

```text
Training Scripts/yolo_seg_train.ipynb
```

Environment:

```text
yolo-seg
```

This notebook trains a YOLO segmentation model using the prepared dataset.

Typical YOLO training inputs:

```text
data.yaml
training images
training labels
validation images
validation labels
```

Typical YOLO outputs:

```text
runs_yolo_seg/
├── train/
│   ├── weights/
│   │   ├── best.pt
│   │   └── last.pt
│   ├── results.png
│   └── ...
```

Recommended output folder:

```text
runs_yolo_seg/
```

or:

```text
Training Scripts/runs_yolo_seg/
```

These folders should usually be ignored by Git.

The main file you care about after training is usually:

```text
best.pt
```

That is the best model checkpoint from training.

---

## Before Pushing

Before pushing changes to the repo, do the following.

---

### 1. Make Sure Notebooks Run

Check that the important notebooks still run:

```text
Meta Scripts/bmp_to_png.ipynb
Training Scripts/cellpose_to_yolo.ipynb
Training Scripts/yolo_seg_train.ipynb
```

You do not necessarily need to fully retrain the model every time, but the scripts should not be obviously broken.

---

### 2. Update Environment Exports

Run:

```text
Meta Scripts/update_environment_exports.ipynb
```

This updates the dependency records in:

```text
Environments/
```

This is important because dependency drift is how a working project becomes an ancient cursed artifact.

---

### 3. Check Git Status

Run:

```bash
git status
```

Make sure you are not accidentally committing:

```text
Raw images
PNGs
YOLO runs
Model weights
Cache folders
Massive generated outputs
```

If Git shows thousands of images staged, stop.

That is not a commit. That is a hostage situation.

---

### 4. Commit the Correct Files

Good files to commit:

```text
README.md
.gitignore
.ipynb notebooks
environment.yml files
pip_freeze.txt files
small config files
```

Usually bad files to commit:

```text
*.bmp
*.png
*.pt
runs/
runs_yolo_seg/
large generated datasets
```

---

### 5. Push

After checking everything:

```bash
git add README.md
git add "Meta Scripts/"
git add "Training Scripts/"
git add Environments/
git add .gitignore
git commit -m "Update project workflow and environment files"
git push
```

Adjust the commit message as needed.

---

## After Pulling

After pulling the repo onto a new computer, do the following.

---

### 1. Pull the Latest Repo

```bash
git pull
```

---

### 2. Check the Folder Structure

Make sure these folders exist:

```text
Data/
Data/Raw Data/
Data/PNGs/
Environments/
Meta Scripts/
Training Scripts/
```

If the data folders are missing, create them manually or make sure `.gitkeep` files are being tracked.

---

### 3. Recreate or Update Environments

For a fresh setup:

```bash
conda env create -f Environments/cellpose/environment.yml
conda env create -f Environments/yolo-seg/environment.yml
```

For an existing setup:

```bash
conda env update -f Environments/cellpose/environment.yml --prune
conda env update -f Environments/yolo-seg/environment.yml --prune
```

---

### 4. Sync Conda Scripts

Run:

```text
Meta Scripts/sync_conda_scripts.ipynb
```

This should be run on the `base` kernel.

This step is meant to make sure environment-related scripts and records are in sync after pulling changes.

---

### 5. Add Data

If starting from raw data:

```text
Data/Raw Data/
```

If starting from PNGs:

```text
Data/PNGs/
```

The data itself is usually not stored in the repo, so pulling the repo does not necessarily mean you pulled the image dataset.

---

### 6. Continue the Workflow

Depending on what files you have:

```text
If you have raw BMPs:
    run bmp_to_png.ipynb

If you have PNGs:
    run Cellpose annotation workflow

If you have Cellpose masks:
    run cellpose_to_yolo.ipynb

If you have YOLO labels:
    run yolo_seg_train.ipynb
```

---

## CUDA Notes

CUDA setup depends on:

```text
GPU model
NVIDIA driver version
CUDA runtime version
PyTorch version
Python version
Conda environment
Whether your computer woke up angry
```

Useful check in terminal:

```bash
nvidia-smi
```

Useful check inside Python:

```python
import torch

print(torch.__version__)
print(torch.version.cuda)
print(torch.cuda.is_available())

if torch.cuda.is_available():
  print(torch.cuda.get_device_name(0))
else:
  print("No CUDA GPU detected")
```

Important notes:

```text
nvidia-smi showing CUDA does not automatically mean PyTorch can use CUDA.
PyTorch must be installed with CUDA support.
CPU-only PyTorch will not use the GPU.
Cellpose and YOLO may need separate PyTorch builds.
Python version matters.
Conda and pip can both install PyTorch, which can get messy.
```

If this prints:

```text
False
```

for:

```python
torch.cuda.is_available()
```

then PyTorch is not currently using the GPU.

This is usually caused by:

```text
CPU-only PyTorch install
Wrong Python version
Wrong CUDA-enabled PyTorch build
Broken conda environment
Driver mismatch
Installing packages in the wrong environment
```

Do not assume that because CUDA exists, PyTorch can see it.

Do not assume that because PyTorch can see it, Cellpose can use it.

Do not assume anything.

CUDA can smell confidence.

---

## Git Ignore Recommendations

Recommended `.gitignore` entries:

```gitignore
# Raw and converted image data
Data/Raw Data/*
Data/PNGs/*

# Keep empty folders
!Data/Raw Data/.gitkeep
!Data/PNGs/.gitkeep

# Cellpose outputs
*_seg.npy
*_masks.png
*_flows.tif
*_cp_masks.png

# YOLO datasets and training outputs
runs/
runs_yolo_seg/
Training Scripts/runs/
Training Scripts/runs_yolo_seg/

# Model weights
*.pt
*.pth
*.onnx
*.engine

# Python cache
__pycache__/
*.pyc

# Jupyter
.ipynb_checkpoints/

# Local environments
.conda/
.venv/
.env

# OS files
.DS_Store
Thumbs.db
```

To keep empty data folders in Git, add a `.gitkeep` file inside each folder:

```text
Data/Raw Data/.gitkeep
Data/PNGs/.gitkeep
```

This lets Git track the folder structure without tracking the massive data files.

---

## Troubleshooting

### Problem: Git is trying to commit all images

Check:

```bash
git status
```

If you see many `.bmp` or `.png` files, check `.gitignore`.

Make sure this exists:

```gitignore
Data/Raw Data/*
Data/PNGs/*
```

Then unstage files if needed:

```bash
git reset
```

If files were already tracked before being added to `.gitignore`, remove them from Git tracking while keeping them locally:

```bash
git rm -r --cached "Data/Raw Data"
git rm -r --cached "Data/PNGs"
```

Then commit the `.gitignore` update.

---

### Problem: PyTorch cannot see CUDA

Run:

```python
import torch

print(torch.__version__)
print(torch.version.cuda)
print(torch.cuda.is_available())
```

If CUDA is unavailable, check:

```text
Are you in the correct conda environment?
Did you install CPU-only PyTorch?
Is your Python version supported?
Does nvidia-smi detect your GPU?
Did pip overwrite your conda PyTorch install?
```

CUDA problems are rarely fixed by randomly installing five more things.

That is how environments become archaeological sites.

---

### Problem: YOLO training uses too much RAM

Possible fixes:

```text
Use smaller image tiles
Reduce batch size
Use batch=1
Reduce image size
Close memory-heavy programs
Use fewer dataloader workers
Use a smaller YOLO model
Train on fewer images for a quick test
```

For an RTX 2070 8 GB, start modestly.

Example ideas:

```text
YOLO nano model
batch=1
smaller tile size
short test run first
```

Do not start with maximum settings unless you want Windows Task Manager to become the main character.

---

### Problem: Cellpose masks do not convert cleanly

Check:

```text
Are the mask files in the expected folder?
Do mask filenames match image filenames?
Are masks empty?
Are masks instance masks, not just binary masks?
Are image dimensions and mask dimensions the same?
```

The conversion script needs to know which mask belongs to which image.

If filenames do not match, the script will suffer, and then you will suffer.

---

### Problem: YOLO labels look wrong

Check:

```text
Are coordinates normalized between 0 and 1?
Are polygons closed or valid?
Are tiny objects being filtered out?
Are class IDs correct?
Does each image have a matching .txt label file?
Are labels in the correct train/val folder?
```

YOLO segmentation labels should generally look like:

```text
0 0.123 0.456 0.124 0.457 0.125 0.458 ...
```

The first number is the class ID.

The rest are polygon coordinates.

---

## Current Project Priorities

Current priorities:

```text
1. Keep data organized.
2. Keep raw images and generated outputs out of Git.
3. Keep conda environments reproducible.
4. Keep Cellpose and YOLO workflows separate.
5. Keep CUDA setup documented clearly.
6. Keep YOLO outputs in their own folder.
7. Keep scripts readable enough that future-you does not file a complaint.
```

---

## Project Status

This project is under active development.

Expect scripts, folders, environment files, and workflows to change as the annotation and YOLO-seg training pipeline improves.

If the GPU is detected, the labels convert, the masks look decent, and the model trains without exploding RAM, that is not luck.

That is character development.
