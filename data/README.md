# Data directory

**This directory is intentionally empty in the public repository.**

## Why the dataset is not included

The analysis uses 594 municipal solid waste sampling records (2014–2019,
covering all 22 counties and cities of Taiwan), compiled from official project
reports published on the public project-report platform maintained by the
Ministry of Environment, Taiwan.

The platform's copyright notice restricts unauthorized reproduction,
modification and redistribution of website content. Neither the source reports
nor the harmonized row-level dataset derived from them may therefore be
redistributed by the author, and no row-level data file is committed to this
repository or included in the archived release.

## How to obtain the source data

Report titles, retrieval details and source-availability notes are given in
**Supplementary S1** of the manuscript. The standard analytical methods (NIEA)
corresponding to each measured variable are listed in **Supplementary Table S1**.
Descriptive statistics for all 25 analysed composition components, computed from
the 589 screened records, are reported in **Supplementary Table S2**, so the
composition and scale of the dataset can be assessed without access to the
row-level file.

## Expected input format

Place the harmonized dataset here as `msw_composition.csv`, or point
`MSW_DATA_PATH` at its location. Required variables:

- dry-basis percentages of the composition components (the sampling protocol
  defines 26; `Pl11` is zero in every record and is dropped automatically,
  leaving 25 analysed components);
- moisture content (wet basis, %);
- carbon content, used for the HHV/carbon thermochemical consistency screening;
- dry-basis higher heating value (DBH), the modelling target;
- sampling date, parsed for leave-one-year-out and final-year analyses;
- county/city identifier, retained as sample metadata.

Column headings do not need to match the internal variable names. Section 1b of
the notebook contains the complete column mapping; editing that mapping is
sufficient to run the analysis on a differently-labelled dataset.

## Running without the dataset

If no input file is found, the notebook generates a **synthetic** dataset so that
the full pipeline can be executed and verified end to end. Synthetic runs
confirm that the code executes correctly but **cannot** reproduce the numerical
results reported in the manuscript. A prominent warning is printed whenever the
synthetic path is taken, and the run is flagged in the reproducibility summary.
