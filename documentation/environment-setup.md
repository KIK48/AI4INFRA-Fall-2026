# Environment Setup

Everyone on the team should work inside the same virtual environment definition (`requirements.txt` at the repo root) so scripts behave identically across machines.

## Windows (PowerShell)

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## macOS/Linux (bash)

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

`.venv/` is gitignored — never commit it. If you add a new dependency, add it to `requirements.txt` (with a version) in the same commit as the code that needs it, so everyone else's environment stays in sync on their next `pip install -r requirements.txt`.

## PDAL (optional)

PDAL is not reliably pip-installable on Windows. If a stage needs it, install via conda instead:

```bash
conda install -c conda-forge pdal python-pdal
```

## DVC (data sync)

DVC is included in `requirements.txt`. See `documentation/data-distribution.md` for how it's configured and how to pull/push data.
