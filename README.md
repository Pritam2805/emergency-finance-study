# Account ownership and emergency finance in India

Among lower-income Indian adults, how is reported financial-institution account ownership associated with severe difficulty arranging emergency money? This work sample uses the **Global Findex 2025 release, with India survey responses collected in 2024**.

Read the [three-page research note](research_note.pdf) for the findings, characteristics table, figures, and limitations.

## Findings

Gaps compare reported account ownership with no reported account, in percentage points, after adjustment for measured characteristics.

| Outcome | Adjusted gap | Approximate 95% interval |
|---|---:|---:|
| Severe difficulty: unable or very difficult | −7.1 | [−13.5, −0.6] |
| Any difficulty: unable, very or somewhat difficult | +1.1 | [−3.5, +5.7] |

The finding depends on the difficulty threshold. The broader outcome has a small positive point estimate and an interval spanning zero. These estimates do not identify a causal effect of account ownership.

## Data and method

The sample comprises adults aged 15+ in the bottom two within-India household-income quintiles: 1,077 respondents, with four unknown outcomes excluded, leaving **1,073**. The no-reported-account code combines no, do not know, and refusal. The emergency question concerns a hypothetical amount equal to **5% of local-currency GNI per capita within 30 days**.

The primary model is weighted binomial logistic regression, fitted with statsmodels GLM, with age, age squared, gender, education, income quintile, and rural/urban location. Adjusted gaps average each respondent's predicted account-versus-no-account difference using the survey weights. Checks cover equal weights, a linear probability model, workforce adjustment, the broader outcome, and unadjusted missing-outcome bounds.

Reported logit intervals use the original-weight HC0 score sandwich and the delta method; the linear model uses WLS with HC3. These are standard methods. The uncertainty calculations treat respondents as independent and do not fully account for the survey design. Unmeasured differences between account groups and hypothetical, self-reported answers limit interpretation. See [data_notes.md](data_notes.md) for the exact calculation, method references, release, definitions, coverage, and published benchmark checks.

## Reproduce

Download `Findex_Microdata_2025_updateIndia.dta` from the [World Bank microdata access page](https://microdata.worldbank.org/catalog/7916/get-microdata), release `IND_2024_FINDEX_v02_M`, under its access terms. Raw respondent data are excluded from this package and should not be redistributed with it.

Tested with Python 3.14.6. From this folder:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Place the downloaded file in `data/raw/`, or set its path before launching the notebook interface:

```bash
export FINDEX_DATA_PATH="/path/to/Findex_Microdata_2025_updateIndia.dta"
```

Open `analysis.ipynb` in a Jupyter-compatible interface, select this Python environment as the kernel, and run all cells from the top. Outputs are written under `results/`.

## Files

- `research_note.pdf`: concise report by Mohan Pritam K, Mahindra University.
- `analysis.ipynb`: complete analysis, validation, model checks, and figure generation.
- `data_notes.md`: data provenance, definitions, coverage, and benchmark sources.
- `results/tables/`: sample summary, group characteristics, difficulty distribution, primary coefficients, model checks, missing-outcome bounds, and benchmark validation; `model_diagnostics.csv` records model rank, convergence, and fitted-value ranges.
- `results/figures/`: two figures in PNG, PDF, and SVG formats.
- `results/metadata/provenance.json`: input fingerprint, definitions, sample counts, and software versions.
- `requirements.txt`: pinned analysis and notebook-kernel dependencies.
