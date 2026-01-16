# Technical Notes — SC05
**Uncertainty-Aware Chemistry Design Maps**  
**Audit-Level Methodology, Diagnostics, and Design Decisions**

---

## 1. Scope and Role of This Document

These technical notes document the analytical rationale, methodological choices, and audit-level uncertainty treatment supporting **SC05 — Uncertainty-Aware Chemistry Design Maps**.

They complement:
- the **public README**, which focuses on decision framing and the final artifacts,
- and the **notebooks**, which contain full analytical execution and figures.

This document concentrates on:
- how uncertainty margins were constructed from out-of-fold behavior,
- what coverage means operationally in this context,
- and what guarantees are and are not being claimed.

---

## 2. Analytical Objective

The analytical objective of SC05 is to translate a validated chemistry-only UTS model into **decision-ready design artifacts** that support conservative engineering decisions.

This includes:
- producing human-readable chemistry design maps,
- defining a robust (“go/no-go”) region relative to a target UTS,
- and making model uncertainty explicit via a conservative margin.

### Explicit Non-Objectives
This analysis does **not** aim to:
- provide release-grade decisions,
- produce locally adaptive uncertainty estimates,
- or claim formal distribution-free finite-sample guarantees beyond what is empirically supported under the chosen protocol.

---

## 3. Data Contracts and Grain Definition

### 3.1 Analytical Unit (Grain)

- **Grain:** one row per heat
- **Justification:**
  - Chemistry and tensile outcomes are indexed at heat level.
  - The decision tools (maps) are intended to support standards defined at the **alloy and temper level**, evaluated using heat-level evidence.
  - Prevents leakage from repeated measurements of the same heat.

---

### 3.2 Dataset Construction

- **Source:** SQL semantic layer defined in SC01.
- **System:** AA3105-O.
- **Features:** Mg, Mn (chemistry-only baseline).
- **Target:** UTS (MPa).
- **Aggregation:** heat-level.

No additional process variables are included by design (consistent with SC04 findings).

---

## 4. Validation and Leakage Control

### 4.1 Validation Strategy

- **Method:** Group-aware cross-validation (GroupKFold).
- **Grouping variable:** heat identifier.
- **Rationale:**
  - prevents leakage from repeated measurements within heats,
  - ensures that residuals used for uncertainty are derived from unseen heats (OOF discipline).

All uncertainty calibration in this study is derived from **out-of-fold predictions**, not in-sample residuals.

---

## 5. Uncertainty Treatment: Definition, Scope, and Guarantees

### 5.1 Margin Definition (“Conformal-style global”)

Uncertainty is represented using a **single global margin** derived from the empirical distribution of **out-of-fold absolute residuals**:

$$
q = \mathrm{Quantile}_{1-\alpha}\left(|y - \hat{y}_{\mathrm{OOF}}|\right)
$$

A conservative lower bound surface is then defined as:

$$
\hat{y}_{\text{lower}}(x) = \hat{y}(x) - q
$$

This approach is “conformal-style” in the sense that the margin is calibrated from empirical residual behavior, but the documentation intentionally avoids claiming stronger guarantees than those supported by the implemented protocol.

---

### 5.2 Why a Single Global Quantile and what is traded off?

This choice is **deliberate** and motivated by engineering usability:

**Benefits**
- stable and smooth map geometry (robust boundaries),
- simple and communicable safety margin,
- reduced sensitivity to local data density,
- practical adoption for engineering teams with limited appetite for complex uncertainty machinery.

**Trade-offs**
- no locally adaptive uncertainty (heteroscedasticity is not modeled),
- no asymmetric bounds (risk is treated symmetrically in absolute residual space),
- no claim of exact finite-sample coverage beyond empirical validation under the chosen protocol.

This design aligns with the intended use: **early-stage conservative screening**, not precise probabilistic forecasting.

---

### 5.3 Selecting $\alpha$: Engineering Trade-off

The nominal coverage level (e.g., **90%**, $\alpha=0.10$) is chosen as an engineering trade-off:
- higher coverage increases conservatism but can shrink usable design regions,
- lower coverage increases usability but weakens safety margin.

The selected value represents a balance between:
- operational usability,
- and risk reduction for downstream failures.

---

### 5.4 Empirical Coverage Validation (OOF-based)

Coverage is assessed empirically on out-of-fold predictions:

$$
\widehat{cov} = \mathbb{E}\left[\mathbb{1}\{|y - \hat{y}_{\mathrm{OOF}}| \le q\}\right]
$$

The intent is to validate conservative behavior in practice without relying on parametric assumptions.

---

### 5.5 Deferred Alternatives (and why)

More sophisticated variants were considered but intentionally deferred to preserve interpretability and map stability, including:
- split conformal / CV+ style constructions,
- locally adaptive (heteroscedastic) bounds,
- asymmetric bounds when the cost of under- vs over-prediction differs.

Deferral is not a limitation of capability; it is a deliberate scope decision aligned with engineering adoption.

---

## 6. Map Construction: Domain, Grid, and Guardrails

### 6.1 Supported Domain

Maps are generated within a **data-supported chemistry window** (e.g., p05–p95 for Mg and Mn) to:
- minimize extrapolation,
- preserve defensibility,
- and reduce boundary artifacts from sparsely populated regions.

Predictions outside this domain are treated as unsupported.

---

### 6.2 Decision Artifact Definition (Robust Region)

Given a target UTS threshold \(T\), the robust region is defined as:

$$
\mathrm{Robust}(x) = \mathbb{1}\left[\hat{y}_{\text{lower}}(x) \ge T\right]
$$

This produces a human-readable go/no-go region that:
- encodes uncertainty explicitly,
- avoids “false precision,”
- and supports conservative decision-making.

---

### 6.3 Operational Guardrails (Misuse Prevention)

The maps must **not** be used when:
- evaluating chemistry outside the supported domain,
- applying to different alloy/temper without revalidation,
- significant process changes occurred (casting, rolling, annealing practices, equipment),
- decisions are release-grade or substitute for mechanical testing.

The tool is intended for **screening and standard definition support**, not final certification.

---

## 7. Model Choice for Map Geometry

Although flexible models may improve raw metrics, ridge regression is selected as the map basis because it yields:
- smooth, stable response surfaces,
- robust boundary geometry,
- and higher interpretability for cross-functional engineering use.

This mirrors the portfolio-wide principle:
**complexity must earn its place**, and geometry stability is a first-class requirement for standards.

---

## 8. Known Limitations and Failure Modes

- global margins may under/over-cover in specific chemistry pockets,
- map validity depends on process stability consistent with historical data,
- standards derived from model outputs require periodic revalidation as operations evolve.

Drift in upstream processes may invalidate **model outputs** over time, requiring revalidation of derived standards.

---

## 9. Decision Implications

This uncertainty-aware translation enables:
- conservative chemistry window definition,
- early-stage screening for robust UTS attainment,
- a common decision language for engineering and quality teams.

It does not replace metallurgical judgment, mechanical testing, or release criteria.

---

## 10. Design Decisions Log

Key non-obvious decisions recorded for traceability:

- Used OOF residuals to calibrate uncertainty, avoiding in-sample bias.
- Chose a single global quantile margin to preserve map stability and usability.
- Prioritized a robust-region decision artifact (lower-bound ≥ target) over point predictions.
- Selected ridge as the map basis to maximize geometric stability and communicability.
- Restricted the domain to data-supported chemistry ranges to prevent extrapolation misuse.

---

## 11. Traceability

- **Notebook:** `sc05_uncertainty_aware_design_maps.ipynb`
- **Data semantics:** SQL semantic layer defined in SC01
- **Commit:** `<>`

---

### Closing Note

These technical notes document how uncertainty was made explicit in a conservative, empirically validated way to translate predictive modeling into practical decision support. The goal is not maximum sophistication, but defensible, stable guidance that engineering teams can adopt.
