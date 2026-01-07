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

## Scope and Constraints

### In scope
- Primary focus on **AA3105**, ensuring continuity with previous study cases
- Chemistry-only feature set with frozen model structure
- Explicit uncertainty quantification layered on top of the predictive model
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
Uncertainty is introduced as a post-modeling layer using a distribution-free approach (e.g. conformal prediction), enabling:
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
