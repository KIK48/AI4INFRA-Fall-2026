## AI4INFRA-Fall-2026

# LiDAR Asset Management Capture Model

## Project Overview

This project is being developed for an infrastructure asset management challenge using a 3D LiDAR point cloud dataset collected from a vehicle driving through a site in Mannford, Oklahoma.

The dataset was captured using a **Trimble MX9 mobile LiDAR system** with two scanner heads.

The goal is to transform raw LiDAR point-cloud data into a structured **infrastructure asset inventory**.

The challenge specifically does **not** want only a segmented point cloud. The desired outcome is to identify physical infrastructure assets, determine their locations, classify them, and extract useful attributes.

---

# Dataset

The dataset consists of `.LAS` files using:

* LAS version: **1.4**
* Point Data Record Format: **7**
* Site: **Mannford, Oklahoma**
* System: **Trimble MX9**
* Scanner heads: **Two**
* Captured by: **WSB**

The dataset contains multiple captures:

```text
Run 1 - Laser Left
Run 1 - Laser Right

Run 2 - Laser Left
Run 2 - Laser Right
```

Each LiDAR point may contain information such as:

* X coordinate
* Y coordinate
* Z coordinate
* Laser intensity
* Return information
* RGB/color
* GPS/time information
* Other LiDAR metadata
* Point classification, if present

A `.LAS` file should be treated primarily as a collection of measured 3D points and associated metadata. The raw data does not necessarily identify complete physical objects such as "this group of points is a utility pole." One of the main goals of this project is to derive those objects from the point cloud.

---

# Data Access

The raw and processed `.LAS`/`.LAZ` files are **not stored in this repository** — they're too large for git. Folders such as `raw_data/`, `processed_data/`, and parts of `outputs/` are gitignored locally and will appear empty here.

Data is distributed via [DVC](https://dvc.org/) backed by a shared Google Drive folder — small pointer files are committed to git, and `dvc pull`/`dvc push` sync the actual LAS/LAZ bytes. See `documentation/data-distribution.md` for setup and day-to-day commands.

**Shared Drive folder:** https://drive.google.com/drive/folders/1OhM6h4Iwc1-vvrf2l_riMVn_lbJcdBon (ask to be added if you don't have access).

See `raw_data/README.md` and `processed_data/README.md` for what each folder is expected to hold, and `documentation/environment-setup.md` for setting up the Python environment (`requirements.txt`).

---

# Objective

Build a repeatable pipeline that converts raw LiDAR point-cloud data into an **asset inventory**.

The four required asset classes are:

## 1. Pavement

Examples:

* Travelled roadway
* Lane markings
* Road markings
* Pavement boundaries

## 2. Utilities

Examples:

* Utility poles
* Overhead conductors
* Utility cabinets
* Other visible utility infrastructure

## 3. Signs

Examples:

* Sign panels
* Sign posts
* Sign structures
* Overhead sign structures

## 4. Safety

Examples:

* Guardrails
* Barriers
* Rumble strips
* Other roadway safety infrastructure

---

# Important Project Principle

The goal is **asset extraction**, not simply point-cloud segmentation.

We want to go from:

```text
Millions of LiDAR points
        ↓
Groups of points representing physical objects
        ↓
Object identification/classification
        ↓
Asset attributes
        ↓
Structured asset inventory
```

For example:

```text
Raw LiDAR points
        ↓
Detected object
        ↓
Utility Pole
        ↓
Asset ID: UTIL-001
Location: X/Y/Z
Height: 9.4 m
Confidence: 94%
```

The final product should represent actual infrastructure assets rather than simply coloring points by class.

---

# Initial Technical Goals

For the first phase, focus on building the fundamentals.

## Phase 1 — Understand the LAS Data

Before developing sophisticated models, inspect the dataset and determine:

* Number of points
* Coordinate ranges
* Coordinate Reference System (CRS)
* Available point attributes
* RGB availability
* Intensity availability
* Return information
* Existing point classifications
* Point density
* Differences between the four scans

Do not assume what fields are available. Inspect the actual files.

---

# Phase 2 — Point Cloud Processing

Develop a basic processing pipeline capable of:

1. Loading LAS files
2. Reading point coordinates and metadata
3. Visualizing the point cloud
4. Filtering obvious noise
5. Identifying/classifying ground points
6. Separating ground/non-ground points
7. Creating useful subsets of the point cloud

Potential tools include:

* Python
* `laspy`
* PDAL
* Open3D
* NumPy
* SciPy

Other tools may be used if they provide a clear advantage.

---

# Phase 3 — Asset Detection

Begin developing methods for detecting the four required asset classes.

Do not attempt to build one massive model immediately.

Start with simpler geometric and data-driven approaches where appropriate.

Potential examples:

### Pavement

Use characteristics such as:

* Ground elevation
* Surface continuity
* Planar/surface geometry
* LiDAR intensity
* RGB/color
* Road position

### Utility Poles

Potential characteristics:

* Vertical orientation
* Height
* Cylindrical/linear geometry
* Ground connection
* Location relative to roadway
* Nearby overhead conductors

### Signs

Potential characteristics:

* Vertical support
* Flat planar surface
* Rectangular geometry
* Orientation
* Location relative to roadway

### Guardrails/Barriers

Potential characteristics:

* Long continuous geometry
* Height above ground
* Location near roadway boundaries
* Horizontal/linear structure

These are initial ideas, not requirements. Determine what works best after inspecting the actual data.

---

# Phase 4 — Asset Inventory

The system should eventually convert detected objects into structured records.

A basic asset schema should contain fields such as:

```json
{
  "asset_id": "UTIL-001",
  "asset_class": "Utility",
  "asset_type": "Utility Pole",
  "geometry_type": "Point",
  "coordinates": {
    "x": 0,
    "y": 0,
    "z": 0
  },
  "attributes": {},
  "confidence": 0.0
}
```

The exact schema can evolve as the project develops.

The coordinate system must be explicitly documented.

---

# Phase 5 — Verification and Evaluation

The system needs a way to determine whether extracted assets are actually correct.

Do not assume that a model prediction is automatically correct.

Create a manually verified validation area within the dataset.

For the validation area, establish a reference inventory by visually inspecting the LiDAR data.

Then compare the automated results against the reference inventory.

Evaluate:

### Detection

* True positives
* False positives
* False negatives
* Precision
* Recall

### Classification

Determine whether detected assets were assigned the correct asset class/type.

### Geospatial Accuracy

Determine how accurately the extracted asset location represents the actual asset.

### Geometry Accuracy

For linear or area assets, evaluate things such as:

* Length
* Boundary
* Spatial overlap
* Position

The project should eventually be able to report measurable results rather than simply saying that the model "looks good."

---

# Initial Success Criteria

The first version of the project should be able to:

* Read the provided LAS data
* Understand the available LiDAR attributes
* Visualize and inspect the point cloud
* Determine the coordinate system
* Process/filter the point cloud
* Identify candidate infrastructure objects
* Classify at least some of the required asset classes
* Produce structured asset records
* Store coordinates and relevant attributes
* Explain how assets were extracted
* Provide a repeatable processing procedure
* Establish a method for validating the results

The goal is **not** to achieve a perfect solution immediately.

The goal is to build a working foundation that can be evaluated and improved.

---

# Repeatability

The final workflow should be repeatable.

Another person should eventually be able to take another section of LiDAR data and follow the documented procedure to produce an asset inventory.

The workflow should therefore eventually look approximately like:

```text
LAS Input
    ↓
Preprocessing
    ↓
Ground / Non-Ground Processing
    ↓
Candidate Object Extraction
    ↓
Asset Classification
    ↓
Attribute Extraction
    ↓
Validation
    ↓
Asset Inventory
```

Document important assumptions, parameters, thresholds, and processing steps.

---

# AI/ML Strategy

AI and machine learning may be used where they provide value.

However, do not assume that a large deep-learning model is necessary for every asset.

Use the simplest reliable method for each problem.

Possible approaches include:

* Geometric rules
* Clustering
* Statistical methods
* Classical machine learning
* Point-cloud ML models
* Computer vision
* LiDAR intensity/RGB analysis
* Hybrid approaches

A hybrid system combining geometry, LiDAR metadata, and ML may be preferable to relying on a single model.

---

# Agents

**Agents are not part of the initial implementation.**

They may be added later as an enhancement after the core extraction pipeline works.

Potential future uses include:

* Asset validation
* Quality assurance
* Reviewing uncertain detections
* Cross-scan reasoning
* Identifying potential false positives
* Assisting with analyst review
* Automating parts of the processing workflow

The core system should remain functional without agents.

---

# Development Philosophy

Build incrementally.

Do not immediately attempt to solve every asset class.

Recommended progression:

```text
1. Inspect LAS data
        ↓
2. Visualize data
        ↓
3. Understand attributes/CRS
        ↓
4. Process point cloud
        ↓
5. Detect one simple asset
        ↓
6. Validate it
        ↓
7. Improve detection
        ↓
8. Add additional asset classes
        ↓
9. Build inventory
        ↓
10. Evaluate entire pipeline
        ↓
11. Add advanced AI/ML enhancements
```

Prioritize **measurable accuracy and repeatability** over unnecessary complexity.

---

# Final Challenge Deliverables

The final project is expected to contain:

## 1. Extraction Model

The trained model, configured workflow, or code used to extract/classify assets.

## 2. Asset Inventory

The assets actually extracted, using a clearly defined schema with the coordinate system explicitly stated.

## 3. Extraction and Classification Procedure

A repeatable procedure describing how another team could apply the workflow to another mile of roadway.

## 4. Enhancement Procedure

Documentation of how the approach could be improved with additional:

* Time
* Data
* Computing resources
* Training data
* Models
* Engineering

## 5. Presentation

A 10-minute presentation followed by Q&A.

The presentation should demonstrate:

* What was built
* How the LiDAR data was processed
* How assets were detected
* How assets were classified
* What the inventory looks like
* How accuracy was measured
* Limitations
* Future improvements

---

# Current Priority

**For now, focus only on understanding the data and building the basic LiDAR → asset extraction pipeline.**

Do not over-engineer the system.

The immediate goal is to answer:

> "Can we reliably take this LAS point cloud and turn groups of 3D points into real infrastructure assets with coordinates and useful attributes?"

Once that foundation works, more advanced AI/ML techniques and agent-based validation can be added.
