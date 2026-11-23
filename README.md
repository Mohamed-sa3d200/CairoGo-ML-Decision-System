# CairoGo ML & Decision System

Private team repository for the CairoGo end-to-end ML assignment.

## Team
- Team leader: add name / student ID
- Member 2: add name / student ID
- Member 3: add name / student ID
- Member 4: add name / student ID
- Member 5: add name / student ID

## Project principles
- Do not commit raw datasets, review text, credentials, or private assignment materials.
- Work on feature branches; merge via Pull Requests into `main`.
- Keep results reproducible; record seeds, splits, metrics, and decisions.
- Avoid target leakage: features must be available at prediction time.
- Do not commit large model artifacts unless the team explicitly agrees and the assignment requires them.


## Repository layout
- `docs/`: workflow, audit notes, data dictionary notes
- `notebooks/`: team analysis notebook
- `src/`: reusable audit, feature, model, and decision code
- `service/`: API and Docker service
- `dashboard/`: dashboard application
- `tests/`: tests for pipeline, predictor, policy
- `data/`: local-only data folders (ignored by Git)
- `artifacts/`: local model artifacts (ignored by Git by default)
- `outputs/`: generated local outputs (ignored by Git by default)

## Setup
```bash
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# macOS/Linux:
# source .venv/bin/activate
pip install -r requirements.txt
```

Put assignment data locally under `data/raw/`; do not upload it to GitHub.


## Team workflow
See `docs/team_workflow.md` before making changes.\n
