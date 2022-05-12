p8106 - Final Project - NBA Players Salary Prediction
================
Mingkuan Xu, Mengfan Luo, Yiqun Jin
5/6/2022

# Introduction

Describe your data set. Provide proper motivation for your work.

What questions are you trying to answer? How did you prepare and clean
the data?

## Data Preprocessing

## Part 0 - Data Preprocessing

## Part 1 - Exploratory Analysis

Since `minute` stands for minutes played per game, we will divided
variables stands for counts by `minute` to get a rate. These variables
includes `field_goal`, `fg_attempt` `x3p`, `x3p_attempt`, `x2p`,
`x2p_attempt`, `free_throw`, `ft_attempt`, `offensive_rb`
`defenssive_rb`, `total_rb`, `assistance`,`steal`, `block`, `turnover`,
`personal_foul` and `point`.

### Univariate Analysis

Distributions of the two categorical variables, `team` and `position`.

![](former_models_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->![](former_models_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

Distributions of other numeric variables.

![](former_models_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->![](former_models_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->![](former_models_files/figure-gfm/unnamed-chunk-3-3.png)<!-- -->

### Correlation Analysis

![](former_models_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### Analyzing trends in data

From numeric variables, we found that `stl`,`x3p`, `age`,`gs` seem to
have some non-linear trends.

![](former_models_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->![](former_models_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

From categorical variable `position`, extremely high values in salary
show in all positions and some teams.

![](former_models_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

# Models

## Data Partition

After getting an overview of data from exploratory analysis, we splitted
the dataset into training (80%) and testing (20%). We would use 10 fold
repeated cross validation to compare each model using training data and
then select a best model to predict on testing data. Based on the
exploratory analysis, we would build 8 models in four category: 1.
Linear Regression: (1) simple Linear Regression Model, (2) Elastic-net
Model, (3) Principal Component Regression Model (PCR) 2. Generalized
Linear Regression: (4) Generalized Addictive Model (GAM), (5)
Multivariate Adaptive Regression Splines Model (MARS) 3. Tree based
Models Models: (6) Random Forest, (7) Generalized Boosted Regression
Modeling (GBM) 4. Blackbox Model (8) neural network

## Part 1 Linear regression

### (a) Standard Least-Squared

There is no tuning parameter for standard least-squared model.

    ## [1] 44.32919

### (b) Elastic Net

The elastic-net model has two parameter, which are alpha (compromise
between LASSO and ridge) and lambda (the penalty term limits the number
or magnitude of predictor coefficients). The elastic-net model reached
its best tune at *α* = 0.6 and lambda = 0.44.

    ##     alpha    lambda
    ## 637   0.6 0.4412332

![](former_models_files/figure-gfm/elastic%20net-1.png)<!-- -->

\#\#\#(c) Principle Component Regression

The tuning parameter of PCR is the number of predictors included in the
final model. There are 12 components included in the model with minimum
RMSE.

    ##    ncomp
    ## 12    12

![](former_models_files/figure-gfm/principal%20component%20regression-1.png)<!-- -->

## Part 2 Generalized Linear Regression

### (a) GAM

There is no tuning parameter for GAM. The GAM model can capture the
non-linear trend in the model, but it may have a high variance. `age`,
`game_starting`, `assistance`, `personal_foul`, and `point` are
statistically significant predictors at 0.0001 significant level.

    ## 
    ## Family: gaussian 
    ## Link function: identity 
    ## 
    ## Formula:
    ## salary ~ s(age) + s(game) + s(game_starting) + s(free_throw) + 
    ##     s(ft_attempt) + s(defenssive_rb) + s(assistance) + s(block) + 
    ##     s(personal_foul) + s(point)
    ## 
    ## Parametric coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   8.5293     0.2958   28.84   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Approximate significance of smooth terms:
    ##                    edf Ref.df      F  p-value    
    ## s(age)           4.414  5.455 16.961  < 2e-16 ***
    ## s(game)          1.695  2.101  4.623  0.00973 ** 
    ## s(game_starting) 1.482  1.805 25.494  < 2e-16 ***
    ## s(free_throw)    8.147  8.791  3.083  0.00538 ** 
    ## s(ft_attempt)    1.000  1.000  0.155  0.69382    
    ## s(defenssive_rb) 1.000  1.000  1.680  0.19591    
    ## s(assistance)    1.000  1.000 18.244 2.58e-05 ***
    ## s(block)         1.000  1.000  2.758  0.09777 .  
    ## s(personal_foul) 6.851  7.891  5.172 6.56e-06 ***
    ## s(point)         6.152  7.361  5.415 5.90e-06 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## R-sq.(adj) =   0.69   Deviance explained = 71.8%
    ## GCV = 34.237  Scale est. = 30.974    n = 354

    ## [1] 46.82317

### (b) MARS

The tuning parameter for MARS is `nprune` and `degree`. When attempting
to fit the MARS model, we noticed that the RMSE increased drastically
when degree is over 3 and nprune is over 8. Therefore, we would choose
the range of degrees as 1:4 and range of nprune as 2:8. When number of
terms is 6 and product degree is 2, MARS model reached its best tune and
RMSE is lowest. The MARS model selected 6 of 69 terms, and 6 of 54
predictors. And the top 3 important predictors are: `age`, `minute`,
`game`. MARS model is highly adaptive comparing with previous models and
has a higher prediction accuracy.

    ##    nprune degree
    ## 19      6      3

    ## Call: earth(x=matrix[354,54], y=c(16.41,3.98,3...), keepxy=TRUE, degree=3,
    ##             nprune=6)
    ## 
    ##                            coefficients
    ## (Intercept)                    7.895265
    ## h(27.8-minute)                -0.331228
    ## h(minute-27.8)                 1.744475
    ## h(point-0.513812)             38.165222
    ## h(26-age) * h(minute-27.8)    -0.420986
    ## h(age-26) * h(minute-27.8)     0.183362
    ## 
    ## Selected 6 of 69 terms, and 3 of 54 predictors (nprune=6)
    ## Termination condition: RSq changed by less than 0.001 at 69 terms
    ## Importance: minute, x2p-unused, age, point, teamBOS-unused, teamBRK-unused, ...
    ## Number of terms at each degree of interaction: 1 3 2
    ## GCV 32.08731    RSS 10509.07    GRSq 0.6794564    RSq 0.7017558

![](former_models_files/figure-gfm/MARS-1.png)<!-- -->

    ## [1] 26.58079

## Model Comparison

The CV RMSE are shown as followed. We can see MARS model has lowest
RMSE.

|             | Min. | 1st Qu. | Median | Mean | 3rd Qu. | Max. | NA’s |
|:------------|-----:|--------:|-------:|-----:|--------:|-----:|-----:|
| LeastSquare | 4.41 |    6.12 |   6.85 | 6.79 |    7.46 | 8.75 |    0 |
| ElasticNet  | 4.57 |    5.95 |   6.37 | 6.45 |    7.06 | 8.55 |    0 |
| PCR         | 5.17 |    6.24 |   7.17 | 7.14 |    7.87 | 9.34 |    0 |
| MARS        | 4.05 |    5.25 |   5.89 | 6.03 |    6.71 | 8.74 |    0 |

RMSE of Different Models

![](former_models_files/figure-gfm/summary-1.png)<!-- -->

|      | Linear | ElasticNet | PCR  | GAM  | MARS |
|:-----|:-------|:-----------|:-----|:-----|:-----|
| RMSE | 6.66   | 6.04       | 5.46 | 6.84 | 5.16 |

RMSE of Different Models on Test Set
