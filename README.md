# Replication Package

**Thesis:** *The Signal, The Noise, and The Receiver: How Textual Ambiguity and Institutional Credibility Moderate the Transmission of Monetary Policy*

**Author:** Clement Durix · **Supervisor:** Prof. Dr. Matthias Weber · **Institution:** University of St. Gallen (HSG), Finance Specialisation

---

## What this repository contains

A single self-contained Jupyter notebook, [`thesis_replication.ipynb`](thesis_replication.ipynb), that reproduces every regression coefficient, p-value, R², and assumption-check statistic reported in Sections 5–7 and Appendix A of the thesis. The notebook is organised section by section to mirror the thesis: each section begins with a markdown cell quoting the thesis table and its headline numbers, followed by the code that produces it and a verification print confirming the match.

## How to run

```bash
pip install -r requirements.txt
jupyter notebook thesis_replication.ipynb
# then: Kernel → Restart & Run All
```

Full end-to-end execution takes under 30 seconds on a laptop. No GPU, no API keys, no internet connection required.

## Notebook ↔ thesis map

| Thesis section | Notebook section | Headline result reproduced |
|---|---|---|
| § 5.3, Table 1 | Validation of the ambiguity classifier | κ = 0.618 at threshold 0.65 |
| § 6.1, Table 2 | Descriptive statistics | All means/std/quartiles for N = 97 + U_t on N = 77 |
| § 6.2, Table 3 | Model A: confusion channel (H1) | A4: β(U_t) = +0.234, p = 0.031 |
| § 6.3, Table 4 | Model B raw sentiment (H2) | B4: β(S × CredGap) = −0.181, p = 0.313 |
| § 6.3, Table 5 | Model B perceived sentiment (H2) | Daily: β(P × CredGap) = −0.228, p = 0.005 |
| § 6.4, Table 6 | Sample split on ambiguity (H3) | Low-ambig daily: β(P × CredGap) = −0.317, p = 0.015 |
| § 7.1, Table 7 | H1 robustness (10 specifications) | All 10 β positive |
| § 7.2, Table 8 | H2 robustness (6 specifications) | Survives NW + VIX control |
| § 7.3, Table 9 | H3 robustness (8 specifications) | Tercile bottom: β = −0.397, p = 0.020 |
| § 7.4 | Assumption checks | All VIFs < 1.8, Breusch-Pagan p > 0.25 |
| Appendix A.1–A.4 | Segment-level concentration | Bucket 14:00–14:15: β(U_t) = +0.286, p = 0.014 |

## Data

All inputs to the notebook live in [`data/processed/`](data/processed/):

| File | Rows | Purpose |
|---|---|---|
| `master_dataset.csv` | 97 | Meeting-level regression inputs (S_t, P_t, Vol_t, CredGap_t, VIX_t, …) |
| `master_dataset_pressconf.csv` | 77 | Press-conference subset, adds U_t |
| `annotation_sample_100.csv` | 100 | 100 hand-labelled Q&A sentences for the NLI validation |
| `zn_event_windows.csv` | 97 | Per-meeting volatilities at narrow/wide/extended windows and 15-min sub-buckets, plus daily price change |
| `appendix_statement_features.csv` | 77 | The nine statement-linguistic features + dissent count + Δword count used in Appendix A |

The last two CSVs are pre-computed derivatives. They are generated from the original 1-minute Treasury-futures parquet (≈ 33 MB; excluded here to keep the repo light) and from the raw statement text files by these scripts in the source-of-truth repository: `scripts/build_figure4_csv.py`, `scripts/run_appendix_statement_features.py`, and `scripts/run_appendix_common_factor.py`. Anyone who wishes to reproduce the feature extraction from raw data may contact the author for the parquet and the 97 statement text files (Databento and FRED API keys also required for re-fetching).

## Implementation notes

- All regressions use HC1 robust standard errors (`statsmodels` default `cov_type="HC1"`), except where the thesis explicitly reports Newey-West, in which case `cov_type="HAC"` with `maxlags=1` (intraday) or `maxlags=4` (daily) is used, the lag asymmetry matches the original analysis scripts.
- All continuous variables are standardized to mean 0, std 1 (`ddof=1`) **within the regression sample**. When a subsample is constructed (e.g. pre-2020, low-ambiguity), variables are re-standardized on that subsample.
- `CredGap_t` and `VIX_t` use the previous trading day's value to avoid endogeneity (Gürkaynak, Levin & Swanson 2010), per the methodological audit described in Section 4 of the thesis.

## Licence

Academic use, attribution requested.
