# Modeling inflation dynamics in Argentina 
Econometric analysis of the relationship between exchange rate movements and inflation dynamics in Argentina using time-series models.

This project develops a time-series model to analyze inflation dynamics in Argentina, drawing inspiration from the Box–Jenkins methodology.

Unlike a traditional Box–Jenkins forecasting exercise, the prediction stage is intentionally omitted. The objective is not to forecast future inflation, but to use the modeling process to investigate the following research question:

> **Are monthly exchange rate movements associated with changes in Argentina's inflation rate, and what are the temporal dynamics of this relationship?**

The analysis follows an iterative model-building approach: examining stationarity, identifying the dynamics of the conditional mean, testing for conditional heteroskedasticity, comparing alternative specifications, and evaluating the final model through residual diagnostics.

The resulting specification is an **AR(2)-X-ARCH(2) model**, where changes in monthly inflation are modeled as a function of their own past dynamics and the monthly variation in the exchange rate, while ARCH effects capture volatility clustering.

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

3. **Identification of mean dynamics**
   - ACF and PACF plots were examined.
   - Alternative autoregressive specifications were compared using AIC and BIC.

4. **Incorporation of the exchange rate**
   - Monthly exchange-rate variation was included as an explanatory variable because the central research question concerns its relationship with inflation dynamics.
   - Alternative specifications with additional exchange-rate lags were also evaluated.

5. **Conditional heteroskedasticity**
   - ARCH-LM tests revealed evidence of ARCH effects.
   - ARCH(1), ARCH(2), and GARCH(1,1) specifications were estimated and compared.

6. **Model re-specification**
   - Although the volatility models captured conditional heteroskedasticity, residual autocorrelation indicated that the conditional mean remained under-specified.
   - AR(0), AR(1), and AR(2) mean specifications were therefore compared while retaining the exchange-rate variable and ARCH(2) variance structure.

7. **Final diagnostics**
   - Ljung–Box tests were applied to standardized residuals and squared standardized residuals.
   - ARCH-LM tests were used to check for remaining conditional heteroskedasticity.
   - ACF and PACF plots were inspected for remaining serial dependence.
   - CUSUM was used as an additional parameter-stability diagnostic.

8. **Granger causality**
   - Granger causality tests were used as a complementary analysis of the predictive relationship between exchange-rate movements and changes in inflation.
  
     (I'll continue tomorrow)
