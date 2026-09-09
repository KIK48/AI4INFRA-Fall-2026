# Data Distribution

LAS/LAZ files are too large for git, but the team still needs a reliable way to hand processed data between pipeline stages (Person 2 → Person 3 → Person 4) without emailing files around or losing track of which version is current.

**Approach: [DVC](https://dvc.org/) tracking a shared Google Drive folder.**

- Git tracks small pointer files (`*.dvc`) — so `git log` shows *when* a dataset changed and in which commit, same as code.
- The actual bytes live in the shared Google Drive folder, not in git.
- Anyone can run one command to fetch the exact data version referenced by the commit they have checked out, instead of hunting for the right file in a shared folder by hand.

## One-time setup (already done in this repo)

```bash
pip install -r requirements.txt   # includes dvc[gdrive]
dvc init                          # already run — .dvc/ is committed
```

## Remote configuration

**Status: configured.** The shared Google Drive folder is registered as the default DVC remote:

* Folder: https://drive.google.com/drive/folders/1OhM6h4Iwc1-vvrf2l_riMVn_lbJcdBon
* Remote name: `gdrive_remote` (set as `core.remote` default in `.dvc/config`, committed to git)

Make sure you have access to that folder (ask whoever created it to share it with your Google account) before running `dvc pull`/`dvc push` — the first run opens a browser window for Google OAuth.

## Day-to-day usage, once configured

When you add or update data in `raw_data/` or `processed_data/`:

```bash
dvc add raw_data          # or processed_data, or a specific file
git add raw_data.dvc .gitignore
git commit -m "Add Run 1/2 raw LAS captures"
dvc push                  # uploads the actual bytes to Google Drive
```

When you pull the latest code and need the matching data:

```bash
git pull
dvc pull                  # downloads whatever version .dvc files point to
```

The first time each teammate runs `dvc pull` or `dvc push` against the Google Drive remote, DVC opens a browser window for Google OAuth — each person authenticates with their own Google account (must have access to the shared folder).
