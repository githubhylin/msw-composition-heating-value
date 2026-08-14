[README.md](https://github.com/user-attachments/files/31070254/README.md)
# Analysis code for MSW physical-composition classification and heating-value estimation

Analysis code accompanying the manuscript:

> Lin, H.-Y. Physical composition classification and thermal representation of
> municipal solid waste: Implications for heating-value estimation from Taiwanese
> sampling data. *(under review)*

The repository contains a single annotated Jupyter/Colab notebook that reproduces
the complete analysis: data screening, classification-resolution evaluation,
zero-intercept Ridge estimation of empirical effective heating-value
coefficients, abundance-weighted contiguous regrouping, deterministic allocation
of low-prevalence components, comparison with literature-reported formula
architectures, and the temporal, bootstrap and sensitivity robustness suite.

Archived version: **DOI 10.5281/zenodo.XXXXXXX** *(replace after the first
Zenodo release; the concept DOI resolves to the latest version)*

---

## Contents

| Path | Description |
|---|---|
| `MSW_HHV_submissionF7_manuscript_sync.ipynb` | Complete analysis notebook, with all cell outputs retained from the authoritative run |
| `requirements.txt` | Package versions of the run that produced the reported results |
| `data/README.md` | Why the source dataset is not redistributed, and how to obtain it |
| `CITATION.cff` | Machine-readable citation metadata |
| `LICENSE` | MIT License (code) |

Cell outputs are deliberately **not** cleared. They are the executed record of
the run reported in the manuscript, and the notebook renders directly in the
GitHub web interface, so every result referenced from the manuscript can be
inspected without re-running anything. Execution counts are sequential (1–21),
confirming a single clean top-to-bottom run.

---

## Data availability

The source reports and sampling appendices were obtained from the public
project-report platform maintained by the Ministry of Environment, Taiwan. The
platform's copyright notice restricts unauthorized reproduction, modification
and redistribution of website content, so **neither the source reports nor the
harmonized row-level dataset can be redistributed here.** Report titles,
retrieval details and source-availability notes are given in Supplementary S1 of
the manuscript; the standard analytical methods (NIEA) for each measured
variable are listed in Supplementary Table S1.

Two safeguards enforce this in code:

- `EXPORT_RESTRICTED_INTERMEDIATES = False` (Section 0) is a hard switch that
  prevents row-level intermediate files, including the cleaned dataset, from
  ever being written.
- No notebook output reproduces row-level records. All retained outputs are
  aggregate statistics, coefficients, performance metrics or figures.

If the input file is absent, the notebook generates a **synthetic** dataset so
the code can be executed end to end. Synthetic runs verify that the pipeline
executes; they cannot reproduce the numerical values reported in the manuscript.

---

## Running the notebook

### On Google Colab

Open the notebook in Colab, then uncomment and edit the configuration block at
the top of Section 0:

```python
import os
from google.colab import drive; drive.mount("/content/drive")
os.environ["MSW_DATA_PATH"]  = "/content/drive/MyDrive/<your folder>/<your file>.csv"
os.environ["MSW_OUTPUT_DIR"] = "/content/drive/MyDrive/<your folder>/outputs"
```

Then use **Runtime → Restart and run all**. The cells must be executed in order;
later sections depend on objects defined in Sections 0–2.

### Locally

```bash
pip install -r requirements.txt
export MSW_DATA_PATH=./data/msw_composition.csv
export MSW_OUTPUT_DIR=./outputs
jupyter lab MSW_HHV_submissionF7_manuscript_sync.ipynb
```

If the two environment variables are unset, the notebook falls back to
`./data/msw_composition.csv` and `./outputs_wseg`.

The paths visible in the stored outputs are those of the authoring environment.
They are retained as the record of the reported run and are not required by any
other user; both locations are configurable through the environment variables
above.

### Column mapping

Section 1b maps the notebook's internal variable names to the column names of
the input CSV. A dataset with different column headings can be analysed by
editing that mapping only — no other cell needs to change. Required variables
are the dry-basis proportions of the 25 composition components, moisture
content, carbon content, dry-basis higher heating value, sampling date and the
county/city identifier.

---

## Fixed analysis parameters

All parameters are declared as named constants in Section 0:

| Parameter | Value | Role |
|---|---|---|
| `RANDOM_STATE` | 42 | All random operations, including the train/test split |
| `ALPHA_MAIN` | 500 | Ridge regularization parameter (Methods §2.3) |
| `TEST_SIZE` | 0.20 | Held-out test fraction (471 / 118 samples) |
| `N_SPLITS` / `N_REPEATS` | 5 / 20 | Repeated cross-validation design |
| `K_RANGE` | 2–7 | Candidate operational group counts |
| `HHV_C_THRESHOLD` | 140.0 | HHV/carbon consistency screening (594 → 589) |
| `L_THRESHOLD` / `M_THRESHOLD` | 5.0 / 1.5 | Prevalence labels (descriptive only) |
| `MIN_GROUP_SHARE` | 1.5 | Minimum dry-basis group share constraint |
| `MAIN_K_TOLERANCE` | 0.01 | Practical-equivalence tolerance for selecting K |
| `FINAL_K` | 4 | Selected operational group count |
| `N_BOOTSTRAP` | 500 | Bootstrap resamples for co-clustering |

Held-out test results are computed only after the analytical rules are fixed and
never participate in resolution selection, allocation or the choice of K.

---

## Notebook section map

The manuscript's Supplementary Material cross-references this notebook using
bracketed tags such as `[Notebook §7b]`. Those tags refer to the section numbers
below.

| Notebook section | Analysis | Manuscript output |
|---|---|---|
| §0 | Environment, imports, fixed parameters | Methods §2.3 |
| §1 | Data loading, column standardisation, HHV/C screening, fixed train/test split | Methods §2.1–2.2 |
| §2 | Shared function library: Ridge CV, weighted segmentation, deterministic allocation, evaluation, DBH→HHV conversion | Methods §2.3–2.5 |
| §3.0 | Component abundance, training-set prevalence labels, top-*N* resolution | Figure 1 |
| §3.1 | Ridge models at L, LM and LMS resolutions | Supports Figures 1–2 |
| §3.2 | Repeated-CV effective coefficient distributions | Figure 2 |
| §3.3 | Within-conventional-major-category heterogeneity | Table 1 |
| §3.4 | Abundance-weighted contiguous segmentation, K = 2–7 | Table 2; Figure 3 |
| §3.4c | Complete operational allocation and K selection | Table S4 |
| §3.5 | Transfer and recalibration of literature-reported formulas A1–A5 | Table 3; Tables S3, S10–S11 |
| §5a | Fixed-structure leave-one-year-out validation | Table S5; Figure S1 |
| §5b | Strict final-year re-derivation, 2014–2018 → 2019 | Table S6 |
| §6a | HHV/carbon exclusion-threshold sensitivity | Table S7 |
| §6b | Ridge alpha sensitivity | Table S8; Figure S2 |
| §6c | Non-negative coefficient sensitivity | Supplementary S4 (text) |
| §6d | Bootstrap co-clustering of the complete operational procedure | Table S9; Figure S3 |
| §6e | Literature-architecture estimator and coefficient sensitivity | Tables S10–S11 |
| §7 | Reproducibility record: software versions and fixed parameters | Supplementary S5 |
| §7b | Publication output export with final table and figure numbering | Table S2; all main and supplementary tables/figures |
| §P | Publication consistency audit | Supplementary S5 |

The notebook has no Section 4; the numbering was kept stable across revisions so
that the manuscript cross-references remain valid.

### Consistency audit

Section §P asserts the dataset dimensions (589 / 471 / 118), the final
operational membership, the main performance metrics, the temporal analyses and
the alpha selection, and confirms that no superseded output files remain. Any
inconsistency raises an `AssertionError`, so a clean run of §P is evidence that
the exported tables and figures match the values reported in the manuscript.

---

## Outputs

Section §7b writes each manuscript table as CSV and XLSX, with a separate
footnote file where the manuscript carries one, and exports Figures 1–3 and
S1–S3 as PDF, PNG and SVG at 1000 dpi with editable text. Publication and
exploratory outputs are written to separate directories under
`MSW_OUTPUT_DIR`. Generated outputs are not committed to this repository.

---

## Citation

If you use this code, please cite both the manuscript and the archived software
release. See `CITATION.cff`, or the "Cite this repository" link in the GitHub
sidebar.

## License

Code is released under the MIT License (see `LICENSE`). The license covers the
code in this repository only; it does not extend to the underlying MSW sampling
reports, which remain subject to the terms of the Ministry of Environment,
Taiwan project-report platform.
