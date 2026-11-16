# Data record

Source: [World Bank Global Findex, India](https://microdata.worldbank.org/catalog/7916), release `IND_2024_FINDEX_v02_M`. The 2025 release contains responses collected in 2024.

The file used here is `Findex_Microdata_2025_updateIndia.dta`, with 3,000 respondents and 199 columns. Its SHA-256 fingerprint is:

```text
0e5275bfc374ecc8328a41e8850416e051336b0aa071cc457660af1a4c93df93
```

Download the file from the [microdata access page](https://microdata.worldbank.org/catalog/7916/get-microdata) under the World Bank's terms. Respondent-level data are excluded from this package.

## Definitions and coverage

- `inc_q` groups respondents by household income within India. Codes 1 and 2 select the bottom two quintiles; this is a relative income group, not a poverty-line definition. The survey population starts at age 15.
- `account_fin = 1` records a reported financial-institution account. Code 0 combines no, do not know, and refusal. It does not distinguish verified nonowners, and ownership does not measure account activity or balances.
- `fin24` and `fin24a` concern arranging a hypothetical amount equal to 5% of local-currency GNI per capita within 30 days. They do not record a realized financial shock. People answering “unable” skip the difficulty follow-up and remain severe cases in this analysis.
- The [survey methodology](https://thedocs.worldbank.org/en/doc/be6615202d1f08a25855c8ac2d615122-0050012025/related/Survey-methodology.pdf) lists India coverage exclusions for the northeast states, remote islands, and Jammu and Kashmir, together less than 10% of the population. Weights cannot supply observations from these areas. The methodology lists 3,086 interviews; the public file has 3,000. The reason for that difference is not established here.

## Estimation and uncertainty

The [statsmodels GLM](https://www.statsmodels.org/stable/generated/statsmodels.genmod.generalized_linear_model.GLM.html) fits a binomial model with a logit link. Each supplied survey weight multiplies its respondent's Bernoulli log-likelihood contribution once. The `freq_weights` argument supplies this weighted objective; its default frequency-weight covariance is not used for the reported logit intervals.

For fitted probability `p_i`, column covariate vector `x_i`, response `y_i`, and original weight `w_i`, the notebook calculates the independent-respondent HC0 sandwich:

```text
B = sum_i w_i p_i (1 - p_i) x_i x_i'
s_i = w_i x_i (y_i - p_i)
V = B^-1 [sum_i s_i s_i'] B^-1
```

The middle term therefore contains squared weights. This is the standard score-sandwich calculation, with no HC0 finite-sample correction. The weighted linear probability check uses statsmodels WLS with the standard [HC3 leverage correction](https://www.statsmodels.org/stable/generated/statsmodels.regression.linear_model.OLSResults.HC3_se.html). Its two predictions above one are retained and reported as a limitation of the linear specification.

For each respondent, predictions are made with account set first to zero and then to one, retaining the other observed covariates. The weighted average of the account-1 minus account-0 predictions is the adjusted gap. The delta method propagates the coefficient covariance into its standard error, holding this observed covariate distribution fixed. Intervals use the normal critical value; gaps and endpoints are multiplied by 100 to report percentage points. [Stata's margins documentation](https://www.stata.com/manuals/rmargins.pdf) describes prediction averaging and this conditional delta-method approach.

The [R survey GLM documentation](https://r-survey.r-forge.r-project.org/pkgdown/docs/reference/svyglm.html) explains how survey variance estimation generalizes robust sandwich methods using design information. This file supplies weights without PSU identifiers, strata, or replicate weights. The notebook's independent-respondent approximation consequently omits those design adjustments and survey degrees of freedom; it is not a fully design-adjusted survey analysis. Stata and R are method references, not software used to produce these results.

## Checks against published indicators

The notebook compares four weighted estimates with the [Little Data Book's India table (PDF page 84)](https://thedocs.worldbank.org/en/doc/be6615202d1f08a25855c8ac2d615122-0050012025/related/Little-Data-Book-2025-Web.pdf#page=84): any account, financial-institution account, any account in the bottom 40%, and emergency-funds resilience. All four match the published one-decimal percentages. This checks selected indicators, not every published result.

The [glossary (PDF page 169)](https://thedocs.worldbank.org/en/doc/be6615202d1f08a25855c8ac2d615122-0050012025/related/Little-Data-Book-2025-Web.pdf#page=169) defines the abbreviated emergency-resilience label to include **somewhat difficult or not difficult** responses. Its denominator is all respondents, including people unable to obtain money. That published indicator differs from this project's severe-difficulty outcome. Definitions and calculated values are saved in `results/tables/benchmark_validation.csv`.

Variable documentation: [account status](https://microdata.worldbank.org/catalog/7916/variable/F1/V14?name=account_fin), [emergency-money source](https://microdata.worldbank.org/catalog/7916/variable/F1/V82?name=fin24), and [difficulty follow-up](https://microdata.worldbank.org/catalog/7916/variable/F1/V83?name=fin24a).
