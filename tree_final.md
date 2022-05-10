Random Forest and Boosting
================
DS2
5/6/2022

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

![](tree_final_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->![](tree_final_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

Distributions of other numeric variables.

![](tree_final_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->![](tree_final_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->![](tree_final_files/figure-gfm/unnamed-chunk-3-3.png)<!-- -->

### Correlation Analysis

![](tree_final_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

### Analyzing trends in data

From numeric variables, we found that `stl`,`x3p`, `age`,`gs` seem to
have some non-linear trends.

![](tree_final_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

From categorical variable `position`, extremely high values in salary
show in all positions and some teams.

![](tree_final_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Tree-based models

Categorical variable `team`have 30 classes, which will resulted in too
much dummy variables in our models. Therefore, we consider clustering
`team` into fewer class according to similar trends in the median and
standard deviation of player’s salary in each team.

We use k-mean clustering to cluster variable `team` in the training data
with class number k = 3. Variable `team` are clustered into the
following 3 clusters:

-   Cluster 1: BRK, GSW, LAL, MIA, MIL, NOP, PHI, POR, UTA
-   Cluster 2: ATL, CHI, CHO, CLE, DAL, DEN, DET, HOU, IND, MEM, MIN,
    NYK, OKC, ORL, PHO, SAC, SAS, TOR
-   Cluster 3: BOS, LAC, WAS

![](tree_final_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->![](tree_final_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

We add class labels for the newly generated clusters of `team` as
`team_cluster`, with values 1, 2, and 3 representing each clusters.

### Random forest

    ##    mtry splitrule min.node.size
    ## 97   26  variance             1

![](tree_final_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

### gbm

    ##    n.trees interaction.depth shrinkage n.minobsinnode
    ## 28    6000                 5     8e-04              1

![](tree_final_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

    ## A gradient boosted model with gaussian loss function.
    ## 6000 iterations were performed.
    ## There were 27 predictors of which 27 had non-zero influence.

### Comparison of Tree-based models

![](tree_final_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

    ##              model  cv_rmse
    ## 1  rf_cluster_team 5.422160
    ## 2 gbm_cluster_team 5.408213

### Best tree model

Generalized Boosted Regression Modeling (GBM) with tuning parameters:

-   `n.trees = 6000`: the total number of trees to fit

-   `interaction.depth = 5`: maximum depth of each tree

-   `shrinkage = 0.0008`: learning rate

-   `n.minobsinnode = 1`: the minimum number of observations in the
    terminal nodes of the trees

Test error: 4.745948

    ## [1] 4.745948

Variable Importance:

![](tree_final_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

    ##                         var     rel.inf
    ## minute               minute 36.80766413
    ## age                     age 15.27024366
    ## point                 point  8.20004685
    ## free_throw       free_throw  7.80013401
    ## fg_attempt       fg_attempt  5.07019798
    ## game_starting game_starting  3.45628930
    ## assistance       assistance  2.61183908
    ## ft_attempt       ft_attempt  2.18387162
    ## team_cluster2 team_cluster2  2.06013694
    ## defenssive_rb defenssive_rb  1.63905099
    ## turnover           turnover  1.61437208
    ## personal_foul personal_foul  1.60872804
    ## block                 block  1.56168258
    ## field_goal       field_goal  1.45090683
    ## game                   game  1.36309336
    ## x3p                     x3p  1.36115057
    ## x2p                     x2p  1.28342555
    ## x2p_attempt     x2p_attempt  1.21499282
    ## x3p_attempt     x3p_attempt  1.00544349
    ## steal                 steal  0.69500781
    ## total_rb           total_rb  0.68620168
    ## positionPF       positionPF  0.44308976
    ## offensive_rb   offensive_rb  0.42966587
    ## team_cluster3 team_cluster3  0.10085567
    ## positionSF       positionSF  0.03322843
    ## positionPG       positionPG  0.02802297
    ## positionSG       positionSG  0.02065793

Prediction on new observations:

![](tree_final_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

True salary (in million) for new observations:

    ##                     salary
    ## Cade Cunningham  10.050120
    ## Cam Reddish       4.670160
    ## Christian Wood   13.666667
    ## Corey Kispert     3.383640
    ## D'Angelo Russell 30.013500
    ## Danuel House Jr.  2.045094
