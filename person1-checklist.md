# Person 1 — Technical Lead / Integration Checklist

- [ ] Define the project goal in one sentence
  - Convert Trimble MX9 LiDAR into a geospatial asset inventory for pavement, utilities, signs, and safety assets.

- [ ] Define the full end-to-end pipeline
  - [ ] Input LAS files
  - [ ] Preprocessing
  - [ ] Ground / road separation
  - [ ] Feature generation
  - [ ] Asset candidate extraction
  - [ ] Asset classification
  - [ ] Geometry fitting
  - [ ] Duplicate merging across runs
  - [ ] Attribute generation
  - [ ] QA / confidence scoring
  - [ ] GIS inventory export
  - [ ] Visualization / dashboard
  - Pipeline: Raw LAS → Preprocessing → Ground / Road Separation → Feature Generation → Asset Extraction → Classification → Geometry Fitting → Multi-Run Fusion → Attribution → QA → Asset Inventory → Visualization

- [ ] Create the shared project folder structure
  - [ ] `raw_data/`
  - [ ] `processed_data/`
  - [ ] `models/`
  - [ ] `scripts/`
  - [ ] `outputs/`
  - [ ] `inventory/`
  - [ ] `documentation/`
  - [ ] `presentation/`

- [ ] Define file naming conventions
  - [ ] Run 1 Left = `R1_L`
  - [ ] Run 1 Right = `R1_R`
  - [ ] Run 2 Left = `R2_L`
  - [ ] Run 2 Right = `R2_R`
  - [ ] Use names such as `mannford_R1_L.las`
  - [ ] Use names such as `mannford_R1_R.las`
  - [ ] Use names such as `mannford_R2_L.las`
  - [ ] Use names such as `mannford_R2_R.las`

- [ ] Define the shared asset schema
  - [ ] `asset_id`
  - [ ] `asset_class`
  - [ ] `asset_type`
  - [ ] `geometry`
  - [ ] `x`
  - [ ] `y`
  - [ ] `z`
  - [ ] `confidence`
  - [ ] `source_run`
  - [ ] `source_scanner`
  - [ ] `point_count`
  - [ ] `height`
  - [ ] `width`
  - [ ] `length`
  - [ ] `orientation`
  - [ ] `condition`
  - [ ] `qa_status`
  - [ ] `notes`

- [ ] Define the four main asset classes
  - [ ] Pavement
  - [ ] Utilities
  - [ ] Signs
  - [ ] Safety

- [ ] Define Pavement asset subtypes
  - [ ] Pavement surface
  - [ ] Lane marking
  - [ ] Edge line
  - [ ] Centerline
  - [ ] Stop bar
  - [ ] Arrow / pavement symbol
  - [ ] Shoulder
  - [ ] Rumble strip

- [ ] Define Utilities asset subtypes
  - [ ] Utility pole
  - [ ] Light pole
  - [ ] Overhead conductor
  - [ ] Utility cabinet
  - [ ] Transformer / equipment

- [ ] Define Signs asset subtypes
  - [ ] Sign panel
  - [ ] Sign post
  - [ ] Multi-post structure
  - [ ] Overhead sign structure

- [ ] Define Safety asset subtypes
  - [ ] Guardrail
  - [ ] Guardrail terminal
  - [ ] Concrete barrier
  - [ ] Cable barrier
  - [ ] Delineator
  - [ ] Rumble strip

- [ ] Coordinate with Person 2 — LiDAR / Data Processing
  - [ ] Confirm the coordinate reference system
  - [ ] Preserve raw coordinates
  - [ ] Generate ground classification
  - [ ] Generate non-ground classification
  - [ ] Generate height above ground
  - [ ] Preserve intensity
  - [ ] Preserve RGB if available
  - [ ] Preserve return information
  - [ ] Preserve scanner ID
  - [ ] Preserve run ID
  - [ ] Document noise filtering method
  - [ ] Agree that Person 2 outputs processed LiDAR with ground classification, height above ground, intensity, RGB, return information, run ID, and scanner ID

- [ ] Coordinate with Person 3 — Asset Extraction / ML
  - [ ] Define what each detection must contain
  - [ ] Candidate ID
  - [ ] Asset class
  - [ ] Asset subtype
  - [ ] Geometry or centroid
  - [ ] X coordinate
  - [ ] Y coordinate
  - [ ] Z coordinate
  - [ ] Confidence score
  - [ ] Supporting point count
  - [ ] Source run
  - [ ] Source scanner

- [ ] Coordinate with Person 4 — Inventory / GIS / Visualization
  - [ ] Decide the final GIS format
  - [ ] Decide whether to use GeoJSON
  - [ ] Decide whether to use GeoPackage
  - [ ] Decide whether to use CSV
  - [ ] Decide whether LAS / LAZ is needed
  - [ ] Make sure the coordinate system is included
  - [ ] Make sure geometry is map-ready
  - [ ] Make sure required attributes are included
  - [ ] Make sure QA status is included
  - [ ] Make sure confidence is included

- [ ] Choose integration formats
  - [ ] CSV for tabular information
  - [ ] GeoJSON for simple GIS geometry
  - [ ] GeoPackage for the final GIS inventory
  - [ ] LAS / LAZ for point-cloud outputs
  - [ ] JSON or YAML for pipeline settings

- [ ] Create a small test area before processing the full site
  - [ ] Pick a short roadway section
  - [ ] Include at least one sign
  - [ ] Include at least one pole
  - [ ] Include pavement markings
  - [ ] Include guardrail or another safety asset if possible
  - [ ] Include multiple scanner observations if possible

- [ ] Run the full pipeline on the test area
  - [ ] Person 2 preprocesses the LiDAR
  - [ ] Person 3 extracts the assets
  - [ ] Integrate the extracted results
  - [ ] Person 4 creates the inventory
  - [ ] Visualize the results
  - [ ] Identify broken handoffs
  - [ ] Fix inconsistent fields
  - [ ] Fix inconsistent coordinate systems
  - [ ] Document problems found

- [ ] Create duplicate detection / multi-run fusion logic
  - [ ] Compare detections from `R1_L`
  - [ ] Compare detections from `R1_R`
  - [ ] Compare detections from `R2_L`
  - [ ] Compare detections from `R2_R`
  - [ ] Define a spatial tolerance
  - [ ] Compare asset classes
  - [ ] Compare geometry
  - [ ] Merge duplicate detections
  - [ ] Preserve the original source observations
  - [ ] Count how many runs/scanners detected the same asset
  - [ ] Example: if one utility pole appears in all four datasets, set Detection Support = 4/4

- [ ] Create a confidence scoring framework
  - [ ] Classification confidence
  - [ ] Geometry fit quality
  - [ ] Point support
  - [ ] Multi-run agreement
  - [ ] Attribute completeness
  - [ ] Consider a formula such as: Final Confidence = 0.35 × Classification Confidence + 0.25 × Geometry Quality + 0.20 × Multi-Run Agreement + 0.20 × Point Support

- [ ] Define QA rules
  - [ ] Confidence ≥ 0.90 → Auto Accept
  - [ ] Confidence 0.70–0.89 → Visual Review
  - [ ] Confidence < 0.70 → Manual Review
  - [ ] Keep rejected detections documented
  - [ ] Track manual corrections

- [ ] Define accuracy metrics
  - [ ] Precision
  - [ ] Recall
  - [ ] False positives
  - [ ] False negatives
  - [ ] Horizontal positional error
  - [ ] Vertical positional error
  - [ ] Attribute completeness
  - [ ] Detection support across runs
  - [ ] Precision = True Positives / (True Positives + False Positives)
  - [ ] Recall = True Positives / (True Positives + False Negatives)

- [ ] Create a validation sample
  - [ ] Manually identify the true assets in a small section
  - [ ] Create a ground-truth inventory
  - [ ] Compare automated detections against the ground truth
  - [ ] Count true positives
  - [ ] Count false positives
  - [ ] Count false negatives
  - [ ] Measure positional error
  - [ ] Calculate precision
  - [ ] Calculate recall
  - [ ] Record common failure cases

- [ ] Track all important pipeline parameters
  - [ ] Ground threshold
  - [ ] Cluster distance
  - [ ] Minimum points per cluster
  - [ ] Maximum points per cluster
  - [ ] Pole minimum height
  - [ ] Pole diameter range
  - [ ] Sign plane threshold
  - [ ] Guardrail height threshold
  - [ ] Pavement intensity threshold
  - [ ] Duplicate merge tolerance
  - [ ] Confidence thresholds

- [ ] Store the pipeline parameters in a configuration file
  - Example settings:
  - `cluster_distance = 0.25 m`
  - `minimum_points = 30`
  - `pole_min_height = 2.0 m`
  - `duplicate_tolerance = 0.15 m`

- [ ] Maintain a decision log
  - [ ] Date
  - [ ] What changed
  - [ ] Why it changed
  - [ ] Who changed it
  - [ ] Old method or value
  - [ ] New method or value
  - [ ] Result of the change

- [ ] Maintain versioning
  - [ ] Pipeline v1
  - [ ] Pipeline v2
  - [ ] Pipeline v3
  - [ ] Extraction Model v1
  - [ ] Extraction Model v2
  - [ ] Inventory v1
  - [ ] Inventory v2
  - [ ] Final Submission

- [ ] Create the final architecture diagram
  - Trimble MX9 LAS Data → Preprocessing → Ground / Road Separation → Feature Generation → Asset Candidate Detection → Asset Classification → Geometry Fitting → Multi-Run Fusion → Attribute Generation → Confidence / QA → Asset Inventory → GIS / Dashboard

- [ ] Make the architecture diagram presentation-ready
- [ ] Keep terminology consistent with the actual workflow
- [ ] Make sure each team member can explain where their work fits

- [ ] Check Extraction Accuracy rubric coverage
  - [ ] Precision is measured
  - [ ] Recall is measured
  - [ ] Positional accuracy is measured
  - [ ] False positives are documented
  - [ ] False negatives are documented

- [ ] Check Policy and Procedure rubric coverage
  - [ ] Full pipeline is documented
  - [ ] Parameters are documented
  - [ ] Input/output formats are documented
  - [ ] Procedure is repeatable
  - [ ] Another team could apply the same workflow to the next mile

- [ ] Check Attribution Completeness rubric coverage
  - [ ] Asset class is included
  - [ ] Asset type is included
  - [ ] Geometry is included
  - [ ] Coordinates are included
  - [ ] Dimensions are included when possible
  - [ ] Confidence is included
  - [ ] Source information is included
  - [ ] Condition is included when possible
  - [ ] QA status is included

- [ ] Check Innovation rubric coverage
  - [ ] Multi-run validation is documented
  - [ ] Confidence scoring is documented
  - [ ] Automatic duplicate fusion is documented
  - [ ] Active learning is included as a possible enhancement
  - [ ] Future model improvement strategy is included

- [ ] Prepare Person 1 presentation responsibilities
  - [ ] Explain the overall system architecture
  - [ ] Explain why the pipeline is structured this way
  - [ ] Explain how each team member's work connects
  - [ ] Explain how data moves through the system
  - [ ] Explain how duplicate assets are merged
  - [ ] Explain how confidence is calculated
  - [ ] Explain how QA works
  - [ ] Explain how the final inventory is generated
  - [ ] Explain how the approach can be repeated on another mile

- [ ] Complete these immediate priorities first
  - [ ] Create the shared project folder structure
  - [ ] Define the shared asset schema
  - [ ] Draw the end-to-end pipeline
  - [ ] Agree with Person 2 on preprocessing output
  - [ ] Agree with Person 3 on extraction output
  - [ ] Agree with Person 4 on inventory output
  - [ ] Select one small test section
  - [ ] Make the full pipeline work on the test section

- [ ] Confirm final success criteria
  - [ ] Raw LiDAR goes into the pipeline
  - [ ] Preprocessed LiDAR comes out correctly
  - [ ] Assets are detected and classified
  - [ ] Duplicate detections are merged
  - [ ] Asset attributes are generated
  - [ ] Confidence scores are assigned
  - [ ] QA rules are applied
  - [ ] Final GIS inventory is generated
  - [ ] Coordinate system is explicitly documented
  - [ ] Accuracy is measured
  - [ ] The process is repeatable
  - [ ] The team can explain the full system during the presentation

- [ ] Final Goal: Raw LiDAR → Reliable, repeatable, geospatial asset inventory