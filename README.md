# CairoGo — Team Project Engineering Guide

**Project:** CairoGo — Team Prediction, Decisions & Application  
**Document type:** Team Master Guide / Project Playbook  
**Version:** 1.0  
**Status:** Working baseline — verify final requirements against the official assignment brief  
**Audience:** All team members, team leader, ML contributors, application contributors, reviewers  

---


## 1. Executive Summary

CairoGo is an end-to-end team Machine Learning project that combines data understanding and quality auditing, predictive modeling, customer/record segmentation, decision-making logic, a prediction interface, and a user-facing application/dashboard. The objective is not simply to train a model: the team must turn data into reliable predictions, translate predictions into defensible actions, expose the functionality through a usable application, test the system, and deliver a reproducible submission.


### What we are building

We will develop a cohesive ML decision-support system with the following stages:

1. **Understand and audit the data** — profile the data, validate joins, investigate missingness and anomalies, identify potential leakage, and document risks.
2. **Prepare features and define a reproducible pipeline** — make transformations explicit, avoid leakage, and ensure the same preprocessing is used during training and inference.
3. **Train and evaluate predictive models** — establish a baseline, compare candidate models, select metrics aligned with the business objective, and document limitations.
4. **Analyze segments and local behavior** — investigate groups with different characteristics and, where required, local-neighborhood methods or segment-specific analyses.
5. **Convert model outputs into decisions** — implement a transparent decision policy using explicit rules, constraints, and thresholds rather than treating a probability as an action by itself.
6. **Deliver a prediction service and application** — make inference available through a documented interface and build a dashboard or application for interacting with results.
7. **Test, integrate, and package** — validate components, run end-to-end checks, document the setup, and prepare the required submission package.

The project is complete only when the work is integrated, reproducible, tested, documented, and packaged according to the official assignment requirements.


## 2. Project Goals and Success Criteria

### 2.1 Technical goals

- Build a repeatable data preparation and model-training workflow.
- Produce predictions using a clearly defined input schema.
- Compare models with a consistent validation strategy and appropriate metrics.
- Investigate data quality, join quality, temporal shifts, leakage, and confounding risks.
- Create segmentation/analysis outputs that support interpretation and decision-making.
- Implement decision logic in a separate, testable module.
- Expose inference through a service/application interface.
- Containerize the service where required and make the environment reproducible.
- Add automated tests for key logic and API behavior.

### 2.2 Team and delivery goals

- Every member has a defined owner area and contributes to the ML work, not only UI or packaging.
- Work is tracked in Git using branches and reviewed merges.
- The repository remains understandable and runnable by another person.
- Findings and decisions are documented rather than kept only in chat or notebooks.
- The final ZIP contains the required files at the correct locations and passes the official checker, if provided.

### 2.3 Definition of Done

A component is considered done when:

- Its implementation is committed to the team repository.
- Its inputs, outputs, assumptions, and limitations are documented.
- It has been tested with normal cases and relevant edge cases.
- It can be run using documented instructions.
- It does not silently duplicate another component's responsibilities.
- Its integration contract is agreed with the team.

The overall project is done when all component DoD criteria are met, integration checks pass, the documentation is updated, and the official submission checklist is satisfied.


## 3. Business / Problem Framing

The project should be treated as a decision-support workflow. A prediction is an estimate produced by a model; it is not automatically the correct business action. The application must keep the distinction clear:

- **Input data:** information available when a prediction is requested.
- **Features:** validated and transformed inputs used by the model.
- **Model output:** prediction and, if supported, a probability or score.
- **Decision:** action selected by policy/rules based on model output, context, and constraints.
- **Explanation:** human-readable reason or rule summary where feasible.

The team must use the official assignment's target definition, data dictionary, and decision requirements. Do not invent target labels, business constraints, or unavailable-at-request-time fields. If a requirement is unclear, record it as an open question and confirm it before implementation.


## 4. Scope

### 4.1 In scope

- Data loading, validation, profiling, and audit documentation.
- Join validation and join-loss analysis.
- Missing-value, duplicate, range, and anomaly investigation.
- Time-based data-shift investigation where relevant to the supplied data.
- Leakage risk analysis and feature availability review.
- Confounding and subgroup investigation where appropriate.
- Feature engineering and reproducible preprocessing.
- Baseline and candidate model development and evaluation.
- Segmentation and local/segment-level analysis required by the assignment.
- Prediction and decision-policy logic.
- Service/API, application/dashboard, containerization, and tests where required.
- Team integration, documentation, and submission packaging.

### 4.2 Out of scope unless explicitly required

- Production deployment to a real external cloud environment.
- Real customer-facing operation or real-world decisions.
- Use of external data not authorized by the assignment.
- Claiming model performance beyond the evaluated dataset.
- Building unrelated infrastructure that does not improve assignment delivery.


## 5. End-to-End System Overview

The expected logical flow is:


```text
Provided Data / Request Payload
            |
            v
    Data Audit & Validation
            |
            v
  Join / Cleaning / Feature Prep
            |
            v
     Feature Transformation
            |
            v
  Trained Model + Evaluation
            |
            v
 Prediction (score / probability)
            |
            v
   Decision Policy / Rules
            |
            v
   API / Application Layer
            |
            v
 Dashboard / User Interaction
            |
            v
 Logs, Tests, Documentation, Outputs
```

This diagram is a conceptual pipeline. The actual order and dependencies must be adjusted to the official dataset and assignment specification. Training-only operations must not be executed in the inference request path.


## 6. Workstreams and Ownership

Ownership means a person is responsible for implementing, documenting, and coordinating that workstream. It does not mean that other members are prohibited from reviewing or contributing.

| Workstream | Primary responsibilities | Expected outputs | Dependencies |
|---|---|---|
| Team Lead & Data Audit | Repository coordination; data inventory; data quality; join loss; unit/time shift; leakage; confounding review; integration oversight | Audit notebook/scripts; audit report; findings JSON; data notes; integration checklist | Dataset, official brief, shared schema |
| Model Training & Evaluation | Baseline; preprocessing pipeline; candidate models; validation; metric comparison; reproducibility | Training notebook/scripts; evaluation table; selected model and configuration; model notes | Audited data, target definition, agreed split |
| Segmentation & Local Analysis | Segmentation; cluster/group profiling; local-neighborhood analysis where required; interpret segment differences | Segment outputs; analysis; plots/tables; methodology notes | Prepared features, agreed variables, model/data interfaces |
| Drift / NLP / Decision Policy | Recent-period analysis; text/review analysis if supplied; policy design; rules and thresholds; decision testing | Drift analysis; text analysis; policy module; rules configuration; decision output | Data and model outputs; official policy constraints |
| Prediction Service & App | Predictor integration; input validation; service endpoint; Docker; app/dashboard; integration and tests | Prediction script; API/app; Docker files; tests; service documentation; screenshots | Model artifact; feature schema; decision interface |

**Note:** The official assignment may prescribe a different number of members, file ownership, or specific methods. Confirm this table with the team and assignment brief before treating it as final. All members must contribute to the Machine Learning portion as required by the assignment.


## 7. Workstream Details

### 7.1 Workstream A — Team Lead & Data Audit

**Objective:** Establish whether the supplied data is trustworthy enough to use, identify risks before modeling, and create a documented audit trail.

**Tasks:**

1. Inventory all files, tables, and columns; record row counts and data types.
2. Review identifiers, keys, target, timestamp columns, units, and expected relationships.
3. Profile missing values, duplicates, invalid values, ranges, and category cardinality.
4. Validate joins between relevant datasets and quantify unmatched records (join loss) before and after any justified correction.
5. Investigate temporal patterns and possible unit shifts or distribution changes.
6. Review each feature for availability at prediction time and potential target leakage.
7. Investigate confounding or suspicious associations; document hypotheses and evidence without making causal claims from correlation alone.
8. Create audit artifacts and concise findings for the team.
9. Review the modeling split and evaluation design for leakage risks.
10. Coordinate integration, track blockers, and keep the team guide current.

**Expected artifacts:**
- `team_analysis.ipynb` (shared analysis notebook; keep sections clearly labeled)
- `results.json` (audit/analysis results in the required schema)
- `docs/data_audit_report.md`
- `docs/data_dictionary_notes.md`
- Reusable audit code under `src/audit/` where appropriate
- Updated project README and integration notes


### 7.2 Workstream B — Model Training & Evaluation

**Objective:** Establish a credible baseline and evaluate candidate models with a consistent and defensible methodology.

**Tasks:**
1. Confirm the target, prediction unit, and intended use with the team.
2. Define a train/validation/test strategy appropriate to the data (respect time or grouping where needed).
3. Create a simple baseline before adding complexity.
4. Build preprocessing and modeling steps in a reproducible pipeline.
5. Train required candidate models (the exact required algorithms must be confirmed from the assignment brief; examples may include Logistic Regression and gradient-boosted tree models).
6. Compare models with the assignment's required metrics and additional relevant metrics.
7. Review threshold behavior and trade-offs, not only a single headline metric.
8. Check calibration and error patterns if probabilities are used by the decision policy.
9. Record random seeds, configuration, versions, and evaluation results.
10. Save only the model artifacts needed for inference and reproducibility.

**Expected artifacts:**
- Model training notebook(s) and/or scripts
- Reusable preprocessing and prediction code
- Evaluation results and plots/tables
- Selected model artifact(s), if allowed and required
- Model card or short model documentation

**Model selection principle:** Select based on the official metric, validation design, stability, interpretability/operational constraints, and policy needs—not solely on whichever score is largest.


### 7.3 Workstream C — Segmentation & Local Analysis

**Objective:** Identify meaningful groups or local patterns that help explain behavior and support the assignment's analysis.

**Tasks:**
1. Confirm the required segmentation method and fields.
2. Select and justify features used for grouping; exclude identifiers and inappropriate variables.
3. Scale/transform features when the chosen method requires it.
4. Evaluate candidate segment counts or settings using the assignment's criteria and domain interpretability.
5. Profile each segment using interpretable summaries.
6. Examine segment size, stability, and whether results are dominated by outliers.
7. Perform local-neighborhood analysis (e.g., nearest-neighbor/local-k) only as specified and document its parameters.
8. Connect findings to model behavior or decisions without overclaiming causality.
9. Export segment assignments using the agreed output schema.

**Expected artifacts:**
- Segmentation notebook/script
- `segments.csv` in the required format
- Segment profiles and visualizations
- Methodology and limitations notes


### 7.4 Workstream D — Temporal/Review Analysis & Decision Policy

**Objective:** Investigate relevant recent-period or text signals and implement a transparent decision policy that turns predictions into actions.

**Tasks:**
1. Confirm which temporal fields and review/text data exist and are allowed.
2. Analyze recent-period performance or distribution changes, with a valid comparison period.
3. Analyze text/reviews only if the dataset and assignment require it; document preprocessing and limitations.
4. Define policy inputs and outputs with the service/model team.
5. Translate assignment constraints into explicit rules and configuration.
6. Define thresholds and their rationale; avoid unexplained hard-coded values.
7. Implement policy logic as a standalone function/module.
8. Test boundary conditions, missing values, contradictory rules, and invalid inputs.
9. Produce decision outputs and a traceable rule/reason where required.
10. Document limitations, fairness/robustness concerns, and assumptions.

**Expected artifacts:**
- Temporal/drift analysis and/or text analysis notebook/script
- `decisions.csv` in the required schema
- `policy.py`
- `DECISION_RULES.json`
- Policy tests and documentation

**Important:** The policy must not be hidden inside the web application. Keep it independently testable so the model, service, and dashboard can use the same logic.


### 7.5 Workstream E — Prediction Service & Dashboard

**Objective:** Provide a reliable interface for predictions and decisions, with a simple application/dashboard for demonstrating the end-to-end workflow.

**Tasks:**
1. Agree on an input contract (required fields, types, validation, optional fields).
2. Load model artifacts and preprocessing once at service startup where appropriate.
3. Implement prediction/inference without retraining on each request.
4. Apply the shared decision policy using the agreed contract.
5. Return a documented response format and handle invalid input safely.
6. Add health/status endpoint(s) if the chosen framework supports them and the team needs them.
7. Build a user interface that calls the service or uses the agreed inference interface.
8. Display input, prediction/score, decision, and relevant explanation/limitations.
9. Containerize the service/application as required.
10. Write tests for predictor, policy integration, API validation, and key app flows.
11. Provide reproducible run instructions and a demonstration screenshot.

**Expected artifacts (confirm exact required names and paths):**
- `predict.py`
- Service/application code (for example, under `service/`)
- `Dockerfile`
- `requirements.txt`
- `test_request.py`
- `README_SERVICE.md`
- Dashboard/app code (`app.py` or `index.html`, depending on the required framework)
- `summary.json`
- `dashboard.png`


## 8. Data Audit Framework

The audit must report facts, evidence, severity, and recommended actions. Avoid making a repair silently: preserve the original finding, explain the decision, and show the effect of any correction.

### 8.1 Data inventory and profiling

For every dataset:
- File name and purpose (known or inferred, labeled accordingly).
- Row count and column count.
- Column name and data type.
- Null/missing count and percentage.
- Unique count/cardinality.
- Duplicate rows and duplicate keys.
- Numeric ranges and suspicious values.
- Category frequency and rare categories.
- Date/time range and timezone assumptions.
- Key/relationship assumptions.

### 8.2 Join loss

For every important join:
- State the join keys and expected relationship.
- Record row counts before the join and after the join.
- Count unmatched rows on each side.
- Calculate the unmatched percentage using a clearly defined denominator.
- Inspect examples of unmatched keys (do not expose sensitive information in documentation).
- Determine whether unmatched records are expected, data-quality issues, or mapping problems.
- If a repair is made, document the rule and compare counts before/after.

### 8.3 Unit shift and temporal anomalies

- Identify columns whose units or measurement conventions may change.
- Plot/compare distributions by time period where useful.
- Inspect impossible or implausible values and abrupt changes.
- Check whether a shift is real, a source-system change, a unit conversion issue, or a sampling effect.
- Do not convert values based on a guess; validate the unit assumption and document the conversion.

### 8.4 Leakage review

For each candidate feature, ask:
- Was this value available at the time the prediction would be made?
- Is it created after the target event or influenced by the target?
- Does it directly encode the label or a post-outcome status?
- Does it contain identifiers or proxy information that can cause memorization?
- Could preprocessing, splitting, feature selection, or target encoding use information from validation/test data?

Maintain a feature review table with: feature, meaning, source, availability time, leakage risk, evidence, decision (keep/remove/needs confirmation), and rationale.

### 8.5 Confounding and subgroup investigation

- Identify variables associated with both the target and other candidate predictors where relevant.
- Compare relevant groups and stratified summaries.
- Check whether a relationship changes after controlling for a plausible variable or stratifying by group.
- Distinguish association, predictive usefulness, and causal claims.
- Record limitations and follow-up questions.

### 8.6 Audit finding format

Use this template for each issue:

| Field | Description |
|---|---|
| Finding ID | Stable identifier, e.g. AUD-001 |
| Topic | Join loss / missingness / shift / leakage / confounding / other |
| Severity | Critical / High / Medium / Low (team-defined scale) |
| Evidence | Counts, percentages, table/plot, or reproducible test |
| Impact | How this may affect data, model, evaluation, or decisions |
| Recommendation | Proposed action |
| Owner | Responsible teammate |
| Status | Open / Investigating / Resolved / Accepted risk |
| Resolution | What changed and evidence after resolution |


## 9. Modeling and Evaluation Principles

- Start with a baseline that is easy to reproduce and understand.
- Define the target and prediction unit before training.
- Use a split that reflects how the model will be used; avoid random splitting when time/group leakage makes it inappropriate.
- Fit preprocessing only on training data and apply the fitted transformations to validation/test data.
- Do not select features, thresholds, or models based on the final test set.
- Keep experiment configurations and results in a structured format.
- Report metrics required by the assignment. Depending on the problem, useful metrics may include AUC, precision, recall, recall at a specified top-k, log loss, Brier score, confusion matrix, and calibration—but do not substitute metrics for official requirements.
- Evaluate threshold and decision trade-offs when actions depend on scores.
- Review performance across meaningful groups or segments where appropriate and permitted.
- Record limitations, data issues, and model assumptions.

### Experiment tracking table

| Experiment ID | Model | Features/version | Split | Parameters | Metric(s) | Result | Notes |
|---|---|---|---|---|---|---|
| EXP-001 | Baseline | v1 | agreed split | documented | TBD | First benchmark |


## 10. Decision Policy Design

The policy is a separate layer between model output and the final action. It should be deterministic, documented, and testable unless the assignment explicitly requires another design.

### 10.1 Policy contract

Agree on the following before implementation:

- **Input:** validated request features and model output (exact schema to be finalized).
- **Output:** action/decision, required metadata, optional reason code, and policy version (exact schema to be finalized).
- **Rules:** named, ordered, and documented.
- **Thresholds:** configured and explained.
- **Fallback:** behavior for missing, invalid, or unavailable model output.
- **Versioning:** policy version or configuration revision included in output/logging if required.

### 10.2 Rule quality

- Avoid duplicated rule definitions in Python and UI.
- Prefer configuration for tunable thresholds when appropriate.
- Define precedence for rules that can overlap.
- Include tests for exact boundary values (below, equal to, above threshold).
- Make decisions reproducible from the same input and policy version.
- Do not imply a probability is a guarantee.


## 11. API and Application Integration

### 11.1 Integration contract

The model and service owners must agree on:
- Request schema and field names.
- Data types, allowed ranges, required/optional fields.
- Preprocessing and feature order (if applicable).
- Model artifact location and loading behavior.
- Prediction response schema.
- Policy input/output schema.
- Error behavior and error response format.
- Versioning and compatibility expectations.

### 11.2 Example flow

```text
Client / Dashboard
       |
       | request (validated fields)
       v
API / Predictor
       |
       +--> Feature validation & preprocessing
       |
       +--> Model inference
       |
       +--> Decision policy
       |
       v
Response: prediction + score (if applicable) + decision + metadata
```

The exact request and response fields must be derived from the assignment's `predict.py` contract and shared team implementation—not invented in this guide.

### 11.3 Service quality expectations

- Validate incoming payloads.
- Return clear errors for invalid input.
- Do not expose stack traces or secrets to clients.
- Avoid loading/retraining the model on every request.
- Keep inference deterministic when the model/policy are deterministic.
- Test request handling with normal, malformed, missing-field, and boundary inputs.
- Document local execution and Docker execution.


## 12. Repository Structure and File Ownership

The repository should stay organized and consistent. The starter structure may be adjusted to match official required paths, but keep a single source of truth for each component.

```text
CairoGo-ML-Decision-System/
├── README.md
├── requirements.txt
├── team.csv
├── team_analysis.ipynb
├── results.json
├── holdout_predictions.csv
├── segments.csv
├── decisions.csv
├── customer_history.csv                 # Include only if required/generated by specification
├── predict.py
├── policy.py
├── DECISION_RULES.json
├── Dockerfile
├── test_request.py
├── README_SERVICE.md
├── summary.json
├── dashboard.png
├── docs/
│   ├── team_workflow.md
│   ├── data_audit_report.md
│   ├── data_dictionary_notes.md
│   ├── model_report.md
│   ├── decision_policy.md
│   └── integration_contract.md
├── src/
│   ├── audit/
│   ├── features/
│   ├── models/
│   └── decision/
├── service/
├── dashboard/
├── tests/
├── artifacts/                            # Model/config artifacts as permitted
├── outputs/                               # Generated results; track only required files
└── data/
    ├── raw/                               # Do not commit restricted/private source data
    └── processed/                           # Do not commit restricted/private derived data
```

**Repository hygiene:** Never commit credentials, `.env` files, private datasets, restricted files, large unneeded artifacts, personal data, or local environment folders. Check the assignment's submission rules before tracking any generated data file. The required deliverables must be included in the final package if the official brief says so.


## 13. Git Collaboration Workflow

### 13.1 Branch strategy

- `main` — stable, integrated, reviewable work.
- Feature branches — one focused task per branch.
- Use pull requests (PRs) to merge into `main` when practical.

Example branch names:

- `feature/data-audit`
- `feature/model-training`
- `feature/segmentation`
- `feature/drift-policy`
- `feature/predictor-service`
- `feature/dashboard`

### 13.2 Daily workflow

```bash
# Start from the latest main
 git checkout main
 git pull origin main

# Create a branch for your task
 git checkout -b feature/your-task-name

# Make focused changes; test locally

# Stage and commit
 git add <files>
 git commit -m "Describe the change"

# Push your branch
 git push -u origin feature/your-task-name
```

Open a PR, explain what changed, list tests performed, identify any schema or dependency changes, and request review from the relevant teammate. Resolve conflicts deliberately; do not overwrite another teammate's work to make a merge pass.

### 13.3 Commit message examples

- `docs: add data audit methodology`
- `feat: implement baseline model pipeline`
- `feat: export segment assignments`
- `fix: validate missing request fields`
- `test: add policy threshold boundary cases`
- `refactor: centralize feature preprocessing`

### 13.4 Pull request checklist

- [ ] PR has one clear purpose.
- [ ] Code/notebook runs or limitations are explained.
- [ ] Tests pass (or the failure is documented).
- [ ] No data, credentials, or unrelated files are included.
- [ ] Required output schema is unchanged or the change is coordinated.
- [ ] Documentation is updated.
- [ ] Another team member has reviewed the change.


## 14. Notebook and Code Standards

- Keep notebooks organized with Markdown headings: objective, inputs, method, findings, limitations, next steps.
- Restart and run all cells before marking a notebook complete.
- Avoid long blocks of duplicated code across notebooks; move reusable logic into `src/`.
- Do not use a notebook as the only implementation of production/inference logic if a script/module is required.
- Use descriptive variable names and functions with one responsibility.
- Add comments to explain reasoning, edge cases, or non-obvious decisions—not every line.
- Set and record random seeds when applicable.
- Keep generated output small and relevant.
- Avoid committing notebook outputs that reveal private data or unnecessarily inflate the repository.
- Ensure the same transformations are used by training and inference.


## 15. Testing Strategy

### 15.1 Unit tests

Test isolated logic such as:
- Data validation and transformation functions.
- Feature preprocessing behavior.
- Policy rule evaluation and threshold boundaries.
- Segment/profile calculations.
- Predictor input validation.

### 15.2 Integration tests

Test component combinations:
- Predictor loads the selected model and preprocessing pipeline.
- Predictor output matches the expected schema.
- Policy accepts model output and returns a valid decision.
- API endpoint connects predictor and policy correctly.
- Dashboard sends a valid request and handles a valid response.

### 15.3 End-to-end smoke test

Run the complete local flow using a documented sample request:

1. Start the service/application.
2. Submit a valid request.
3. Confirm validation, prediction, policy, and response.
4. Submit an invalid request and confirm a controlled error.
5. Confirm the application can be restarted from documented instructions.

Use the assignment's required test files and official checker when available. Do not claim that tests passed unless they were actually run.


## 16. Documentation Deliverables

The repository should maintain concise, accurate documentation:

- **`README.md`** — project overview, setup, repository structure, team, how to run, outputs, and status.
- **`docs/data_audit_report.md`** — audit methodology, findings, evidence, severity, actions, and unresolved risks.
- **`docs/data_dictionary_notes.md`** — field meanings, types, units, availability timing, and assumptions.
- **`docs/model_report.md`** — target, split, features, model comparison, metrics, selection, limitations.
- **`docs/decision_policy.md`** — policy inputs/outputs, rules, thresholds, precedence, examples, limitations.
- **`docs/integration_contract.md`** — agreed request/response and module interfaces.
- **`README_SERVICE.md`** — service/application setup, execution, Docker, endpoints, test instructions.
- **Notebook Markdown** — purpose, method, results, and interpretation for each notebook.

Only add documentation files that are useful and keep each file current. Official required filenames take priority over the suggested organization.


## 17. Team Communication and Coordination

### 17.1 Working agreements

- Communicate blockers early; do not wait until integration day.
- Before changing shared schemas, file names, or interfaces, notify the owners.
- Keep tasks small enough to review.
- Use issues or a shared task board to record task, owner, status, and blocker.
- Record technical decisions in the repository (brief decision notes) rather than relying only on chat.
- Ask for a review before merging changes that affect shared interfaces.
- Do not commit directly to `main` unless the team explicitly agrees and the change is low-risk.

### 17.2 Status format

Each member can post updates in this format:

```text
Task:
Status: Not started / In progress / Blocked / In review / Done
Branch / PR:
Files changed:
Tests or checks:
Findings / decisions:
Blockers:
Next step:
```

### 17.3 Integration checkpoints

- **Checkpoint 1:** repository, environment, dataset inventory, task owners.
- **Checkpoint 2:** audit findings and shared feature/data contract.
- **Checkpoint 3:** baseline model and initial evaluation.
- **Checkpoint 4:** segment outputs and policy draft.
- **Checkpoint 5:** predictor/API contract and application skeleton.
- **Checkpoint 6:** integrated end-to-end test.
- **Checkpoint 7:** final documentation, packaging, and official checker.


## 18. Suggested Implementation Sequence

This is a dependency-aware plan; adjust dates and order to the team's schedule.

### Phase 0 — Kickoff and requirements

- Read the official assignment together.
- List mandatory files, formats, metrics, algorithms, constraints, and checker instructions.
- Assign owners and confirm the team roster/leader.
- Create repository and branches.
- Set up environment and agreed dependencies.
- Create task board and initial open-questions list.

**Exit criteria:** Everyone understands the scope; requirements are recorded; repository is accessible; owners are assigned.

### Phase 1 — Data inventory and audit

- Load supplied data safely.
- Inventory files, columns, types, keys, and row counts.
- Profile missingness, duplicates, ranges, and categories.
- Validate relationships and measure join loss.
- Investigate time/unit anomalies.
- Review leakage and confounding risks.
- Write findings with evidence and proposed actions.

**Exit criteria:** Audit report exists; critical data questions are resolved or explicitly recorded; modeling data contract is agreed.

### Phase 2 — Data preparation and baseline

- Agree target, prediction unit, and split.
- Create reproducible cleaning and preprocessing steps.
- Establish baseline model and baseline metrics.
- Verify feature availability and leakage controls.

**Exit criteria:** Baseline is reproducible and metrics are documented.

### Phase 3 — Model experiments and analysis

- Train required candidate models.
- Compare using agreed metrics and validation design.
- Inspect errors, calibration/thresholds if relevant, and subgroup behavior.
- Perform segmentation/local analysis.
- Conduct temporal/text analysis required by the brief.

**Exit criteria:** Candidate results are documented; selected model and supporting analyses are justified.

### Phase 4 — Decision policy

- Define decision rules, constraints, threshold logic, and fallback behavior.
- Implement policy independently from UI/API.
- Export required decision file(s).
- Test rule precedence and boundaries.

**Exit criteria:** Policy contract and rules are documented; tests pass; outputs conform to schema.

### Phase 5 — Service and dashboard

- Integrate model artifacts and preprocessing.
- Implement prediction contract and decision integration.
- Validate inputs and errors.
- Build app/dashboard.
- Add service/app tests.
- Containerize and document local run.

**Exit criteria:** A fresh environment can run the documented application and demonstrate the expected end-to-end flow.

### Phase 6 — Integration and hardening

- Merge workstreams.
- Run all notebooks/scripts from a clean state where practical.
- Validate output files and schemas.
- Run unit, integration, and smoke tests.
- Review security/privacy and repository hygiene.
- Fix integration issues and freeze interface changes.

**Exit criteria:** Integrated pipeline is stable; no known blocking issue remains; test evidence is recorded.

### Phase 7 — Final submission

- Confirm team file and member IDs are correct.
- Confirm leader is first if required by the official team schema.
- Confirm every required artifact exists and is at the correct path.
- Confirm ZIP structure (required files at root or specified paths; no unintended wrapper folder).
- Run the official checker using the exact documented command.
- Inspect the ZIP contents after creation.
- Confirm README, screenshots, summaries, and model/config files are present as required.
- Submit through the official method; retain a copy of the final package and checker output.

**Exit criteria:** The package matches the official specification and has been checked; only the designated team leader submits if the assignment says so.


## 19. Output and Artifact Contracts

Before implementation, create a shared table that records each deliverable's owner, schema, location, status, and validation method.

| Artifact | Owner | Contract / validation | Status |
|---|---|---|---|
| `team.csv` | Team Lead | Official roster schema; IDs correct; leader placement as specified | TBD |
| `team_analysis.ipynb` | Team / Audit Lead | Runs top-to-bottom; clearly labeled analyses | TBD |
| `results.json` | Audit/Analysis owners | Valid JSON; official schema; no missing required keys | TBD |
| `holdout_predictions.csv` | Model owner | Required columns, row count, valid prediction values | TBD |
| `segments.csv` | Segmentation owner | Required columns, valid segment labels | TBD |
| `decisions.csv` | Policy owner | Required schema; valid decisions | TBD |
| `predict.py` | Predictor owner | Reproducible inference; official input/output contract | TBD |
| `policy.py` | Policy owner | Deterministic policy; tests and contract | TBD |
| `DECISION_RULES.json` | Policy owner | Valid JSON; rules agree with implementation | TBD |
| `Dockerfile` | Service owner | Builds and runs using documented command | TBD |
| `requirements.txt` | Integration owner | Dependencies pinned/compatible as appropriate | TBD |
| `test_request.py` | Service owner | Required tests pass | TBD |
| `README.md` | Team Lead | Setup, project, outputs, team, instructions | TBD |
| `README_SERVICE.md` | Service owner | API/application and Docker instructions | TBD |
| `summary.json` | Integration owner | Valid JSON and official schema | TBD |
| `dashboard.png` | App owner | Current, readable screenshot of functioning app | TBD |

**Important:** This table is a project-management contract, not a substitute for the official submission schema. Validate every artifact against the assignment brief and any checker.


## 20. Risk Register

| Risk | Impact | Mitigation | Owner |
|---|---|---|---|
| Unclear official requirement | Wrong output or missed marks | Convert brief to checklist; verify ambiguous items early | Team Lead |
| Data leakage | Inflated evaluation; unreliable predictions | Feature availability review; split discipline; audit | Audit + Model |
| Join loss / key mismatch | Missing or duplicated records | Quantify joins; inspect keys; document repair | Audit |
| Unit or time shift | Incorrect feature values/model behavior | Period-based profiles; verify units; document changes | Audit / Drift |
| Inconsistent preprocessing | Training/inference mismatch | Shared pipeline; integration tests | Model + Service |
| Metric optimization without business context | Weak or unsuitable model | Agree metrics and decision objective | Model + Policy |
| Policy duplicated in UI | Inconsistent decisions | Single policy module/config | Policy + App |
| Interface changes late | Integration failure | Contract-first; PR review; freeze interfaces | Team Lead |
| Environment differences | Code fails on another machine | Requirements, clean setup, Docker, smoke test | Service owner |
| Unreviewed notebook-only logic | Difficult to reproduce or deploy | Move reusable logic to `src/`/modules | All owners |
| Missing required files | Submission rejection/lost marks | Automated checklist and checker | Team Lead |
| Data or secrets accidentally committed | Privacy/security incident | `.gitignore`, review staged files, no credentials | All owners |


## 21. Open Questions to Resolve at Kickoff

Use this list to avoid guessing assignment-specific requirements:

- [ ] What is the exact target and prediction unit?
- [ ] What are the official train/holdout files and allowed columns?
- [ ] Which metrics and thresholds are mandatory?
- [ ] Which algorithms or analysis techniques are mandatory versus optional?
- [ ] What is the exact expected schema for each output file?
- [ ] Are there restrictions on external libraries or data?
- [ ] What are the required predictor input and output fields?
- [ ] What are the exact decision rules/constraints?
- [ ] What is the official folder layout and ZIP naming convention?
- [ ] What command should run the official submission checker?
- [ ] Which member is responsible for each artifact and who reviews it?
- [ ] What is the submission deadline and internal freeze date?

Keep answers in a requirements checklist or decision log and update this guide after the team verifies them.


## 22. Final Quality Checklist

### Requirements
- [ ] Official assignment brief reviewed and converted into a checklist.
- [ ] Required file names, schemas, methods, and checker verified.
- [ ] All open questions resolved or explicitly documented.

### Data and ML
- [ ] Data audit completed and findings documented.
- [ ] Join quality checked.
- [ ] Shift/anomaly investigation completed as applicable.
- [ ] Leakage risks reviewed and addressed.
- [ ] Target and split documented.
- [ ] Preprocessing reproducible.
- [ ] Baseline and candidate models evaluated.
- [ ] Metrics and model selection rationale documented.
- [ ] Segment/local analyses completed as required.
- [ ] Policy implemented and tested.

### Engineering
- [ ] Repository is organized and clean.
- [ ] Shared interfaces are documented.
- [ ] Code and notebooks reviewed.
- [ ] Unit/integration/smoke tests run and evidence recorded.
- [ ] Service/app starts using documented commands.
- [ ] Docker build/run checked if required.
- [ ] No secrets or restricted data committed.

### Submission
- [ ] Every mandatory artifact exists.
- [ ] File schemas and formats validated.
- [ ] Team roster correct.
- [ ] ZIP name and structure correct.
- [ ] Official checker run and result recorded.
- [ ] Final ZIP inspected.
- [ ] Submission completed by the authorized member.



## 23. How to Use This Guide

1. Read this document once as a team.
2. Review the official assignment brief and mark every requirement as **Confirmed**, **Not applicable**, or **Open**.
3. Assign owners and reviewers for all workstreams and deliverables.
4. Use the implementation phases as the execution sequence.
5. Update the status tables and decision log as work progresses.
6. Keep the guide aligned with the actual repository and code. If a requirement changes, update the guide and communicate the change.
7. Before submission, use the final checklist and official checker—not this guide alone—to verify compliance.


