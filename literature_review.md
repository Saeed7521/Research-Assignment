# Literature Review

Topic: **Linear regression**, applied to predicting fuel efficiency (mpg) on the Auto MPG dataset.

## 1. Hoerl, A. E., & Kennard, R. W. (1970). *Ridge Regression: Biased Estimation for Nonorthogonal Problems.* Technometrics, 12(1), 55-67.
Link: https://www.stat.cmu.edu/technometrics/70-79/VOL-12-01/v1201055.pdf

The authors argue that ordinary least squares can give unreliable coefficients when the predictors are correlated with each other (not orthogonal). Their fix is to add a small positive constant to the diagonal of X'X, which trades a little bias for a large drop in variance. They also introduce the "ridge trace", a plot of how coefficients shift as that constant grows, to help choose it. This is the basis for using Ridge in my experiment, since engine size, cylinders, horsepower and weight in cars all move together.

## 2. TensorFlow Authors. *Basic regression: Predict fuel efficiency* (tutorial).
Link: https://www.tensorflow.org/tutorials/keras/regression

This tutorial uses the same Auto MPG data and walks through loading it, dropping rows with unknown values, and turning the numeric origin code into USA/Europe/Japan columns. It then fits a one-variable linear model (horsepower to mpg) before moving to multiple inputs and a neural network. It is a useful template for the data preparation steps, and a reminder that a simple linear model should be the starting point before anything more complex.

## 3. UMass Dartmouth student report. *Analysis on Auto data using Multiple Linear Regression.*
Link: https://shashankmth522.sites.umassd.edu/files/2023/02/Project_1_Data_set_2.pdf

This report fits an ordinary least squares model on a subset of the ISLR Auto data (displacement, horsepower, weight, acceleration) to explain mpg. It finds that more weight and more horsepower go with lower mileage, and it cautions that correlation is not causation and that other factors such as aerodynamics or maintenance are not measured. It shows what a basic interpretable baseline looks like, and its limits, since it only uses four predictors and does not test on held-out data.

## 4. Georgia College & State University student poster. *Analysis of Gas Mileage of a Car.*
Link: https://kb.gcsu.edu/src/2020/poster_presentations/19

This poster studies 392 cars from the ISLR Auto data set (originally from CMU's StatLib) and finds that performance traits such as horsepower and displacement go with worse mileage. Its best simple models use weight together with model year, and it also tries log transforms of weight and mpg. That supports my plan to include model year and a log of weight as engineered features, because the raw relationship with mpg is curved.

## 5. NTNU course assignment (TMA4268, 2018). *Compulsory exercise 2.*
Link: https://www.math.ntnu.no/emner/TMA4268/2018v/Compulsory2.html

This exercise, based on the ISLR textbook, sets aside 20% of the Auto data as a test set and compares model selection, ridge regression and lasso using test-set mean squared error. It shows the standard workflow I will follow: split first, choose settings using only the training data, then report error on untouched test data. It also frames ridge and lasso as ways to shrink coefficients when there are many related predictors.
