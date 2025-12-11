# MSB/MIB Technical Appendix – v1.0  
**File:** `msb-mib_appendix_tech_v1.md`  
**Project:** msb-mib-gross-mobility  
**Author:** Juan Robles (Mexico)  
**Email:** mroblesg88@gmail.com  
**ORCID:** 0009-0006-0458-6186  
**Date:** December 9, 2025  
**License:** CC BY 4.0

---

## 1. Purpose and Scope

This technical appendix defines a **first, simple formalization** of:

- **MSB – Gross Substantive Mobility** (individual-level indicator),  
- **MIB – Gross Internal Mobility** (territorial-level indicator), and  
- a minimal **Universal Dividend** allocation rule using:

  - a **single transaction tax**,  
  - a **base national dividend**, and  
  - an optional **territorial Top-Up** based on MIB.

The goal is **not** to present a fully calibrated econometric model, but to provide:

- clear notation,  
- baseline formulas, and  
- pseudocode that can be implemented, tested, and criticized.

---

## 2. Notation and Data Model

### 2.1. Sets and indices

- Individuals:  
  \( i = 1, 2, \dots, N \)
- Territories (regions, states, municipalities, etc.):  
  \( r \in \mathcal{R} \)
- Time periods (e.g. month, year):  
  \( t \in \mathcal{T} \)
- For each individual \( i \) in period \( t \):  
  \( E_{i,t} \) = set of events (transactions/activities).

### 2.2. Event-level variables

Each event \( e \in E_{i,t} \) is characterized by:

- \( value_e \):  
  Economic or normalized value of the event.  
  (Example: monetary amount, or standardized “importance score”)
- \( type_e \in \mathcal{K} \):  
  Categorical type, e.g. `work`, `health`, `education`, `leisure`, `care`, etc.
- \( w_{type_e} \in \mathbb{R}^+ \):  
  Weight associated with the event type.
- \( satisfaction_e \in [0, 1] \):  
  Satisfaction score associated with the event.
- \( timestamp_e \):  
  Time of occurrence (used for frequency/windows, not directly in the core formula here).

### 2.3. Period-level normalization

To keep MSB within a manageable range, we define:

- \( Z_t > 0 \):  
  Normalization factor for period \( t \) (e.g. 95th percentile of raw sums).

---

## 3. Gross Substantive Mobility (MSB)

### 3.1. Basic MSB definition

For individual \( i \) in period \( t \):

\[
MSB_{i,t} = \frac{1}{Z_t} \sum_{e \in E_{i,t}} w_{type_e} \cdot value_e \cdot satisfaction_e
\]

Where:

- If \(E_{i,t}\) is empty, we set \( MSB_{i,t} = 0 \).
- \( Z_t \) normalizes the distribution across the population, so that most values stay in a practical range (e.g. \( [0, 1] \) or \( [0, 100] \)).

### 3.2. Interpretation

- High **MSB** indicates a life with:
  - multiple or significant activities,
  - in meaningful domains (weights \( w_{type_e} \)),
  - with **high satisfaction**.
- Low **MSB** may indicate:
  - very few activities, or
  - many activities that are stressful, forced, or low satisfaction.

### 3.3. Suggested normalization strategy

A simple strategy for defining \( Z_t \):

1. For each individual \( i \), compute the **raw sum**:
   \[
   raw\_MSB_{i,t} = \sum_{e \in E_{i,t}} w_{type_e} \cdot value_e \cdot satisfaction_e
   \]
2. Take \( Z_t \) as the empirical **95th percentile** of \( raw\_MSB_{i,t} \) across all individuals.

Then:

\[
MSB_{i,t} = \frac{raw\_MSB_{i,t}}{Z_t}
\]

This keeps the indicator relatively stable and robust to outliers.

---

## 4. Decomposition by Activity Type (Optional)

In some applications, it is useful to decompose MSB by type \( k \in \mathcal{K} \).

### 4.1. Type-specific MSB

For each category \( k \):

\[
MSB_{i,t}^{(k)} = \frac{1}{Z_t^{(k)}} \sum_{e \in E_{i,t}: type_e = k} value_e \cdot satisfaction_e
\]

Where:

- \( Z_t^{(k)} \) is a type-specific normalization factor (e.g. 95th percentile of the raw sums for that type).

### 4.2. Aggregated MSB with policy weights

We can recombine type-specific MSBs:

\[
MSB_{i,t} = \sum_{k \in \mathcal{K}} \alpha_k \cdot MSB_{i,t}^{(k)}
\]

Where:

- \(\alpha_k \ge 0\),  
- \(\sum_k \alpha_k = 1\),  
- and \(\alpha_k\) can reflect policy priorities (e.g. giving more weight to health or education events).

---

## 5. Gross Internal Mobility (MIB)

### 5.1. Territorial aggregation

Let \( I_r \subseteq \{1, \dots, N\} \) be the set of individuals in territory \( r \).

The **Gross Internal Mobility** for territory \( r \) in period \( t \) is:

\[
MIB_{r,t} = \frac{1}{|I_r|} \sum_{i \in I_r} MSB_{i,t}
\]

If desired, one may use a **weighted average**, e.g. weighting individuals by demographic or socioeconomic factors.

### 5.2. National-level MIB

For the whole country (or macro-region):

\[
MIB_{country,t} = \frac{1}{N} \sum_{i=1}^N MSB_{i,t}
\]

---

## 6. Universal Dividend – Base Component

Let:

- \( R_t \): total transaction tax revenue collected in period \( t \).  
- \( \theta \in (0,1) \): fraction of total revenue allocated to the **base Universal Dividend**.  
- \( N \): total number of registered individuals.

### 6.1. Base national dividend per person

The **base Universal Dividend** in period \( t \) is:

\[
DU_t^{base} = \frac{\theta \cdot R_t}{N}
\]

Every individual \( i \) receives at least \( DU_t^{base} \) during period \( t \), independent of their MSB.

---

## 7. Territorial Top-Up Using MIB (Optional)

This section introduces an optional **Top-Up** that uses MIB to direct additional resources to territories with lower mobility.

Let:

- \( \phi \in (0,1) \): fraction of total revenue used for the Top-Up component.  
- \( R_t^{TopUp} = \phi \cdot R_t \): total Top-Up pool for period \( t \).  
- \( |I_r| \): number of individuals in territory \( r \).  
- \( MIB_{r,t} \): Gross Internal Mobility for territory \( r \) in period \( t \).  
- \( MIB_t^{target} \): benchmark or target value (e.g. national median or explicit policy target).

### 7.1. Need factor per territory

We define a **need factor** for each territory \( r \):

\[
need_{r,t} = \max(0, MIB_t^{target} - MIB_{r,t})
\]

- If \( MIB_{r,t} < MIB_t^{target} \), then \( need_{r,t} > 0 \).  
- If \( MIB_{r,t} \ge MIB_t^{target} \), then \( need_{r,t} = 0 \).

### 7.2. Normalized needs

Let:

\[
TotalNeed_t = \sum_{r \in \mathcal{R}} need_{r,t}
\]

If \( TotalNeed_t > 0 \):

\[
\tilde{need}_{r,t} = \frac{need_{r,t}}{TotalNeed_t}
\]

### 7.3. Allocation of Top-Up pool

The Top-Up pool for territory \( r \) is:

\[
Pool_{r,t}^{TopUp} = \tilde{need}_{r,t} \cdot R_t^{TopUp}
\]

The **Top-Up per person** in territory \( r \) is:

\[
TopUp_{r,t} = 
\begin{cases}
\frac{Pool_{r,t}^{TopUp}}{|I_r|}, & \text{if } |I_r| > 0 \\
0, & \text{if } |I_r| = 0
\end{cases}
\]

### 7.4. Final Universal Dividend per person

For an individual \( i \in I_r \):

\[
DU_{i,t} = DU_t^{base} + TopUp_{r,t}
\]

Note that:

- **MSB is not used** to reduce the dividend of any individual.  
- The Top-Up adjusts territorial allocation but preserves the base Universal Dividend for everyone.

---

## 8. Example Parameter Choices (Non-binding)

The following values are **illustrative only** and must be adapted to each country:

- Normalization:
  - \( Z_t \): 95th percentile of raw MSB sums.
- Activity weights \( w_{type_e} \):
  - `work`: 1.0  
  - `education`: 1.2  
  - `health_preventive`: 1.3  
  - `health_reactive`: 0.9  
  - `care`: 1.1  
  - `leisure`: 1.0  
- Policy weights \( \alpha_k \) (if using type-specific MSB):
  - Sum to 1, e.g. more weight on `education` and `health_preventive`.
- Dividend shares:
  - \( \theta = 0.30 \) (30% of revenue for base dividend).  
  - \( \phi = 0.10 \) (10% of revenue for MIB-based Top-Up).

These values are **not prescriptions**—they merely show the degrees of freedom policymakers have in calibrating the system.

---

## 9. Pseudocode (Python-like)

The following pseudocode uses Python-like syntax and assumes the existence of basic data structures.

### 9.1. MSB per individual

```python
def compute_msb_for_individual(events, type_weights, Z):
    """
    Compute MSB for one individual in a given period.

    events: list[dict] with keys:
        'value' (float),
        'type' (str),
        'satisfaction' (float in [0, 1])
    type_weights: dict[str, float] mapping type -> weight
    Z: normalization factor for the period (float > 0)
    """
    raw_sum = 0.0

    for e in events:
        v = e['value']
        t = e['type']
        s = e['satisfaction']  # assumed in [0, 1]
        w = type_weights.get(t, 1.0)
        raw_sum += w * v * s

    if Z <= 0:
        return 0.0

    return raw_sum / Z

---



## 10 Limitations and Ethical Considerations 

Limitations and Ethical Considerations (Minimal Outline)

Any implementation of MSB/MIB must address:

Privacy and data protection

Use of strong anonymization and aggregation.

Clear separation between individual-level rights and statistical uses.

Avoiding “social credit” misuse

MSB is not a score for punishing or rewarding individuals.

It should never be used to restrict access to fundamental rights.

Bias and fairness

Satisfaction signals (especially indirect/biometric) may reflect structural biases.

Continuous auditing and inclusive governance are required.

Transparency

Formulas, parameters and allocation rules must be public, inspectable and explainable.

These notes are intentionally brief. A full ethical framework would require more analysis.

Versioning

Version: 1.0

Status: Conceptual and exploratory.

Intended audience:

Researchers in digital economy, UBI, and wellbeing metrics.

Policymakers and technical teams interested in testing MSB/MIB in simulations or pilots.

Further versions may include:

empirically calibrated parameters,

simulation results,

empirical validation in specific countries or regions.