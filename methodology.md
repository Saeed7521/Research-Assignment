# Methodology

## 1. Refined research question

> How accurately can a linear model predict a car's fuel efficiency (mpg) from its engine and body characteristics, and does a regularized linear model (Ridge), combined with engineered features, predict unseen cars better than plain ordinary least squares (OLS)?

Hypotheses: (a) weight, horsepower and model year will be the strongest drivers of mpg; (b) because several predictors are strongly correlated, Ridge will be at least as good as OLS on held-out data; (c) engineered features (log weight, power-to-weight ratio) will lower the error of both models compared with the raw features.

## 2. Dataset description

**Source:** Auto MPG dataset, UCI Machine Learning Repository (originally from the StatLib library at Carnegie Mellon University). The copy used is `data/auto_mpg.csv`.

| Item | Detail |
|---|---|
| Size | 398 rows, 9 columns |
| Target | `mpg` (miles per gallon, continuous, range 9.0-46.6, mean 23.5) |
| Numeric features | `cylinders`, `displacement`, `horsepower`, `weight`, `acceleration`, `model_year` (70-82) |
| Categorical feature | `origin` (usa 249, japan 79, europe 70) |
| Identifier | `name` (car make and model; not used as a raw feature) |
| Missing values | `horsepower` is missing in 6 rows; no other column has missing values |
| Duplicates | none |

**Limitations**
- Small sample (398 cars), so test results will be noisy and sensitive to which rows land in the test set.
- Old data: cars from model years 1970-1982, so conclusions may not transfer to modern vehicles (hybrids, turbochargers, fuel injection).
- Predictors are heavily correlated (weight, displacement, cylinders and horsepower all correlate with mpg between -0.78 and -0.83), which makes individual coefficients unstable in OLS.
- Origin is imbalanced (about 63% USA), and only 7 cars have 3 or 5 cylinders.
- Several important factors are not recorded (aerodynamics, transmission, fuel type, driving conditions).

## 3. Data cleaning plan

1. Load the CSV and confirm the shape, data types and missing-value counts.
2. Check for duplicate rows and impossible values (non-positive weight or mpg).
3. Fill the 6 missing `horsepower` values with the **median of the training set** (done inside a scikit-learn pipeline so no information leaks from the test set). Dropping them would lose 1.5% of the data for no gain.
4. Keep outliers (e.g. mpg above 40, weight above 5000 lb) because they are real cars, but inspect them in plots.
5. Split the data 80/20 into train and test sets with a fixed random seed **before** any fitting or scaling.

## 4. Feature engineering plan

| New feature | How | Why |
|---|---|---|
| `log_weight` | natural log of `weight` | the mpg-weight relationship is curved; the log makes it closer to linear (motivated by the GCSU poster) |
| `power_to_weight` | `horsepower / weight` | captures how powerful a car is for its size |
| `disp_per_cyl` | `displacement / cylinders` | average cylinder size, separates large from small engines |
| `car_age` | `83 - model_year` | easier-to-read "age" scale; same information as year |
| `origin_europe`, `origin_japan` | one-hot encoding of `origin` (USA as baseline) | linear models need numbers for categories |
| Standardization | zero mean, unit variance, fitted on training data only | required so the Ridge penalty treats all features fairly |

`name` is dropped. `acceleration`, `cylinders`, `displacement` and `horsepower` are kept in their raw form so the models can use them directly.

## 5. Models and why

1. **Baseline (predict the training mean).** Gives a floor to confirm the models learn something.
2. **Ordinary least squares (OLS) linear regression.** The core method of the topic. It is simple, fast, and its coefficients can be interpreted.
3. **Ridge regression** with the penalty strength chosen by 5-fold cross-validation on the training set. Chosen because of the correlated predictors discussed by Hoerl and Kennard (1970): shrinking coefficients should make predictions more stable.

I will also compare each of OLS and Ridge on two feature sets (raw features only vs. raw plus engineered) to test hypothesis (c).

## 6. Evaluation metrics and why

- **RMSE (primary metric)**, in mpg. Same unit as the target, easy to explain ("typical error of about X mpg"), and it penalizes large misses more than small ones.
- **MAE**, in mpg. Less sensitive to a few bad predictions, so it shows typical error alongside RMSE.
- **R-squared**, the share of variance in mpg explained, which allows comparison with the baseline (R-squared of about 0).

Models are compared using 5-fold cross-validated RMSE on the training set and the final RMSE, MAE and R-squared on the 20% held-out test set. Residual plots will be checked for curvature or unequal spread, which would break linear regression assumptions.
