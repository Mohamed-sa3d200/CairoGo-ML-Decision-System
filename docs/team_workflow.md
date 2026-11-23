# Team Workflow

## Branches
- `main`: stable, reviewed work only.
- Each task uses a short-lived branch: `feature/audit-join-loss`, `feature/model-baselines`, `feature/decision-policy`, etc.

## Before committing
1. Pull the latest `main`.
2. Make changes only within your assigned scope.
3. Run the relevant checks/notebook cells.
4. Commit with a descriptive message.
5. Push your branch and open a Pull Request.
6. Ask another member to review; team lead merges after checks.

## Avoid conflicts
- Coordinate edits to shared files (`team_analysis.ipynb`, `results.json`, README) before touching them.
- Prefer reusable Python modules in `src/` over duplicating code in notebooks.
- Never commit raw data, review text, credentials, or local environment files.

## Reproducibility
Record random seeds, time-based split boundaries, feature lists, preprocessing steps, model settings, and metrics. Re-run from a clean environment before submission.
