# Cox Proportional Hazards Model — Multiple Myeloma Survival

**Which clinical measurements at diagnosis predict survival in multiple myeloma, and does the Cox model's central assumption actually hold?**

R · `survival` · `rms` · Survival analysis coursework

---

## The question

Multiple myeloma patients present with a spread of laboratory abnormalities:
renal impairment, anaemia, hypercalcaemia, and Bence-Jones protein in urine.
Which of these carry prognostic information once the others are accounted for?

This analysis fits Cox proportional hazards models to a survival dataset of 48
patients, builds up from a two-covariate model to a five-covariate one, and then
tests whether the proportional hazards assumption underlying the whole approach
is defensible.

---

## Dataset

`multmyel` — survival times of patients with multiple myeloma.

- 48 patients, 36 deaths (12 censored)
- Survival time measured in months from diagnosis

| Variable | Meaning |
|---|---|
| `time` | Survival time from diagnosis (months) |
| `status` | Death indicator |
| `age` | Age at diagnosis |
| `sex` | Female / Male |
| `BUN` | Blood urea nitrogen (renal function) |
| `CA` | Serum calcium |
| `HB` | Haemoglobin |
| `PC` | Percentage of white blood cells |
| `BJP` | Bence-Jones protein present (Yes / No) |

---

## What I found

**Renal function and anaemia carry the prognostic signal. The rest do not.**

Full model, n = 48, 36 events:

| Covariate | HR | 95% CI | p |
|---|---|---|---|
| BUN (per unit) | 1.021 | 1.009 – 1.033 | 0.0006 |
| Haemoglobin (per unit) | 0.872 | 0.767 – 0.993 | 0.038 |
| Bence-Jones protein (absent vs present) | 1.81 | 0.80 – 4.07 | 0.15 |
| Sex (male vs female) | 1.24 | 0.59 – 2.59 | 0.57 |
| Age (per year) | 0.98 | 0.93 – 1.03 | 0.46 |

Likelihood ratio test 17.68 on 5 df, p = 0.003. Concordance 0.711.

Three things worth drawing out:

- **A small per-unit hazard ratio is not a small effect.** BUN has HR 1.021, which
  looks negligible until you note that BUN ranges widely across these patients. A
  10-unit increase gives HR 1.23, a 23 percent increase in instantaneous risk.
  Interpreting a coefficient without looking at the scale of its variable is a
  good way to dismiss a real effect.

- **Bence-Jones protein does not reach significance here.** It is the biomarker
  most specifically associated with myeloma, and the crude model gives HR 1.72
  for its absence, but the confidence interval spans 1 in both the crude and
  adjusted models. With 36 events this study is underpowered to settle the
  question; absence of evidence is not evidence of absence.

- **The sex-by-BJP interaction is not supported.** Adding `sex:BJP` gives an
  interaction HR of 1.12 (p = 0.88) and widens every confidence interval. It was
  dropped. Fitting an interaction and then reporting it as a finding regardless
  is a common way to manufacture a result.

**Concordance is 0.711**, meaning the model orders patient risk correctly about
71 percent of the time. Usefully better than chance, well short of clinically
decisive.

---

## Checking the assumption

A Cox model assumes hazard ratios are constant over time. If that fails, the
reported hazard ratios are averages over a changing effect and can be
substantially misleading.

Tested using:

- **Schoenfeld residuals**, which under proportional hazards should show no
  association with time for a given covariate
- **`cox.zph`**, the formal test of that association
- **`plot.cox.zph`**, plotting the scaled residuals against time to see the shape
  of any violation rather than only its p-value

Reporting a Cox model without this check is reporting an unvalidated model. The
diagnostics are included here for that reason.

---

## Also included

- Kaplan-Meier survival curves stratified by sex and by Bence-Jones protein, with numbers at risk
- Estimation of the baseline hazard function
- Model-based survival prediction for specified covariate profiles

---

## Repository contents

```
R/       Proportional hazards model.qmd   Analysis source
results/ Proportional hazards model.pdf   Rendered output
data/    Ahda_RLab3.RData                 Dataset
```

---

## Scope

This was a lab exercise for the Survival Analysis component of my MSc, not an
independent research project. The dataset and the analysis questions were
supplied by the course; the model building, interpretation and diagnostics are
my own work.

I am publishing it because the assumption-checking section is the part most
often skipped in applied survival analysis, and it is worth showing that I do
not skip it.

---

## Skills demonstrated

- Cox proportional hazards modelling
- Hazard ratio interpretation, including per-unit versus per-clinically-meaningful-increment
- Testing and interpreting interaction terms
- Proportional hazards assumption testing via Schoenfeld residuals and `cox.zph`
- Kaplan-Meier estimation and survival curve comparison
- Baseline hazard estimation and model-based prediction
- Handling right-censored time-to-event data
