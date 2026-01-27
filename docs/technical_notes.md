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

$$\widehat{cov} = \mathbb{E}\left[\mathbb{1}\{|y - \hat{y}_{\mathrm{OOF}}| \le q\}\right]$$

**Observed coverage:** Approximately 90% of out-of-fold heats fall within the [ŷ_lower, ŷ_upper] band, validating that the selected α=0.10 margin achieves intended coverage on unseen data.

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

Maps are generated within the **5th–95th percentile window** (p05–p95) for Mg and Mn based on the analytical dataset. This choice:

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

While the uncertainty-aware approach is robust and empirically validated, several limitations must be understood for responsible use:

### Global Margin Trade-Off

The constant margin (q ≈ 7.5 MPa) simplifies interpretation and ensures smooth map geometry, but creates trade-offs across the chemistry space:

- **Over-coverage in high-Mg regions:** Where residuals are naturally small (e.g., Mg > 0.50, where historical |residuals| ≤ 5 MPa), the 7.5 MPa margin is unnecessarily conservative. Robust regions may be smaller than required.

- **Less conservative in low-Mg regions:** Where scatter is higher (e.g., Mg < 0.35, where some |residuals| > 8 MPa), the margin provides relatively less protection than in high-Mg regions.

This heteroscedasticity is documented but intentionally not modeled locally (see Section 5.5 "Deferred Alternatives"). The global choice is conservative overall; it is a deliberate design trade-off to maintain interpretability and operational usability.

### Process Drift

Model validity depends on continuation of historical process conditions. If annealing temperatures, rolling schedules, or other process parameters shift significantly, the chemistry–UTS relationship may change, invalidating predictions.

**Example:** If equipment recalibration increases annealing temperature by 50°C, new heats might show systematically higher or lower UTS than predicted, exceeding the 7.5 MPa margin.

**Detection:** Monitor prediction residuals for new production. If P95 absolute error exceeds 10 MPa (vs. 7.5 MPa in OOF), revalidation is recommended.

### Limited Scope (Chemistry Only)

These maps deliberately exclude process variables (see SC04 findings). Engineering teams must recognize:
- Chemistry defines the **potential** UTS under good conditions
- Process execution (rolling, cooling, etc.) determines **actual** outcome
- Within a chemistry window, process control remains critical

### Temporal Validity

Standards are derived from historical data. Periodic revalidation is recommended:

- **Quarterly:** Routine performance check against new production data
- **After major changes:** Equipment overhaul, supplier changes, specification revisions
- **Upon drift detection:** If new predictions consistently miss by >±10 MPa

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

- **Notebook:** sc05_uncertainty_aware_design_maps.ipynb
- **Data source:** v_models_analysis_o_temper (same as SC02, SC04)
- **Validation:** GroupKFold (n_splits=5, heat-level grouping)
- **Toolkit:** [version pinned in requirements.txt]
- **Reproducibility:** HOW_TO_RUN.md
---


## APENDIX A. Revalidation Protocol and Maintenance

### Intended Use Lifecycle

1. **Initial validation:** OOF model performance on historical data (Section 4)
2. **Deployment:** Maps released for engineering use
3. **Monitoring:** Track prediction accuracy on new production
4. **Revalidation:** Every 3 months or upon process change
5. **Update:** Retrain model if drift is detected

### Drift Detection Criteria

Revalidation is triggered if:
- **Residual growth:** Median |residuals| for new heats > 9 MPa (vs. historical p75 = 7.5 MPa)
- **Coverage loss:** <85% of new heats fall within predicted ±q band
- **Systematic bias:** New heats show consistent over/under prediction (>±5 MPa)
- **Distribution shift:** UTS distribution moves >10 MPa from historical median (120 MPa)

### Revalidation Actions

When drift is detected:

1. **Pull new data:** 100+ recent heats with comparable conditions
2. **Diagnostic:** Retrain model on combined historical + new data
3. **Compare:** Has model degradation occurred, or are conditions genuinely different?
4. **Decide:**
   - **No degradation:** Update coefficients, revise maps
   - **Degradation detected:** Investigate root cause (composition, process, equipment)
   - **Conditions changed:** Revalidate from scratch on new operating regime

### Governance and Ownership

- **Quarterly review:** Analytics team reviews prediction accuracy
- **Alert threshold:** Automatically triggered if drift criteria exceeded
- **Update authority:** [Define team/person responsible for model updates and standard revisions]
- **Documentation:** Changes logged with date, reason, and new model details

---
### Closing Note

These technical notes document how uncertainty was made explicit in a conservative, empirically validated way to translate predictive modeling into practical decision support. The goal is not maximum sophistication, but defensible, stable guidance that engineering teams can adopt.
