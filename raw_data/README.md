# raw_data/

Untouched input `.LAS` files as delivered (LAS v1.4, Point Data Record Format 7), captured by WSB with a Trimble MX9 mobile LiDAR system (two scanner heads) over two runs:

- `Run 1 - Laser Left.las`
- `Run 1 - Laser Right.las`
- `Run 2 - Laser Left.las`
- `Run 2 - Laser Right.las`

Keep the original delivered filenames — treat this folder as read-only source data, never edit or overwrite in place. LAS/LAZ files are gitignored; this folder is populated locally/manually, not committed.

**This folder is empty in the git repo.** The actual files live externally — see "Data Access" in the root `README.md` (location TBD).

Internally (scripts, outputs, docs) these four captures are referred to with short codes: `R1_L`, `R1_R`, `R2_L`, `R2_R`. That mapping is documentation-only — it does not rename the files here.
