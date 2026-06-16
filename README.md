# rs-fMRI Clinical Interface

## Overview

The rs-fMRI Clinical Interface is a Dash-based visualization tool for viewing resting-state fMRI network maps overlaid on anatomical MRI scans. The application supports both subject-space and MNI-space visualization, allows interactive adjustment of overlay thresholds and opacity, and provides export functionality for DICOM images and DICOM series.

The viewer was originally designed for clinical and research evaluation of resting-state network maps generated from stroke patient imaging data, but it can be adapted to any dataset that follows the expected directory structure.

---

# Installation

## Required Python Packages

Install the required dependencies:

```bash
pip install dash nibabel numpy plotly pydicom scipy scikit-image pillow
```

Additional dependencies:

```bash
pip install pandas
```

---

# Configuring Your Dataset

The application expects all data to be stored under a single root directory specified by:

```python
BASE_DIR = "/path/to/your/data"
```

Update this variable near the top of the script to point to your dataset location.

---

# Expected Directory Structure

Each patient should have their own folder inside `BASE_DIR`.

Example:

```text
BASE_DIR/
│
├── Patient001/
│   ├── anat/
│   │   └── sub-Patient001_desc-preproc_T1w.nii.gz
│   │
│   └── xprx/
│       ├── display/
│       │   ├── aud_net_t1.nii.gz
│       │   ├── dmn_net_t1.nii.gz
│       │   ├── sm_net_t1.nii.gz
│       │   └── ...
│       │
│       └── mni/
│           ├── tpl-MNI152NLin2009cAsym_res-02_desc-brain_T1w.nii.gz
│           ├── sub-Patient001_ses-1_task-rest_space-MNI152NLin2009cAsym_res-2_desc-aud.nii.gz
│           ├── sub-Patient001_ses-1_task-rest_space-MNI152NLin2009cAsym_res-2_desc-dmn.nii.gz
│           └── ...
│
├── Patient002/
├── Patient003/
└── ...
```

---

# Required Files

## Anatomical T1 Image

Each patient must contain at least one T1-weighted anatomical image.

The code automatically searches recursively for filenames containing:

```text
desc-preproc_T1w
T1w
T1
t1w
t1
```

Examples:

```text
sub-001_desc-preproc_T1w.nii.gz
sub-001_T1w.nii.gz
```

The highest-scoring match is automatically selected as the anatomical reference image.

---

## Subject-Space Network Maps

Subject-space overlays should be stored in:

```text
patient/xprx/display/
```

These files can be either:

```text
.nii
.nii.gz
```

Examples:

```text
aud_net_t1.nii.gz
dmn_net_t1.nii.gz
lfp_net_t1.nii.gz
rfp_net_t1.nii.gz
```

---

## MNI-Space Network Maps

MNI-space overlays should be stored in:

```text
patient/xprx/mni/
```

The viewer only loads files matching the naming convention:

```text
sub-{PATIENT}_ses-1_task-rest_space-MNI152NLin2009cAsym_res-2_desc-*.nii.gz
```

Example:

```text
sub-001_ses-1_task-rest_space-MNI152NLin2009cAsym_res-2_desc-dmn.nii.gz
```

---

# Supported Network Types

The current version includes labels for:

| Filename Suffix    | Display Name                 |
| ------------------ | ---------------------------- |
| aud_net_t1         | Auditory Network             |
| ec_net_t1          | EC Network                   |
| lfp_net_t1         | Left Frontoparietal Network  |
| rfp_net_t1         | Right Frontoparietal Network |
| vl_net_t1          | Visuolateral Network         |
| vm_net_t1          | Visuomedial Network          |
| vo_net_t1          | Visuooccipital Network       |
| dmn_net_t1         | Default Mode Network         |
| sm_net_t1          | Sensorimotor Network         |
| reho_t1            | ReHo                         |
| seedcorr_t1        | Seed Correlation             |
| desc-brain_mask_t1 | Brain Mask                   |

To add additional network types:

1. Add a display name to:

```python
SUFFIX_DISPLAY_NAMES
```

2. Add a color gradient index to:

```python
SUFFIX_GRADIENT_INDEX
```

3. Update `get_network_suffix()` if a new naming convention is introduced.

---

# Running the Application

Start the server:

```bash
python rsfmri_viewer.py
```

The application will launch at:

```text
http://localhost:8051
```

---

# Main Features

## Visualization

* Subject-space overlays
* MNI-space overlays
* Axial, coronal, and sagittal views
* Single-slice mode
* Mosaic mode

## Overlay Controls

For each selected network:

* Opacity adjustment
* Threshold adjustment
* Cluster-size filtering
* Custom color gradients

## Ratings

The viewer supports:

* Network-level ratings
* Overall patient ratings

Ratings can be exported as CSV files.

## DICOM Export

Supports:

### Single Image Export

Exports the current view as:

* PNG
* DICOM Secondary Capture

### Full Volume Export

Exports every axial slice as a DICOM series and packages the results into a ZIP archive.

---

# Common Setup Issues

## No Patients Appear

Check that:

```python
BASE_DIR
```

points to the correct folder.

Also verify that patient folders are direct children of `BASE_DIR`.

---

## No Overlays Appear

Confirm overlays are stored in:

```text
patient/xprx/display/
```

or

```text
patient/xprx/mni/
```

and have `.nii` or `.nii.gz` extensions.

---

## T1 Not Found

Verify that the anatomical scan filename contains one of:

```text
desc-preproc_T1w
T1w
T1
t1w
t1
```

or modify the search logic in:

```python
find_t1_file()
```

to match your naming convention.

---

# Customization

For new datasets, the most commonly modified sections are:

```python
BASE_DIR
SUFFIX_DISPLAY_NAMES
SUFFIX_GRADIENT_INDEX
get_network_suffix()
```

These determine where files are located, how overlays are labeled, and how they are displayed within the viewer.
