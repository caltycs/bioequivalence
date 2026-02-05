# Detailed Statistical Model for Bioequivalence Testing
## Mathematical Foundations and Worked Examples

---

# 1. Why Standard Hypothesis Testing Fails for Equivalence

## 1.1 The Fundamental Problem

In traditional hypothesis testing:

```
H₀: μT = μR    (no difference)
H₁: μT ≠ μR    (there is a difference)
```

**Problem**: Failing to reject H₀ does NOT prove equivalence. It only means we lack evidence of a difference. A poorly designed study with low power will almost always "fail to find a difference."

### Example of the Flaw:

| Study | Sample Size | Observed Difference | p-value | Conclusion |
|-------|-------------|---------------------|---------|------------|
| A | n = 6 | 15% | 0.32 | "No significant difference" |
| B | n = 200 | 3% | 0.04 | "Significant difference" |

Study A concludes "equivalence" despite a 15% difference simply because it was underpowered. This is dangerous for drug approval.

## 1.2 The Solution: Reverse the Hypotheses

In equivalence testing, we **assume products are different** and require evidence to prove similarity:

```
H₀: |μT - μR| ≥ Δ    (products differ by at least Δ)
H₁: |μT - μR| < Δ    (products are within Δ of each other)
```

Now, rejecting H₀ provides **positive evidence** of equivalence.

---

# 2. The Two One-Sided Tests (TOST) Procedure

## 2.1 Mathematical Formulation

The TOST procedure decomposes the equivalence hypothesis into two one-sided tests:

### Original Equivalence Hypothesis:
```
H₀: μT/μR ≤ θL  OR  μT/μR ≥ θU
H₁: θL < μT/μR < θU
```

Where:
- θL = 0.80 (lower equivalence bound)
- θU = 1.25 (upper equivalence bound)
- Note: 1.25 = 1/0.80, making bounds symmetric on log scale

### Decomposed into Two Tests:

**Test 1 (Lower Bound):**
```
H₀₁: μT/μR ≤ 0.80
H₁₁: μT/μR > 0.80
```

**Test 2 (Upper Bound):**
```
H₀₂: μT/μR ≥ 1.25
H₁₂: μT/μR < 1.25
```

### Decision Rule:
```
IF (reject H₀₁ at α = 0.05) AND (reject H₀₂ at α = 0.05)
THEN conclude bioequivalence
ELSE fail to establish bioequivalence
```

## 2.2 Why Log Transformation?

### Reason 1: Pharmacokinetic Data is Log-Normal

```
                Raw Data (Skewed)              Log-Transformed (Normal)

Frequency│                                 Frequency│
         │    ██                                    │      ████
         │   ████                                   │    ████████
         │  ██████                                  │   ██████████
         │ ████████                                 │  ████████████
         │██████████▄▄                              │ ██████████████
         └──────────────→ Cmax                      └──────────────→ ln(Cmax)
```

### Reason 2: Multiplicative Effects Become Additive

On the original scale, we want:
```
0.80 ≤ μT/μR ≤ 1.25
```

Taking natural log:
```
ln(0.80) ≤ ln(μT) - ln(μR) ≤ ln(1.25)
-0.2231 ≤ ln(μT) - ln(μR) ≤ 0.2231
```

This creates symmetric bounds on the log scale.

### Reason 3: Geometric Mean is Appropriate

For log-normal data, the geometric mean (GM) is a better measure of central tendency:

```
Geometric Mean = exp(1/n × Σ ln(Xᵢ))
```

The ratio of geometric means equals:
```
GMR = GM_T / GM_R = exp(mean[ln(X_T)] - mean[ln(X_R)])
```

---

# 3. The Linear Mixed Effects Model

## 3.1 Model Specification

For a 2×2 crossover design, the statistical model is:

```
Yᵢⱼₖ = μ + SEQᵢ + SUBⱼ₍ᵢ₎ + PERₖ + TRTₗ + εᵢⱼₖ
```

### Full Mathematical Notation:

```
ln(Yᵢⱼₖₗ) = μ + γᵢ + Sⱼ₍ᵢ₎ + πₖ + τₗ + εᵢⱼₖₗ
```

Where:
| Symbol | Description | Type | Constraint |
|--------|-------------|------|------------|
| ln(Yᵢⱼₖₗ) | Log-transformed response (AUC or Cmax) | Dependent variable | - |
| μ | Overall population mean | Fixed | - |
| γᵢ | Effect of sequence i (i = 1,2) | Fixed | Σγᵢ = 0 |
| Sⱼ₍ᵢ₎ | Effect of subject j nested in sequence i | Random | Sⱼ₍ᵢ₎ ~ N(0, σ²ᵦ) |
| πₖ | Effect of period k (k = 1,2) | Fixed | Σπₖ = 0 |
| τₗ | Effect of treatment l (l = T,R) | Fixed | ΣTₗ = 0 |
| εᵢⱼₖₗ | Random error | Random | εᵢⱼₖₗ ~ N(0, σ²ᵥ) |

## 3.2 Design Matrix Representation

For subject j in sequence RT (Reference first, then Test):

| Period | Treatment | Observation | Model Terms |
|--------|-----------|-------------|-------------|
| 1 | R | Y₁ⱼ₁ᴿ | μ + γ₁ + Sⱼ₍₁₎ + π₁ + τᴿ + ε₁ⱼ₁ᴿ |
| 2 | T | Y₁ⱼ₂ᵀ | μ + γ₁ + Sⱼ₍₁₎ + π₂ + τᵀ + ε₁ⱼ₂ᵀ |

For subject j in sequence TR (Test first, then Reference):

| Period | Treatment | Observation | Model Terms |
|--------|-----------|-------------|-------------|
| 1 | T | Y₂ⱼ₁ᵀ | μ + γ₂ + Sⱼ₍₂₎ + π₁ + τᵀ + ε₂ⱼ₁ᵀ |
| 2 | R | Y₂ⱼ₂ᴿ | μ + γ₂ + Sⱼ₍₂₎ + π₂ + τᴿ + ε₂ⱼ₂ᴿ |

## 3.3 Matrix Form

```
Y = Xβ + Zu + ε
```

Where:
- **Y** = n×1 vector of log-transformed observations
- **X** = n×p design matrix for fixed effects
- **β** = p×1 vector of fixed effect parameters
- **Z** = n×q design matrix for random effects
- **u** = q×1 vector of random subject effects
- **ε** = n×1 vector of residual errors

### Variance-Covariance Structure:

```
Var(u) = G = σ²ᵦI_q

Var(ε) = R = σ²ᵥI_n

Var(Y) = V = ZGZ' + R
```

## 3.4 Why Include Each Term?

| Effect | Purpose | What Happens if Omitted |
|--------|---------|------------------------|
| **Sequence** | Controls for baseline differences between sequence groups | Confounding with treatment effect |
| **Subject(Sequence)** | Accounts for inter-subject variability | Inflated error variance, reduced power |
| **Period** | Controls for learning effects, seasonal variation | Period-treatment confounding |
| **Treatment** | The effect of interest (Test vs Reference) | Cannot estimate bioequivalence |

---

# 4. ANOVA Decomposition

## 4.1 Sums of Squares

For a balanced 2×2 crossover with n subjects per sequence:

### Total Sum of Squares:
```
SS_Total = Σᵢ Σⱼ Σₖ (Yᵢⱼₖ - Ȳ...)²
```

### Decomposition:
```
SS_Total = SS_Sequence + SS_Subject(Seq) + SS_Period + SS_Treatment + SS_Residual
```

## 4.2 ANOVA Table

| Source | df | Sum of Squares | Mean Square | Expected Mean Square | F-ratio |
|--------|-----|----------------|-------------|---------------------|---------|
| Sequence | 1 | SS_SEQ | MS_SEQ | σ²ᵥ + 2σ²ᵦ + 2n·Σγᵢ²/(s-1) | MS_SEQ/MS_SUB(SEQ) |
| Subject(Seq) | 2n-2 | SS_SUB | MS_SUB | σ²ᵥ + 2σ²ᵦ | - |
| Period | 1 | SS_PER | MS_PER | σ²ᵥ + 2n·Σπₖ²/(p-1) | MS_PER/MS_RES |
| Treatment | 1 | SS_TRT | MS_TRT | σ²ᵥ + 2n·Στₗ²/(t-1) | MS_TRT/MS_RES |
| Residual | 2n-2 | SS_RES | MS_RES | σ²ᵥ | - |
| **Total** | **4n-1** | **SS_TOT** | - | - | - |

Where:
- s = number of sequences (2)
- p = number of periods (2)
- t = number of treatments (2)
- n = subjects per sequence

## 4.3 Degrees of Freedom Calculation

For the ibuprofen study (n=12 per sequence, N=24 total):

| Source | df Formula | df Value |
|--------|------------|----------|
| Sequence | s - 1 | 2 - 1 = 1 |
| Subject(Seq) | s(n - 1) | 2(12 - 1) = 22 |
| Period | p - 1 | 2 - 1 = 1 |
| Treatment | t - 1 | 2 - 1 = 1 |
| Residual | (s-1)(n-1) or (N-s)(p-1)-1 | 22 |
| **Total** | N×p - 1 | 24×2 - 1 = 47 |

---

# 5. Point Estimate: Geometric Mean Ratio

## 5.1 Least Squares Means

The least squares mean (LSMean) for each treatment is the model-predicted mean adjusted for other effects:

```
LSMean_T = μ̂ + τ̂_T
LSMean_R = μ̂ + τ̂_R
```

### Calculation from Crossover Data:

For sequence RT: d₁ⱼ = Y₁ⱼ₂ᵀ - Y₁ⱼ₁ᴿ (Test - Reference)
For sequence TR: d₂ⱼ = Y₂ⱼ₁ᵀ - Y₂ⱼ₂ᴿ (Test - Reference)

```
Treatment difference = (d̄₁ + d̄₂) / 2
```

Where d̄₁ and d̄₂ are the mean intra-subject differences for each sequence.

## 5.2 Geometric Mean Ratio

```
Δ̂ = LSMean_T - LSMean_R    (on log scale)

GMR = exp(Δ̂)               (back-transformed)
```

### Example Calculation:

From the ibuprofen study for Cmax:
```
LSMean_T (ln scale) = 2.308
LSMean_R (ln scale) = 2.295

Δ̂ = 2.308 - 2.295 = 0.013

GMR = exp(0.013) = 1.013 = 101.3%
```

---

# 6. Confidence Interval Construction

## 6.1 Standard Error of the Difference

The standard error of the treatment difference depends on the design:

### For 2×2 Crossover:
```
SE(Δ̂) = √(2 × MSE / n)
```

Where:
- MSE = Mean Square Error from ANOVA (residual variance)
- n = number of subjects per sequence

### For Unbalanced Design:
```
SE(Δ̂) = √(MSE × (1/n₁ + 1/n₂) / 2)
```

## 6.2 The 90% Confidence Interval

### Formula:
```
90% CI = exp[Δ̂ ± t₍₁₋α, df₎ × SE(Δ̂)]
```

Where:
- α = 0.05 (for 90% CI)
- df = degrees of freedom for residual
- t = critical value from t-distribution

### Step-by-Step Calculation:

**Given (from ibuprofen study):**
- Δ̂ = 0.013 (log scale difference for Cmax)
- MSE = 0.0289 (from ANOVA)
- n = 12 subjects per sequence
- df = 22

**Step 1: Calculate SE**
```
SE = √(2 × 0.0289 / 12) = √0.00482 = 0.0694
```

**Step 2: Get t-critical value**
```
t(0.95, 22) = 1.717    (one-sided)
or equivalently
t(0.90, 22) for two-sided = 1.717
```

**Step 3: Calculate CI on log scale**
```
Lower (log) = 0.013 - 1.717 × 0.0694 = 0.013 - 0.119 = -0.106
Upper (log) = 0.013 + 1.717 × 0.0694 = 0.013 + 0.119 = 0.132
```

**Step 4: Back-transform**
```
Lower = exp(-0.106) = 0.899 = 89.9%
Upper = exp(0.132) = 1.141 = 114.1%

90% CI = [89.9%, 114.1%]
```

**Step 5: Compare to acceptance limits**
```
Is 89.9% ≥ 80%?  ✓ YES
Is 114.1% ≤ 125%?  ✓ YES

Conclusion: Bioequivalence ESTABLISHED
```

---

# 7. TOST Test Statistics

## 7.1 Test Statistic Formulas

**For Lower Bound Test (H₀₁: μT/μR ≤ 0.80):**
```
t_L = (Δ̂ - ln(0.80)) / SE(Δ̂)
    = (Δ̂ - (-0.2231)) / SE(Δ̂)
    = (Δ̂ + 0.2231) / SE(Δ̂)
```

**For Upper Bound Test (H₀₂: μT/μR ≥ 1.25):**
```
t_U = (Δ̂ - ln(1.25)) / SE(Δ̂)
    = (Δ̂ - 0.2231) / SE(Δ̂)
```

## 7.2 Example Calculation

Using values from above (Δ̂ = 0.013, SE = 0.0694):

**Lower Bound Test:**
```
t_L = (0.013 + 0.2231) / 0.0694
    = 0.2361 / 0.0694
    = 3.40

p_L = P(t₂₂ > 3.40) = 0.0013    ✓ Reject H₀₁
```

**Upper Bound Test:**
```
t_U = (0.013 - 0.2231) / 0.0694
    = -0.2101 / 0.0694
    = -3.03

p_U = P(t₂₂ < -3.03) = 0.0031    ✓ Reject H₀₂
```

**Conclusion:**
Both p-values < 0.05, therefore bioequivalence is established.

## 7.3 Equivalence of CI and TOST Approaches

The 90% CI approach and TOST at α=0.05 give **identical conclusions**:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   90% CI fully within [80%, 125%]                          │
│                    ⟺                                        │
│   Both TOST p-values < 0.05                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

This is because:
- The lower CI bound > 80% ⟺ t_L > t_critical ⟺ p_L < 0.05
- The upper CI bound < 125% ⟺ t_U < -t_critical ⟺ p_U < 0.05

---

# 8. Variance Components and Power

## 8.1 Variance Decomposition

Total variability in crossover studies:

```
σ²_Total = σ²_Between-Subject + σ²_Within-Subject
         = σ²_B + σ²_W
```

The crossover design eliminates between-subject variability from the treatment comparison:

```
Var(Δ̂) = 2σ²_W / n
```

Not:
```
Var(Δ̂) ≠ 2(σ²_B + σ²_W) / n    (parallel design)
```

This is why crossover designs are more efficient.

## 8.2 Coefficient of Variation

The within-subject CV is estimated from MSE:

```
CV_W = √(exp(MSE) - 1) × 100%
```

For the ibuprofen study:
```
CV_W = √(exp(0.0289) - 1) × 100%
     = √(1.0293 - 1) × 100%
     = √0.0293 × 100%
     = 17.1%
```

## 8.3 Sample Size and Power

### Power Formula:

```
Power = Φ(−z_{1-α} + δ/SE) + Φ(−z_{1-α} − δ/SE) − 1
```

Where:
- δ = distance from GMR to nearest boundary
- SE = standard error of log(GMR)

### Sample Size Formula (approximate):

```
n ≥ 2 × [(z_{1-α} + z_{1-β})² × CV_W²] / [ln(θU) - |ln(GMR)|]²
```

### Example:
For 80% power, assuming GMR = 0.95, CV_W = 20%:

```
n ≥ 2 × [(1.645 + 0.842)² × 0.20²] / [0.2231 - 0.0513]²
  ≥ 2 × [6.19 × 0.04] / [0.0296]
  ≥ 2 × 0.248 / 0.0296
  ≥ 16.8

Therefore n ≥ 17 subjects per sequence (34 total)
```

---

# 9. Worked Example with Complete Data

## 9.1 Input Data (Simplified for 8 Subjects)

| Subject | Sequence | Period | Treatment | ln(Cmax) |
|---------|----------|--------|-----------|----------|
| 1 | RT | 1 | R | 2.31 |
| 1 | RT | 2 | T | 2.35 |
| 2 | RT | 1 | R | 2.18 |
| 2 | RT | 2 | T | 2.22 |
| 3 | RT | 1 | R | 2.42 |
| 3 | RT | 2 | T | 2.38 |
| 4 | RT | 1 | R | 2.28 |
| 4 | RT | 2 | T | 2.30 |
| 5 | TR | 1 | T | 2.35 |
| 5 | TR | 2 | R | 2.32 |
| 6 | TR | 1 | T | 2.25 |
| 6 | TR | 2 | R | 2.21 |
| 7 | TR | 1 | T | 2.40 |
| 7 | TR | 2 | R | 2.44 |
| 8 | TR | 1 | T | 2.29 |
| 8 | TR | 2 | R | 2.27 |

## 9.2 Calculate Intra-Subject Differences

**Sequence RT (T - R):**
| Subject | d = ln(Cmax_T) - ln(Cmax_R) |
|---------|------------------------------|
| 1 | 2.35 - 2.31 = 0.04 |
| 2 | 2.22 - 2.18 = 0.04 |
| 3 | 2.38 - 2.42 = -0.04 |
| 4 | 2.30 - 2.28 = 0.02 |
| **Mean d̄₁** | **0.015** |

**Sequence TR (T - R):**
| Subject | d = ln(Cmax_T) - ln(Cmax_R) |
|---------|------------------------------|
| 5 | 2.35 - 2.32 = 0.03 |
| 6 | 2.25 - 2.21 = 0.04 |
| 7 | 2.40 - 2.44 = -0.04 |
| 8 | 2.29 - 2.27 = 0.02 |
| **Mean d̄₂** | **0.0125** |

## 9.3 Point Estimate

```
Δ̂ = (d̄₁ + d̄₂) / 2 = (0.015 + 0.0125) / 2 = 0.01375

GMR = exp(0.01375) = 1.0138 = 101.4%
```

## 9.4 ANOVA Results

| Source | df | SS | MS | F | p-value |
|--------|-----|--------|--------|------|---------|
| Sequence | 1 | 0.0012 | 0.0012 | 0.15 | 0.71 |
| Subject(Seq) | 6 | 0.0482 | 0.0080 | - | - |
| Period | 1 | 0.0003 | 0.0003 | 0.23 | 0.65 |
| Treatment | 1 | 0.0008 | 0.0008 | 0.58 | 0.48 |
| Residual | 6 | 0.0081 | 0.00135 | - | - |
| **Total** | **15** | **0.0586** | - | - | - |

MSE = 0.00135

## 9.5 Standard Error and CI

```
SE = √(2 × MSE / n) = √(2 × 0.00135 / 4) = √0.000675 = 0.026

t(0.95, 6) = 1.943

Lower = exp(0.01375 - 1.943 × 0.026) = exp(-0.037) = 0.964 = 96.4%
Upper = exp(0.01375 + 1.943 × 0.026) = exp(0.064) = 1.066 = 106.6%

90% CI = [96.4%, 106.6%]
```

## 9.6 Conclusion

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   90% CI: [96.4%, 106.6%]                              │
│                                                         │
│   80% ←────[████ 96.4% ████ 106.6% ████]────→ 125%    │
│                       ↑                                 │
│                    101.4%                               │
│                                                         │
│   ✓ Entirely within [80%, 125%]                        │
│   ✓ BIOEQUIVALENCE ESTABLISHED                         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

# 10. Statistical Software Implementation

## 10.1 SAS Code

```sas
/* Bioequivalence Analysis - 2x2 Crossover */
proc mixed data=be_data;
   class sequence subject period treatment;
   model logCmax = sequence period treatment / ddfm=satterthwaite;
   random subject(sequence);
   estimate 'T vs R' treatment 1 -1 / cl alpha=0.10;
   lsmeans treatment / diff=all cl alpha=0.10;
run;
```

## 10.2 R Code

```r
# Using the BE package
library(BE)

# Read data
data <- read.csv("be_data.csv")

# Run bioequivalence analysis
result <- be2x2(
  data = data,
  var = "Cmax",          # or "AUClast", "AUCinf"
  subject = "SUBJ",
  treatment = "TRT",
  period = "PRD",
  sequence = "SEQ"
)

# View results
summary(result)
```

## 10.3 Python Code

```python
import numpy as np
import pandas as pd
from scipy import stats
import statsmodels.formula.api as smf

# Load data
df = pd.read_csv('be_data.csv')
df['logCmax'] = np.log(df['Cmax'])

# Fit mixed model
model = smf.mixedlm(
    "logCmax ~ C(sequence) + C(period) + C(treatment)",
    data=df,
    groups=df["subject"]
)
result = model.fit()

# Extract treatment effect
delta = result.params['C(treatment)[T.Test]']
se = result.bse['C(treatment)[T.Test]']

# Calculate 90% CI
df_resid = result.df_resid
t_crit = stats.t.ppf(0.95, df_resid)
ci_lower = np.exp(delta - t_crit * se)
ci_upper = np.exp(delta + t_crit * se)
gmr = np.exp(delta)

print(f"GMR: {gmr*100:.1f}%")
print(f"90% CI: [{ci_lower*100:.1f}%, {ci_upper*100:.1f}%]")
```

---

# 11. Special Cases and Extensions

## 11.1 Highly Variable Drugs (HVD)

For drugs with CV_W > 30%, regulators allow **Reference-Scaled Average Bioequivalence (RSABE)**:

```
Scaled Criterion: |ln(GMR)| ≤ θ × σ_WR

Where:
- σ_WR = within-subject SD of reference
- θ = regulatory scaling factor (typically 0.760)
```

The acceptance limits expand with variability, up to a maximum of 69.84% - 143.19%.

## 11.2 Narrow Therapeutic Index (NTI) Drugs

For drugs with narrow safety margins (e.g., warfarin, lithium):

```
Tightened limits: 90.00% - 111.11%
```

Additional requirement: Demonstrate similar within-subject variability.

## 11.3 Replicate Designs

For more precise estimates, replicate designs (2×4, 2×2×4) allow:
- Better estimation of within-subject variability
- Individual bioequivalence assessment
- Scaled average bioequivalence

---

# 12. Summary Table

| Component | Formula/Value | Purpose |
|-----------|---------------|---------|
| **Model** | ln(Y) = μ + Seq + Subj(Seq) + Per + Trt + ε | Partition variability |
| **Point Estimate** | GMR = exp(LSMean_T - LSMean_R) | Compare formulations |
| **Standard Error** | SE = √(2×MSE/n) | Quantify uncertainty |
| **90% CI** | exp[Δ̂ ± t×SE] | Test equivalence |
| **Acceptance Limits** | 80.00% - 125.00% | Regulatory threshold |
| **Decision Rule** | CI ⊂ [80%, 125%] | Conclude BE |

---

*This detailed statistical supplement provides the mathematical foundations for bioequivalence testing. For implementation, regulatory submissions require validation of all software and methods against reference datasets.*
