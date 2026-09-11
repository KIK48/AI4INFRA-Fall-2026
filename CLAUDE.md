# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository state

This repo currently contains **no pipeline code yet** — `README.md` (the project brief), `person1-checklist.md` (the technical lead's integration checklist), the scaffolded folder structure (each with its own README), and environment/data tooling (below) exist; `scripts/` is still empty. There is no test suite yet — when adding the first code, also add a test runner and record the commands here.

**LAS/LAZ data files are not committed** — `raw_data/`, `processed_data/`, and LAS/LAZ outputs are gitignored (they're too large for git). Data is synced via DVC against a shared Google Drive remote (configured, see `documentation/data-distribution.md`); don't assume the data sits locally on disk unless a teammate confirms it or `dvc pull` has been run.

## Commands

```bash
# Environment setup (see documentation/environment-setup.md)
python -m venv .venv && source .venv/bin/activate   # or .venv\Scripts\Activate.ps1 on Windows
pip install -r requirements.txt

# Data sync (documentation/data-distribution.md) — first run opens a browser for Google OAuth
dvc pull    # fetch the data version referenced by the current git commit
dvc push    # publish local data changes after `dvc add <path>` + git commit
```

Note: `dvc`/`python -m dvc` may not be on PATH after a fresh `pip install` on Windows — use `python -m dvc ...` if the bare `dvc` command isn't found.

## What this project is

A LiDAR → infrastructure asset inventory pipeline for a Fall 2026 challenge. Input is `.LAS` v1.4 (Point Data Record Format 7) captured by a Trimble MX9 mobile LiDAR system in Mannford, Oklahoma. Four captures exist: two runs × two scanner heads.

The critical framing: **the deliverable is asset extraction, not point-cloud segmentation.** Coloring points by class is not the output. Discrete physical objects with IDs, coordinates, dimensions, and confidence scores are.

## Pipeline architecture

```
Raw LAS → Preprocessing → Ground/Road Separation → Feature Generation
  → Asset Candidate Detection → Classification → Geometry Fitting
  → Multi-Run Fusion → Attribution → Confidence/QA → Asset Inventory → GIS/Dashboard
```

The work is split across four people, and the stage boundaries are contracts between them:

- **Person 2 (LiDAR processing)** outputs processed point clouds carrying: ground classification, height above ground, intensity, RGB, return information, run ID, scanner ID. Raw coordinates must be preserved through every stage.
- **Person 3 (extraction/ML)** outputs detections carrying: candidate ID, asset class, asset subtype, geometry or centroid, x/y/z, confidence, supporting point count, source run, source scanner.
- **Person 4 (inventory/GIS)** consumes those into the final map-ready inventory.

When editing code at a stage boundary, preserve the full field set above — downstream stages (especially multi-run fusion and confidence scoring) depend on `source_run` / `source_scanner` / `point_count` surviving intact.

**Multi-run fusion** is the step that distinguishes this design: the same physical asset seen in `R1_L`, `R1_R`, `R2_L`, `R2_R` must be merged into one record while preserving the original per-run observations, with a detection-support count (e.g. 4/4).

## Conventions to follow

**File naming** — the four delivered captures (`Run 1 - Laser Left.las`, `Run 1 - Laser Right.las`, `Run 2 - Laser Left.las`, `Run 2 - Laser Right.las`) are kept under those original names in `raw_data/`. Internally (scripts, processed/output filenames, docs) they're referred to with short codes `R1_L`, `R1_R`, `R2_L`, `R2_R` — that mapping is documentation-only, it doesn't rename the raw files.

**Directory layout**: `raw_data/`, `processed_data/`, `models/`, `scripts/` (subfolders `preprocessing/`, `extraction/`, `fusion/`, `inventory/`, `common/` — see `scripts/README.md`), `outputs/`, `inventory/`, `documentation/`, `presentation/`. Each has its own README.

**Asset schema** — records carry: `asset_id`, `asset_class`, `asset_type`, `geometry`, `x`, `y`, `z`, `confidence`, `source_run`, `source_scanner`, `point_count`, `height`, `width`, `length`, `orientation`, `condition`, `qa_status`, `notes`. Asset IDs look like `UTIL-001`.

**Asset classes and subtypes** (four classes, fixed):
- *Pavement* — pavement surface, lane marking, edge line, centerline, stop bar, arrow/pavement symbol, shoulder, rumble strip
- *Utilities* — utility pole, light pole, overhead conductor, utility cabinet, transformer/equipment
- *Signs* — sign panel, sign post, multi-post structure, overhead sign structure
- *Safety* — guardrail, guardrail terminal, concrete barrier, cable barrier, delineator, rumble strip

**Confidence and QA** — the intended scoring formula is
`0.35 × classification + 0.25 × geometry quality + 0.20 × multi-run agreement + 0.20 × point support`,
with QA routing at ≥0.90 auto-accept, 0.70–0.89 visual review, <0.70 manual review. Rejected detections and manual corrections stay documented rather than being dropped.

**Formats** — CSV for tabular, GeoJSON for simple geometry, GeoPackage for the final GIS inventory, LAS/LAZ for point-cloud outputs, JSON or YAML for pipeline settings.

**Tooling** — Python with `laspy`, PDAL, Open3D, NumPy, SciPy is the expected stack. Other tools are allowed where they give a clear advantage.

## Working rules specific to this project

- **Inspect the data; don't assume its fields.** The brief explicitly warns against assuming which attributes (RGB, intensity, returns, existing classifications) are present or what the CRS is. Read the actual LAS headers first.
- **The CRS must be explicit** in every output that carries coordinates. Never emit an inventory without stating it.
- **Every tunable parameter goes in a config file**, not inline in code — ground threshold, cluster distance, min/max points per cluster, pole min height, pole diameter range, sign plane threshold, guardrail height threshold, pavement intensity threshold, duplicate merge tolerance, confidence thresholds. Starting values from the checklist: `cluster_distance = 0.25 m`, `minimum_points = 30`, `pole_min_height = 2.0 m`, `duplicate_tolerance = 0.15 m`.
- **Prefer the simplest reliable method per asset.** Geometric rules and clustering are the expected starting point; a deep-learning model is not assumed to be necessary for any given class. Hybrid geometry + metadata + ML is preferred over one large model.
- **Agents are explicitly out of scope for the initial implementation.** The core pipeline must work standalone; agent-based validation is a later enhancement.
- **Validate against a manually built ground truth**, not against eyeballing. Precision, recall, false positives/negatives, and horizontal/vertical positional error are the metrics that matter.
- **Develop against a small test section first** (one containing at least a sign, a pole, pavement markings, and ideally a safety asset) before touching the full site.
- **Repeatability is a graded deliverable.** Document assumptions, parameters, thresholds, and steps as they're chosen — another team must be able to run the same procedure on a different mile of roadway.
