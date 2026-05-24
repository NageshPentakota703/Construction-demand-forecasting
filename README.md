**Construction Labor Demand Forecasting: Predicting the Next Construction Boom:**\
End-to-end quarterly forecasting framework built in R to predict sector level labor demand for construction services by combining internal labor hours data with external macroeconomic indicators.
This project demonstrates the design and implementation of a regression based forecasting framework integrating internal Lithko data with public macroeconomic indicators from FRED and the U.S. Census Bureau to support workforce and bid pipeline planning.

**Project Overview:**\
Construction service firms experience dramatic sector level demand shifts (warehouse boom during COVID, data center boom today, post-COVID office decline) that are typically identified only after activity has already accelerated, limiting effective workforce planning.
This project builds a quarterly forecasting framework to:
- Identify early macroeconomic signals that lead construction labor demand
- Compare a baseline model (Lithko data only) against augmented models (with external indicators)
- Validate model performance using out-of-sample testing on held-out quarters
- Apply a pre-specified selection rule to pick the best forecast horizon per sector
- Deliver an actionable, reproducible framework for ongoing planning use

The solution was implemented in R using a unified forecasting framework applied uniformly across all five tested construction sectors.

**Workflow**
- Source data collected from Lithko internal databases and external macroeconomic data sources (FRED, U.S. Census Bureau).
- R based data preparation pipeline aggregated daily labor hours into quarterly time series by sector.
- External monthly indicators converted to quarterly frequency using arithmetic mean of three months.
- Baseline and augmented regression models trained on 2019 Q1 - 2024 Q4 data.
- Models tested on held out 2025 Q1 - Q4 quarters using out-of-sample MAPE.
- Pre-specified selection rule applied across all sectors and horizons to pick the best forecasting model.
- Results delivered as a knitted HTML report, a 27-page client paper, and a presentation deck.

**Architecture Overview:**\
The project architecture consists of five primary layers:

*Data Sources*
- Lithko internal data (Jobs, Labor Hours, Customers, Business Units, Regions)
- Federal Reserve Economic Data (FRED) : macroeconomic indicators
- U.S. Census Bureau : construction statistics

*Data Preparation Layer*
- R scripts for cleaning, joining, and quarterly aggregation
- Quarter factor encoding and COVID structural-break dummy
- Lag-1 through lag-6 variable construction for autoregressive modeling

*Modeling Layer*
- Baseline model: AR(1) + COVID dummy
- Augmented Parsimonious (Levels) —> baseline + single best-correlated indicator
- Augmented Parsimonious (Change-in) —> baseline + first-differenced indicator
- Augmented Full —> baseline + all indicators + quarter factor

*Validation Layer*
- Time ordered hold out validation (train 2019–2024, test 2025)
- Out-of-sample MAPE and RMSE as evaluation metrics
- Prespecified selection rule (MAPE ≤ 30%, lowest MAPE wins, longer horizon breaks ties)

*Reporting Layer*
- R Markdown notebook with reproducible analysis
- Knitted HTML output with all regression tables and diagnostic charts
- Final report (PDF) and client presentation deck (PPTX)

**Architecture Diagram:**\
*Lithko Internal Data + FRED + U.S. Census Bureau Indicators -> R Data Prep Pipeline (Quarterly Aggregation + Lag Construction) -> Baseline & Augmented Regression Models -> Time-Ordered Hold-Out Validation -> Sector × Horizon MAPE Matrix -> Deck-Ready Cross-Sector Summary*

**Technology Stack:**

*Programming*
- R

*Development Environment*
- RStudio
- R Markdown

*Libraries*
- tidyverse (dplyr, tidyr, purrr) -> data manipulation
- broom -> tidy regression output
- kableExtra -> formatted tables
- lubridate -> date handling
- ggplot2 -> visualization
- scales -> number formatting

*Statistical Methods*
- Multiple Linear Regression
- Time-Series Forecasting
- Out-of-Sample Validation
- Lag-Structured Predictor Design

*Data Sources*
- Federal Reserve Economic Data (FRED)
- U.S. Census Bureau Construction Statistics

*Output Formats*
- HTML (knitted notebook)
- PDF (final report)
- PPTX (client presentation deck)

**Dataset Description:**\
*Two categories of data were integrated:*

*Lithko Internal Data*
- 28 quarters of historical labor hours (2019 Q1 – 2025 Q4)
- Source tables: Jobs, Labor Hours, Customers, Business Units, Regions
- Aggregated to quarterly cadence by construction sector

*External Macroeconomic Indicators*
- FRED indicators: DC construction spending, PNFI (Private Nonresidential Fixed Investment), industrial production, total capacity utilization, PCE deflator, utility production, 10-Year Treasury rate (DGS10), e-commerce retail sales
- Census Bureau series: undcon5musa (multifamily units under construction), multifamily permits (5+ units), construction spending by sector
- Monthly series converted to quarterly using arithmetic mean of three months

The datasets were integrated using quarterly date relationships to enable combined regression analysis.

**Forecasting Methodology:**\
*The methodology pipeline performs the following steps:*

*Extract*
- Read internal Lithko labor data from production database tables
- Pull external macroeconomic indicators from FRED and U.S. Census Bureau

*Transform*
- Standardize date formats and column conventions
- Define true construction start dates from first logged labor hour per job
- Aggregate daily labor hours into quarterly totals per sector
- Convert monthly external indicators into quarterly values (mean of three months)
- Add COVID structural-break dummy (1 in 2020 Q1-Q4, 0 elsewhere)
- Remove warehouse outlier quarters (z-score > 2 on labor hours)
- Construct lag-1 through lag-6 columns for autoregressive and indicator variables

*Model*
- Fit baseline model: labor_hours ~ labor_hours_lag(h) + COVID
- Fit augmented Levels: baseline + best-correlated indicator at lag(h)
- Fit augmented Change-in: baseline + first-differenced indicator at lag(h)
- Fit augmented Full: baseline + all indicators + quarter factor at lag(h)
- Repeat for every (sector × horizon) combination across 5 sectors and 6 horizons

*Validate*
- Train models on 2019 Q1 - 2024 Q4
- Test models on held-out 2025 Q1 - Q4 (4 quarters)
- Compute out-of-sample MAPE and RMSE

*Select*
- Apply pre-specified rule: must beat baseline RMSE, MAPE ≤ 30%, lowest MAPE wins, longer horizon breaks ties
- Report selected horizon and indicator per sector
- Report sectors with no actionable signal transparently as negative findings

**Model Selection Rule:**\
*A pre-specified, transparent rule applied uniformly across all sectors and horizons:*

*Filter 1*
- Augmented RMSE must be lower than baseline RMSE (must improve over baseline)

*Filter 2*
- Out-of-sample MAPE must be at most 30% (planning-quality threshold)

*Sort*
- Lowest MAPE wins among surviving horizons
- Longer horizon breaks ties (more planning runway for the business)

*No-Signal Fallback*
- If no horizon between 3Q and 6Q satisfies both filters, the sector is labeled "No actionable signal" and reported transparently as a negative finding

**Validation:**\
*To ensure forecast accuracy and methodological rigor:*
- Time-ordered hold-out: 22–27 training observations, 4 held-out test quarters
- Lag-h restriction: at horizon h, only lag-h predictors used (no information leakage)
- Both baseline and augmented models trained on the same window
- Out-of-sample MAPE used as headline metric, RMSE used for selection rule
- Pre-specified rule prevents post-hoc cherry-picking

**Sample Analytical Outputs:**\
*Example analyses produced by the framework:*
- Sector × horizon out-of-sample MAPE matrix (5 sectors × 6 horizons)
- Per-sector forecast plots (actual vs baseline vs augmented across full timeline)
- Regression coefficient tables with estimate, standard error, and p-value
- Cross-sector summary with selected horizon, indicator, MAPE, and lift
- Baseline bias diagnostic plots
- Indicator audit trail (best-correlated indicator per sector per horizon)

**Key Results:**

| Sector | Picked Horizon | Spec | Indicator(s) | MAPE | Lift |
|---|---|---|---|---|---|
| Manufacturing | 5Q Ahead | Levels | PNFI | 5.1% | 83.8% |
| Data Center | 5Q Ahead | Full | DC capex + utility + TCU + PCE | 7.5% | 80.1% |
| Apartment | 4Q Ahead | Levels | undcon5musa | 9.7% | 23.7% |
| Warehouse | 6Q Ahead | Full | e-commerce + PNFI + warehouse + DGS10 | 21.2% | 39.7% |
| Office | — | — | No actionable signal at 3Q–6Q | — | — |

Four of the five tested sectors produced actionable forecasting signals at planning-relevant horizons, giving the business roughly 12–15 months of forward visibility on labor demand.

**Screenshots:**
- *Sector × Horizon Out-of-Sample MAPE Matrix*
[Upload screenshot here]

- *Data Center 5-Quarter Ahead Forecast Plot*
[Upload screenshot here]

- *Manufacturing 5-Quarter Ahead Forecast Plot*
[Upload screenshot here]

- *Cross-Sector Summary Table*
[Upload screenshot here]

- *Sample Regression Coefficient Output*
[Upload screenshot here]

**Key Learning Outcomes:**\
*This project demonstrates practical experience with:*
- Time series forecasting methodology design
- Multiple linear regression with lag-structured predictors
- Out-of-sample validation on small samples
- Pre specified model selection rules to prevent cherry-picking
- Reproducible analysis using R Markdown
- Communicating technical results to mixed business and technical audiences
- Honest reporting of negative findings (the Office no-signal result)
- Translating macroeconomic indicators into business-relevant leading indicators
- Acknowledging and mitigating small-sample limitations openly

**Future Improvements:**\
*Potential enhancements include:*
- Extending data window to enable walk forward (rolling-origin) validation
- Incorporating bid pipeline data as operational leading indicators
- Modeling at the project subtype level (hyperscale vs. colocation data centers, etc.)
- Re testing the Office sector with rental-rates-per-sq-ft and return-to-office data
- Automating quarterly data refresh from FRED and Census Bureau APIs
- Building an interactive forecast dashboard for ongoing planning use

**Author:**\
Nagesh Pentakota\
**Master of Business Analytics | University of Dayton**\
***LinkedIn:*** https://www.linkedin.com/in/nagesh-pentakota/

**Project Team:**\
Bhuvaneshwar Sanappareddy · Sai Ram Jella · Gemechu Tola

**Advisor:**\
Professor Michael Gorman — University of Dayton MBAN Program

**Client:**\
Lithko Contracting
