# Optimal Model for Post-Surgery Patient Survival (using R)

This project aims to select the optimal model, through a model-building process, that will best predict survival time (in days) after a specific liver operation from pre-operative measurements, and to choose the best linear regression model.

## Data

A hospital surgery unit wanted to predict survival in patients undergoing a particular liver operation. A random sample of 108 patients was available; this analysis uses the first 54.

Source: (Textbook) Applied Linear Statistical Models (5th Edition) by Kutner, Nachtsheim, Neter, and Li.

| Variable | Description |
|---|---|
| `blood` | Blood clotting score |
| `prog` | Prognostic index |
| `enzyme` | Enzyme function test score |
| `liver` | Liver function test score |
| `age` | Age in years |
| `gender` | 1 = female, 0 = male |
| `a.mod` | 1 = moderate alcohol use, 0 = otherwise |
| `a.heavy` | 1 = heavy alcohol use, 0 = otherwise |
| `survival` | Survival time after surgery (days), the response |

## Methods

1. **Full model and diagnostics.** Fit a linear model of survival on all eight predictors. Checked linearity, normality, and constant variance with residual and Q-Q plots (`ggResidpanel`). The constant variance assumption was violated.
2. **Transformation.** A Box-Cox analysis (`lindia`) gave lambda = 0.1 (95% CI: -0.1 to 0.3). Because the interval includes 0, I used the log transformation (ln of survival), which is easier to interpret. After refitting, the residual variance was more constant, and the main outliers in the Q-Q plot were handled.
3. **Model selection.** Two approaches on the transformed response:
   - *Best subset search* (`leaps`): all 256 possible models were searched. Adjusted R-squared rose quickly up to four predictors and then flattened at about 0.82. It was highest at six predictors.
   - *Backward elimination by AIC* (`step`): dropped `liver`, then `a.mod`, and stopped at six predictors (AIC = -163.86).
4. **Final-model check.** Variance inflation factors for the selected model were all below 1.12, so there was no sign of multicollinearity.

## Result

Both methods chose the same model:

`ln(survival) ~ blood + prog + enzyme + age + gender + a.heavy`

## Limitations

- Small sample (54 patients).
- Model selection and fit used the same data, with no hold-out set or cross-validation, so fit statistics are likely optimistic.
- Adjusted R-squared and AIC are both in-sample criteria.

## Files

- `Optimal Model for Post-Surgery Patient Survival.Rmd`: R Markdown source
- `Optimal Model for Post-Surgery Patient Survival.pdf`: rendered output

## Packages

`tidyverse`, `dplyr`, `ggplot2`, `cowplot`, `ggResidpanel`, `lindia`, `leaps`, `car`
