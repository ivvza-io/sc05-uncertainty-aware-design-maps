# How to Run This Study Case

This section describes how to reproduce the analysis locally.
It is intentionally separated from the study narrative and results.

The goal is to ensure technical reproducibility without interfering with the analytical and decision-focused structure of the case study.

---

## 1. Expected Repository Structure

The following structure is expected in order to reproduce the analysis:

```text
.
├── data/
│   └── public/
│       └── sc02/
│           └── v_models_analysis_o.csv
├── notebooks/
│   └── sc02_chemistry_only_uts_model.ipynb
├── requirements.txt
└── requirements-notebooks.txt
```

Public datasets are versioned under data/public/ to ensure reproducibility.
All paths are resolved relative to the project root.

Public datasets are intentionally limited to what is required to reproduce figures and metrics shown in the README.

---

## 2. Environment Setup

**Create a Virtual Environment**

From the repository root:

```bash
python -m venv .venv
source .venv/bin/activate
```
> Python ≥ 3.10 recommended
---

## 3. Install Dependencies

### Option A — Notebook reproduction (recommended)

Use this option to reproduce the full notebook analysis and regenerate figures.

```bash
pip install -r requirements-notebooks.txt
```

This installs:

- core scientific dependencies
- Jupyter execution support
- the shared portfolio toolkit (used to keep notebooks concise)

### Option B — Runtime-only (no notebooks)

Use this option if you only need the non-notebook runtime dependencies.

```bash
pip install -r requirements.txt
```
---

## 4. Run the Notebook

### Interactive (JupyterLab)

```bash
jupyter lab
```

### Interactive (Jupyter Notebook)

```bash
jupyter notebook
```

Then open:
- `notebooks/sc02_chemistry_only_uts_model.ipynb`

---

---

## 5. Verify Installation

**Check that the toolkit is installed correctly:**
```bash
python -c "from portfolio_analytics_toolkit.cv import build_oof_predictions; print('✓ Toolkit installed correctly')"
```

**Expected output:**
```
✓ Toolkit installed correctly
```

**If you see an error:**
- Verify you ran `pip install -r requirements-notebooks.txt`
- Check that you're using the correct virtual environment

---

## 6. Quick Checks

Confirm the public dataset is present:

```bash
ls -lh data/public/sc02/v_models_analysis_o.csv
```

If the file is missing, verify that you cloned the repository correctly and that the data/public/ directory is included in your checkout.