# Modeling Inflation Dynamics in Argentina

*Econometric analysis of the relationship between exchange-rate movements and inflation dynamics in Argentina using time-series models.*

This project develops a time-series model to analyze inflation dynamics in Argentina and examine their relationship with monthly exchange-rate movements.

The modeling strategy is inspired by the iterative **identification–estimation–diagnostic checking** logic of the Box–Jenkins methodology. Rather than focusing on forecasting, the analysis uses this iterative process to investigate the following research question:

> **Are monthly exchange-rate movements associated with changes in Argentina's inflation rate, and what are the temporal dynamics of this relationship?**

The analysis examines the stationarity properties of the series, identifies the dynamics of the conditional mean, tests for conditional heteroskedasticity, compares alternative model specifications, and evaluates the selected model through residual diagnostics.

The resulting specification is an **AR(2)-X-ARCH(2) model**, in which the change in monthly inflation is modeled using two autoregressive terms and the contemporaneous monthly percentage change in the exchange rate, while an ARCH(2) process captures conditional volatility.

The model is intended to characterize **statistical associations and temporal dynamics**, rather than identify a causal effect of exchange-rate movements on inflation.

## Data

The analysis uses **115 monthly observations covering the period from January 2017 to July 2026**, obtained from two official Argentine sources:

- **Consumer Price Index (CPI):** obtained from Argentina's National Institute of Statistics and Censuses (INDEC) and used to calculate the monthly inflation rate.
- **Exchange rate:** obtained from the Central Bank of Argentina (BCRA) and transformed into its monthly percentage variation.

The dataset was organized at a monthly frequency and checked for missing values and date consistency before the econometric analysis.

The main variables used in the modeling process are:

- `inflacion_mensual`: monthly inflation rate derived from the CPI.
- `variacion_dolar`: monthly percentage change in the exchange rate.
- `delta_inflacion`: change in the monthly inflation rate.

The latter is defined as:
Δπₜ = πₜ − πₜ₋₁


Therefore, `delta_inflacion` captures the **acceleration or deceleration of monthly inflation**, measured in percentage points.

## Tools and Technologies

- **Python**
  - **pandas** — data cleaning, transformation, and time-series preparation
  - **NumPy** — numerical operations
  - **statsmodels** — stationarity tests, ARIMA/ARIMAX estimation, Granger causality, Ljung–Box tests, and CUSUM diagnostics
  - **arch** — ARCH/GARCH estimation and volatility modeling
  - **Matplotlib** — time-series and diagnostic visualizations

---

## Methodology

The modeling strategy draws inspiration from the **Box–Jenkins methodology**, particularly its iterative logic of identification, estimation, diagnostic checking, and model re-specification.

Because the objective of this project is explanatory rather than forecasting-oriented, the forecasting stage was omitted.

The analysis followed the following process:

1. **Data preparation and transformation**
   - Monthly frequency and date consistency were verified.
   - CPI data were transformed into monthly inflation rates.
   - The exchange rate was transformed into monthly percentage changes.

2. **Stationarity analysis**
   - Augmented Dickey–Fuller (ADF) and KPSS tests were applied to assess the stationarity properties of the variables used in the analysis.
   - The monthly inflation rate (`inflacion_mensual`) showed ambiguous/non-stationary behavior.
   - The monthly inflation rate was therefore first-differenced, producing `delta_inflacion`, which measures the month-to-month change in the inflation rate in percentage points. This transformed series was found to be stationary.
   - For the exchange rate, the analysis used its monthly percentage variation (`variacion_dolar`) rather than the exchange-rate level. This series was found to be stationary and therefore did not require additional differencing.
3. **Initial identification of mean dynamics**
   - ACF and PACF plots of the stationary change in monthly inflation were examined to identify potential short-run serial dependence.
   - Alternative ARIMA specifications were explored using AIC and BIC as an initial assessment of the series' dynamic structure.

4. **Incorporation of the exchange rate**
   - Monthly exchange-rate variation was incorporated as a regressor because the central research question concerns its relationship with inflation dynamics.
   - Specifications with additional exchange-rate lags were also evaluated.

5. **Conditional heteroskedasticity**
   - ARCH-LM tests revealed evidence of conditional heteroskedasticity.
   - ARCH(1), ARCH(2), and GARCH(1,1) variance specifications were estimated and compared using information criteria and residual diagnostics.
   - ARCH(2) adequately captured the conditional variance dynamics, but significant autocorrelation remained in the standardized residuals.

6. **Mean re-specification**
   - The remaining serial correlation indicated that the conditional mean was under-specified.
   - ACF and PACF plots of the standardized residuals suggested remaining short-run dependence, particularly around the second lag.
   - AR(0), AR(1), and AR(2) conditional mean specifications were therefore compared while retaining the exchange-rate regressor and ARCH(2) variance structure.
   - Both AIC and BIC favored the AR(2)-X-ARCH(2) specification.

7. **Final diagnostic checking**
   - Ljung–Box tests were applied to standardized residuals and squared standardized residuals.
   - ARCH-LM tests were used to assess remaining conditional heteroskedasticity.
   - ACF and PACF plots were inspected for remaining serial dependence.
   - The final standardized residuals were compatible with white noise.
   - CUSUM was used as an additional parameter-stability diagnostic.

8. **Granger causality**
   - Granger causality tests were used as a complementary analysis of the predictive relationship between exchange-rate movements and changes in inflation.
     
## Stationarity Results

Stationarity was assessed before model estimation to avoid modeling relationships between non-stationary series.

| Variable | Test | Statistic | p-value | Interpretation |
|---|---|---:|---:|---|
| Monthly inflation | ADF | -2.354 | 0.155 | Unit root cannot be rejected |
| Monthly inflation | KPSS | 0.381 | 0.085 | Stationarity is not rejected at 5% |
| Change in monthly inflation | ADF | -5.004 | <0.001 | Stationary |
| Change in monthly inflation | KPSS | 0.095 | >0.10 | Stationarity is not rejected |
| Monthly exchange-rate variation | ADF | -8.439 | <0.001 | Stationary |

The ADF and KPSS tests provided mixed evidence regarding the stationarity of the monthly inflation rate. After first-differencing the inflation rate, both tests supported treating the resulting series (`delta_inflacion`) as stationary.

The monthly percentage variation in the exchange rate (`variacion_dolar`) was also found to be stationary and was therefore included without additional differencing.

The transformation can also be observed visually in the time-series plots below.
### Monthly Inflation Rate

![Monthly inflation rate](inflation_time_series.png)

The monthly inflation rate exhibits substantial changes in its level and volatility over the sample, with particularly pronounced movements during 2023–2024.

### Change in Monthly Inflation

![Change in monthly inflation](inflation_time_series_diff.png)

After first-differencing the monthly inflation rate, the resulting series fluctuates around zero, although periods of markedly higher volatility remain visible, particularly around 2023–2024. This visual pattern is consistent with the subsequent investigation of conditional heteroskedasticity. 
## Identification of Mean Dynamics

The ACF and PACF of `delta_inflacion` were examined to identify potential short-run dependence in the conditional mean.

![ACF/PACF FIGURE](ACF_PACF.png)

Both correlograms show significant negative dependence around the second lag, while no simple cutoff pattern clearly identifies a unique AR or MA specification. Therefore, the correlograms were used as an initial identification tool rather than as the sole criterion for model selection.

## Initial ARIMA Specification

Following the stationarity analysis and the inspection of the ACF and PACF, alternative ARIMA specifications were estimated as an initial characterization of the dynamics of monthly inflation.

The candidate models were compared using the Akaike Information Criterion (AIC) and Bayesian Information Criterion (BIC):

| Model | AIC | BIC |
|---|---:|---:|
| **ARIMA(2,1,2)** | **462.363** | 476.044 |
| ARIMA(2,1,0) | 466.272 | **474.481** |
| ARIMA(2,1,1) | 467.594 | 478.539 |
| ARIMA(0,1,2) | 468.829 | 477.038 |
| ARIMA(1,1,2) | 470.522 | 481.466 |
| ARIMA(0,1,0) | 475.358 | 478.094 |
| ARIMA(1,1,1) | 476.226 | 484.435 |
| ARIMA(0,1,1) | 477.210 | 482.683 |
| ARIMA(1,1,0) | 477.308 | 482.781 |

AIC favored the **ARIMA(2,1,2)** specification, while BIC favored the more parsimonious **ARIMA(2,1,0)**. ARIMA(2,1,2) was initially retained under the AIC criterion as an exploratory specification of the conditional mean.

This specification served as an initial modeling step rather than the final model. The exchange-rate variable was subsequently incorporated to address the main research question.

## Exchange Rate and Initial ARIMAX Specification

Monthly exchange-rate variation was incorporated as a regressor because the central research question concerns the relationship between exchange-rate movements and inflation dynamics.

As shown in the stationarity analysis, the ADF test strongly rejected the presence of a unit root in `variacion_dolar` (ADF = -8.439, p < 0.001). The variable was therefore included as its stationary monthly percentage change, without additional differencing. Using the stationary percentage-change series rather than the exchange-rate level also reduces the risk of estimating a spurious time-series relationship.

The initial ARIMA(2,1,2) specification was then extended by including contemporaneous monthly exchange-rate variation as an external regressor.

### Initial ARIMAX Results

| Parameter | Estimate | p-value |
|---|---:|---:|
| Exchange-rate variation | **0.116** | **<0.001** |
| AR(1) | -0.119 | 0.508 |
| AR(2) | -0.960 | <0.001 |
| MA(1) | 0.086 | 0.675 |
| MA(2) | 0.955 | <0.001 |

The resulting model achieved an **AIC of 415.254** and a **BIC of 431.671**.

The coefficient on contemporaneous exchange-rate variation was positive and statistically significant. A one-percentage-point increase in monthly exchange-rate variation was associated with an approximately **0.116 percentage-point increase in monthly inflation**, conditional on the dynamics represented by this initial specification.

However, several AR and MA parameters were not individually statistically significant, and satisfactory modeling of the conditional mean does not imply that the residual variance is adequately specified. Residual diagnostics were therefore conducted before accepting the model.

## Conditional Heteroskedasticity

The residuals from the initial ARIMAX specification were tested for ARCH effects using the **Engle ARCH-LM test**.

The null hypothesis of the ARCH-LM test is that no ARCH effects are present in the residuals.

| Lag | LM Statistic | p-value |
|---:|---:|---:|
| 3 | 11.975 | 0.008 |
| 6 | 13.737 | 0.033 |
| 12 | 21.490 | 0.044 |

The null hypothesis was rejected at the 5% significance level across all three lag specifications, providing evidence of **conditional heteroskedasticity**.

Although the ARIMAX specification captured important conditional mean dynamics, the ARCH-LM results indicated that the variance of the innovations was not constant and exhibited temporal dependence.

This motivated extending the analysis to explicit ARCH and GARCH conditional variance models.

### Volatility Model Selection

Three alternative volatility specifications were estimated and compared: ARCH(1), ARCH(2), and GARCH(1,1).

| Volatility specification | AIC | BIC | Log-Likelihood |
|---|---:|---:|---:|
| ARCH(1) | 398.558 | 409.503 | -195.279 |
| **ARCH(2)** | **383.996** | **397.677** | **-186.998** |
| GARCH(1,1) | 386.728 | 400.409 | -188.364 |

Among these candidate specifications, ARCH(2) achieved the lowest AIC and BIC. However, information criteria alone do not establish model adequacy. The standardized residuals and squared standardized residuals were therefore examined to assess whether the selected variance specification adequately captured the remaining temporal structure.
### Volatility Model Diagnostics

Residual diagnostics revealed an important distinction between the conditional mean and variance specifications.

For the ARCH(2) model, the Ljung–Box tests applied to the squared standardized residuals did not reject the null hypothesis of no serial dependence:

| Diagnostic | Lag 6 | Lag 12 |
|---|---:|---:|
| Ljung–Box: standardized residuals | 0.003 | 0.001 |
| Ljung–Box: squared standardized residuals | 0.506 | 0.367 |

Similarly, the ARCH-LM tests on the ARCH(2) standardized residuals produced p-values of 0.889, 0.505, and 0.192 at lags 3, 6, and 12, respectively.

These results suggested that the ARCH(2) specification successfully captured the main conditional variance dynamics.

However, the Ljung–Box tests on the standardized residuals themselves remained statistically significant. This indicated that serial dependence was still present in the residuals even after modeling conditional volatility.

The remaining dependence therefore pointed to an **under-specified conditional mean rather than remaining ARCH effects**. This motivated a re-specification of the mean equation.

### Residual Autocorrelation Structure

To investigate the remaining serial dependence, the ACF and PACF of the standardized residuals from the ARCH(2) specification were examined.

![ACF and PACF of standardized residuals](Standardized_Residuals_acf_pacf.png)

The correlograms showed remaining short-run dependence, with a particularly noticeable pattern around the second lag. This provided evidence that additional autoregressive structure in the conditional mean should be considered.

Rather than selecting the autoregressive order solely from the correlograms, alternative mean specifications were subsequently estimated and compared.

## Mean Re-specification and Model Selection

To address the remaining serial correlation, AR(0), AR(1), and AR(2) conditional mean specifications were estimated while retaining the contemporaneous exchange-rate regressor and the ARCH(2) conditional variance structure.

The competing specifications were compared using AIC, BIC, and log-likelihood:

| Model | AIC | BIC | Log-Likelihood |
|---|---:|---:|---:|
| AR(0)-X-ARCH(2) | 383.996 | 397.677 | -186.998 |
| AR(1)-X-ARCH(2) | 376.754 | 393.119 | -182.377 |
| **AR(2)-X-ARCH(2)** | **370.379** | **389.408** | **-178.189** |

The **AR(2)-X-ARCH(2)** specification achieved the lowest AIC and BIC and the highest log-likelihood among the candidate models.

The improvement obtained by introducing two autoregressive terms was also consistent with the residual dependence observed in the previous specification. The AR(2)-X-ARCH(2) model was therefore selected for final diagnostic evaluation.

## Final Model: AR(2)-X-ARCH(2) 
  
## Limitations and Further Research

The AR(2)-X-ARCH(2) specification models changes in monthly inflation as the dependent variable and includes contemporaneous exchange-rate variation as a regressor. Therefore, the estimated exchange-rate coefficient should be interpreted as a conditional statistical association rather than a causal effect.

Granger causality tests indicate bidirectional predictive relationships between exchange-rate movements and changes in inflation, suggesting that the two variables may interact dynamically rather than follow a strictly one-directional relationship.

A natural extension of this analysis would be to model inflation and exchange-rate dynamics jointly within a Vector Autoregression (VAR) framework and examine their dynamic responses through impulse-response functions.
