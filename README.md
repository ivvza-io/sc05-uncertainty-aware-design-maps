# SC05 — Uncertainty-Aware Chemistry Design Maps

> **Predictive models become decision tools only when uncertainty is made explicit. This study translates a validated chemistry-only UTS model into conservative, human-readable design maps that support robust engineering decisions.**

---

## 1. Why This Study Case Exists

Previous study cases established that:
- Chemistry contains sufficient signal to define **internal UTS standards** (SC02),
- The chemistry-based framework generalizes across systems, though not as a fixed formula (SC03),
- Adding process variables does not necessarily improve predictive robustness (SC04).

At this point, a new challenge emerges:

> **How can a validated predictive model be transformed into a tool that engineers can actually use to make conservative, defensible decisions?**

Regression coefficients, equations, and abstract metrics are rarely suitable for **day-to-day engineering work**, particularly when uncertainty must be considered explicitly.

This study case exists to **translate a chemistry-only model into decision-ready design artifacts**, without sacrificing rigor or interpretability.

---

## 2. Problem Framing and Hypothesis

### Strategic Problem

Point predictions alone are insufficient for engineering decisions.

In practice:
- Models are uncertain
- Errors are asymmetric in their consequences
- Decisions must be robust, not optimal on average

The strategic question addressed here is therefore:

> **Can a chemistry-only UTS model be expressed as a conservative design space that explicitly accounts for uncertainty and supports clear go / no-go decisions?**

### Hypothesis

> *By combining a stable chemistry-only model with an explicit uncertainty margin, it is possible to construct design maps that support conservative and interpretable engineering decisions.*

---

## 3. Design Philosophy and Principles

### 3.1 Models Must Be Translated, Not Exposed  
While regression models provide explicit mathematical formulations, these representations:
- Become unintuitive in multivariate settings
- Lose physical meaning after scaling or normalization
- Are poorly suited for collaborative engineering decisions

Design maps act as a **translation layer** between mathematical models and human reasoning.

### 3.2 Conservative Decisions Require Explicit Uncertainty  
Ignoring model uncertainty leads to false precision.

In this study, uncertainty is treated as:
- Explicit
- Quantified
- Central to decision-making

### 3.3 Robustness Over Nominal Optimality  
Design choices prioritize:
- Stability,
- Interpretability,
- Margin,

over marginal gains in nominal predictive accuracy.

---

## 4. Methodological Decisions (What We Chose — and Why)

This analysis uses the same analytical dataset as SC02 and SC04 (AA3105-O, 2,294 heats). For detailed domain description, see SC02.

- **System:** AA3105-O
- **Features:** Mg, Mn (chemistry-only, frozen baseline)
- **Model:** Ridge regression
- **Validation:** Group-aware cross-validation by heat
- **Uncertainty estimation:** Global margin derived from out-of-fold residuals

The uncertainty margin is defined using a simple, conservative rule derived from historical out-of-fold errors:


Uncertainty is estimated using a **conformal-style global margin**:

$$
q = \mathrm{Quantile}_{1-\alpha}\left(|y - \hat{y}_{\mathrm{OOF}}|\right)
$$

This defines a conservative lower bound:

$$
\hat{y}_{\text{lower}} = \hat{y} - q
$$

The margin is intentionally global to preserve stability and interpretability of the resulting design maps.

> Portfolio-wide assumptions and conventions are documented in  
> → [`README_EXTENDED.md`](https://github.com/ivvza-io/analytics-engineering-portfolio/blob/main/docs/README_EXTENDED.md)  
> Design rationale and technical details: [`docs/technical_notes.md`](docs/technical_notes.md)  
>  
> This study case uses the [`portfolio-analytics-toolkit`](https://github.com/ivvza-io/portfolio-analytics-toolkit) for reusable CV, metrics, and plotting utilities.  
>  
> Reproducibility instructions: [`HOW_TO_RUN.md`](HOW_TO_RUN.md)
---

## 5. Key Results and Design Artifacts

**Global uncertainty margin:** q ≈ 7.5 MPa (90% quantile of out-of-fold absolute residuals)

Among the generated artifacts, the **robust design region** constitutes the primary decision tool.

All other maps are supporting surfaces intended to provide context, diagnostics, or justification.

---

### 5.1 Robust Design Region (Primary Result)

**Figure 1 — Robust Chemistry Window for Target UTS = 120 MPa**

![binary map: lower ≥ target](assets/sc5_robust_region.png)

This map answers: **For a given target UTS, which chemistry compositions are defensible 
under uncertainty?**

> → The chemistry map enables direct go/no-go decisions on composition selection.

---

### 5.2 Uncertainty Validation

**Figure 2 — Out-of-Fold Prediction Band (Margin Validation)**

![y vs ŷ with uncertainty band](assets/sc5_prediction_band.png)

This plot validates that the selected margin q ≈ 7.5 MPa covers ~90% of unseen heats, 
confirming the conservative approach.

> → Empirical evidence: approximately 90% of OOF residuals fall within [ŷ - q, ŷ + q].

---

### 5.3 Model Justification

**Figure 3 — Ridge vs Polynomial: Lower-Bound Comparison**

![side by side lower bound maps](assets/sc5_lower_comparison_ridge_poly.png)

Ridge regression is selected for superior boundary stability and interpretability, 
despite slightly higher error margin (MAE 3.6 vs 3.5 MPa).

> → Smoother map geometry supports engineering adoption and reduces interpretation ambiguity.

---

## 6. Engineering Interpretation

These design maps support **early- and mid-stage engineering decisions**, including:
- Defining conservative chemistry windows
- Screening heats prior to execution
- Supporting cross-functional discussions between metallurgy and process teams

Key observations:
- Mg dominates the chemistry-driven envelope
- Mn provides secondary modulation
- Robust regions encode uncertainty-aware decision rules

---
## 7. How to Read and Use the Design Maps

The design maps are intended as **decision-support tools**, not as exact predictors.

To use the maps correctly:

- **Identify the chemistry point** (Mg, Mn) corresponding to the heat or design under evaluation
- **Select the appropriate target UTS level** based on internal requirements
- **Check whether the point lies inside the robust region**, where the conservative lower bound exceeds the target

Interpretation rules:
- Points **inside** the robust region satisfy the target with margin under the selected uncertainty level
- Points **outside** the robust region may still meet the target on average, but without conservative guarantees
- Boundaries should be interpreted as **transition zones**, not sharp physical limits

The maps are designed to support early-stage screening and conservative decision-making, not to replace detailed engineering analysis.

---

## 8. Operational Guardrails: When to Use and When NOT to Use

**Use this tool for:**
- Early-stage engineering screening
- Chemistry window definition
- Cross-functional discussions (metallurgy + process teams)

**Do NOT use for:**
- Extrapolation outside p05–p95 chemistry window
- Non-comparable alloys or tempers
- Release-grade decisions (mechanical testing required)
- Process conditions significantly different from 2023 historical baselines

**Key limitation:** Global uncertainty margin may be conservative in some regions, less so in others.  
For detailed trade-offs, see Technical Notes.

---

## 9. What This Study Case Is — and Is Not

### This Study *Is*:
- A translation of predictive modeling into a **decision-ready artifact**
- A conservative, uncertainty-aware framework
- A **bridge** between **data science** and **engineering practice**

### This Study *Is Not* :
- A release-grade quality decision system
- A local or heteroscedastic uncertainty model
- A replacement for detailed process modeling

---

## 10. Why This Study Case Matters in the Portfolio

This study case completes the portfolio arc:

1. **SC02:** Chemistry contains sufficient signal for UTS prediction
2. **SC03:** This signal generalizes across alloy systems as a framework
3. **SC04:** Additional variables do not improve robustness beyond chemistry alone
4. **SC05:** Standards become operationally useful only when uncertainty is explicit

**SC05 translates validated analytics into tools that engineering teams can use daily—without requiring deep statistical training—enabling confident, defensible decisions at the point of composition selection.**

---

## 11. Conclusion

Ridge regression with global conformal-style uncertainty margins provides a defensible, interpretable foundation for conservative chemistry design maps. 

This framework** translates validated predictive signals into operational decision tools** suitable for engineering adoption, completing the portfolio's standardization narrative from data foundation (SC01) through decision deployment (SC05).

---

## References and Related Work

- Portfolio assumptions and conventions:  
  → [`README_EXTENDED.md`](https://github.com/ivvza-io/analytics-engineering-portfolio/blob/main/docs/README_EXTENDED.md)
- Chemistry-only standard definition:  
  → [`SC02 — Chemistry-Only Modeling of UTS`](https://github.com/ivvza-io/sc02-chemistry-only-mechanical-properties)
- Chemistry generalization across systems:  
  → [`SC03 — Chemistry Generalization Across Systems`](https://github.com/ivvza-io/sc03-chemistry-generalization-across-systems)
- Variable influence screening:  
  → [`SC04 — Variable Influence Screening`](https://github.com/ivvza-io/sc04-variable-influence-screening)

---

### Takeaway

This study shows that **explicit uncertainty is what turns models into decisions**.  
The goal is not perfect coverage, but **defensible, conservative engineering guidance**.
For day-to-day engineering decisions, **models** are most effective when **expressed as human-readable design maps**, not as raw equations or coefficients.
