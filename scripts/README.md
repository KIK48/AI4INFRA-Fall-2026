# scripts/

Pipeline code, organized by stage:

```
scripts/
  preprocessing/      # Person 2 — LAS loading, ground/non-ground separation, filtering
  extraction/         # Person 3 — candidate detection, classification, geometry fitting
  fusion/             # Person 1 — multi-run fusion, confidence scoring, QA routing
  inventory/          # Person 4 — GIS export, dashboard/visualization
```

Shared, cross-stage code (the asset schema, config loading, common utilities) belongs at `scripts/common/` so no stage duplicates it.
