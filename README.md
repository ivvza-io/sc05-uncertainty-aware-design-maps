# sc05-uncertainty-aware-design-maps

**Translating validated chemistry-only models into robust, decision-ready design tools**

---

## Position in the Portfolio

This study case builds directly on the conclusions of **Study Cases 2, 3, and 4**, which progressively evaluated the role of chemistry-only modeling as a predictive and engineering support approach.

The narrative to this point can be summarized as follows:

- **Study Case 2** established that chemistry-only models provide meaningful predictive signal for UTS and that simple, interpretable models (ridge and polynomial regression) capture this relationship effectively.
- **Study Case 3** demonstrated that this approach generalizes across alloy systems, while highlighting that the chemistry–property relationship is system-dependent in shape rather than existence.
- **Study Case 4** evaluated whether adding process proxies (thickness, reduction, coiling temperature) justifies increased model complexity. Under a conservative screening lens, no variable demonstrated robust, repeatable gains over chemistry-only modeling for AA3105.

Together, these results support a clear conclusion:

> **For AA3105, chemistry-only modeling represents the most stable and defensible foundation for downstream engineering tools.**

Study Case 5 shifts the focus accordingly, from predictive optimization to **design usability**.

---

## Core Question

**How can a validated chemistry-only model be transformed into a conservative, uncertainty-aware design tool suitable for engineering decision-making?**

---

## Thesis

Even in the absence of process variables, chemistry-only models can support robust design decisions when uncertainty is explicitly quantified and translated into interpretable visual representations.

The objective is not to maximize point accuracy, but to:
- expose uncertainty,
- define conservative design bounds,
- and identify composition regions that reliably meet performance targets.

---

### Why design maps - From model equations to human-centered design tools

While predictive models can be expressed as explicit mathematical equations, multivariate and standardized formulations quickly become impractical as decision interfaces for engineering use.

In academic settings, this representation is usually sufficient. A linear regression with one variable can be easily interpreted as “the equation of a line”, and its behavior can be reasoned about analytically.

Design maps translate the learned chemistry–UTS relationship into a human-readable representation that supports inverse reasoning: starting from a required UTS target and identifying chemistry regions that satisfy it under conservative uncertainty bounds.

If a model is intended to support **design decisions**, not just prediction, it must be translated into a representation that is compatible with human reasoning, not algebraic manipulation.

In this study, models are not presented as equations to be interpreted, but as constraints to be navigated visually.

---
### Conservative chemistry design map (AA3105)

The figure below shows the conservative lower-bound prediction surface derived from a chemistry-only ridge model, calibrated using out-of-fold conformal residuals.

![Conservative chemistry design map (AA3105)](assets/sc5_design_map.png)

Rather than representing expected performance, the map encodes **guaranteed behavior**: each point reflects a lower bound on UTS that holds with the specified coverage level.

This representation enables inverse reasoning, starting from a required UTS target and identifying chemistry regions that satisfy it under uncertainty, making it directly usable as a design and release support tool.


### Robust region - target-based design guidance

While the conservative design map provides a continuous view of guaranteed performance,
engineering decisions are often binary in nature:

> *Is this chemistry safe or not for a given requirement?*

To support this decision, the conservative lower-bound surface is thresholded against a target UTS requirement.
The resulting **robust region map** identifies chemistry combinations that satisfy the target **under uncertainty**, not just on average.

In this representation:
- **Robust regions** guarantee the required UTS with the specified coverage level.
- **Non-robust regions** may meet the target on average, but cannot be relied upon under conservative assumptions.

![Robust region](assets/sc5_robust_region.png)

This binary view transforms the predictive model into a **release- and design-ready tool**,
allowing engineers to define internal chemistry windows that balance performance and risk without physical trial campaigns.


### Uncertainty layer - from prediction error to conservative bounds

Uncertainty is introduced as a post-modeling layer, derived exclusively from **out-of-fold residuals**
under group-aware cross-validation.

![Conformal prediction band around point predictions (OOF)](assets/sc5_prediction_band.png)

Rather than assuming a parametric error distribution or relying on in-sample confidence intervals,
a **distribution-free, conformal-style approach** is used to quantify empirical prediction error.
This ensures that uncertainty estimates reflect realistic, unseen-heats behavior.

The resulting prediction bands define:
- a **conservative lower bound**, used for design and release decisions,
- an upper bound for completeness,
- and an explicit trade-off between coverage and interval width.

Importantly, this uncertainty layer does not modify the underlying predictive model.
It acts as a transparent calibration step, translating empirical model error into
**interpretable safety margins** suitable for engineering decision-making.


---

## Scope and Constraints

### In scope
- Primary focus on **AA3105**, ensuring continuity with previous study cases
- Chemistry-only feature set with frozen model structure
- Explicit uncertainty quantification layered on top of the predictive model (using a distribution-free, conformal-style approach)
- 2D design maps as the primary decision interface
- Limited use of 3D visualizations when they add clarity
- Qualitative comparison between ridge and polynomial models at the map level

### Out of scope (explicit)
- Process-aware modeling
- Feature screening or expansion
- Hyperparameter optimization
- Causal interpretation of model coefficients
- Production deployment considerations

The focus is **decision support**, not predictive optimization.

---

## Model Choice for Design Maps

### Primary model: Ridge regression

Ridge regression is selected as the primary model for design map construction because it produces:
- smooth and stable response surfaces,
- well-behaved boundaries,
- and predictable extrapolation behavior.

These properties are critical when models are used as **visual design tools** rather than purely predictive engines.

### Reference comparison: Polynomial regression

Polynomial regression exhibits similar predictive performance to ridge regression across systems, but may introduce localized curvature and boundary behavior that complicates visual interpretation.

A limited visual comparison is included to highlight:
- differences in surface smoothness,
- boundary behavior,
- and interpretability.

### Excluded model: Random Forest

Although Random Forest models may achieve strong predictive metrics, they are intentionally excluded from design map construction.

The resulting surfaces tend to be:
- discontinuous,
- difficult to interpret visually,
- and less suitable for direct engineering decision-making.

This study prioritizes **interpretability and usability** over raw metric performance.

---

## Method Overview

### Chemistry-only baseline
- Model structure inherited from previous study cases
- Group-aware validation strategy
- Out-of-fold predictions used to ensure conservative assessment

### Uncertainty layer
Uncertainty is introduced as a post-modeling layer using a distribution-free approach (e.g. conformal prediction), derived exclusively from out-of-fold residuals, enabling:
- conservative lower bounds,
- interpretable coverage guarantees,
- and uncertainty estimation without altering the underlying model.

### Design map construction
Design maps are generated in chemistry space (e.g. two dominant chemical dimensions), including:
- Mean prediction surfaces
- Conservative lower-bound maps
- Uncertainty width maps
- Robust regions satisfying a specified performance target

---

## Expected Outputs

### Visual tools
- 2D chemistry design maps (mean prediction, lower bound, uncertainty width)
- Binary “robust region” maps identifying conservative design space
- Selected 3D surfaces for exploratory visualization

### Engineering artifacts
- Coverage vs interval width trade-off analysis
- Target-based design guidance (e.g. guaranteed UTS thresholds)
- Clear identification of regions suitable for conservative design decisions

---

## Evaluation Criteria

This study is considered successful if it demonstrates that:

- Chemistry-only, uncertainty-aware models can support conservative engineering decisions
- Design maps are interpretable and actionable
- Robust regions can be clearly identified relative to engineering targets
- Visual representations remain stable and human-readable

Predictive accuracy improvements are **not** success criteria.

---

## Relationship to Other Systems

While AA3105 is the primary focus, selected results from commercially pure aluminum systems (e.g. 1xxx series) may be shown to illustrate the general applicability of the design map approach, without duplicating the full analysis.

---

## Relationship to Future Work

Study Case 5 establishes chemistry-only, uncertainty-aware design maps as a baseline engineering tool.

Potential future extensions include:
- Applying the same framework to yield strength and elongation
- Introducing additional process context where justified
- Comparing design map robustness before and after adding process-aware variables

---

## Closing Perspective

The progression from Study Case 2 through Study Case 5 reflects a deliberate shift:

> **From asking whether a model can predict, to asking whether it can support decisions.**

Study Case 5 formalizes this shift by translating validated predictive structure into tools that engineers can directly use for conservative design.
