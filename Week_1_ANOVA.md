##Study the impact of background music on concentration##
We conducted research on whether background music impacts concentration on cognitively demanding tasks. To do so, we obtained a random sample of 100 people and randomly assigned each person to one of four groups: 
- Control (white noise)
- Rock
- Country
- Metal 

so that each group had an equal number of people. We operationalized concentration by counting the number of incorrect answers to 50 SAT-level verbal reasoning items completed under their assigned background music condition. The entire dataset can be downloaded here. Therefore, we are interested in whether there is some difference in concentration levels among the four different treatments. To justify our assumption, we use the variance of analysis (ANOVA). In other words, the **null hypothesis** (H0) is that there is no difference in concentration levels among the treatments. The **alternative hypothesis** (H1) is that there is some difference in concentration levels among the treatments. 

We first import data into Python and do some data cleaning as follows. We can see that each group has 25 observations. 
```python
>>> import pandas as pd
>>> import statsmodels.formula.api as smf
>>> import statsmodels.stats.multicomp as multi
>>> import matplotlib.pylab as plt

# import SAT test score data 
>>> SATtest = pd.read_csv("SATtest.csv")
# drop the missing observations
>>> SATtest = SATtest.dropna()

# convert categorical values into numeric values
>>> recode = {"Control": 1, "Rock": 2, "Country": 3, "Metal": 4}
>>> SATtest['Treatment'] = SATtest['Treatment'].map(recode)
>>> SATtest.groupby(by = 'Treatment').size()
Out[14]: 
Treatment
1    25
2    25
3    25
4    25
dtype: int64
```
To test the difference in SAT score for treatments, we first look at its mean and standard deviation for each treatment as follows. We can see that there may be some difference in concentration levels among the treatment, especially treatment 1 ("Control") and treatment 3 ("Country"). We can also see that the standard deviations are equal across the treatment, which indicates the assumption of equal standard deviation in ANOVA is satisfied. The boxplot of score for each treatment also verifies our proceeding conjectures.
```python
# check the means and standard deviation for each treatment
>>> print("means for SAT score by different treatment")
>>> print(SATtest.groupby(by = "Treatment").mean())
means for SAT score by different treatment
               Score
Treatment           
1           7.432498
2          10.141882
3          14.390506
4          12.281508
>>> print("standard deviations for SAT score by different treatment")
>>> print(SATtest.groupby(by = "Treatment").std())
standard deviations for SAT score by different treatment
              Score
Treatment          
1          2.186669
2          2.802557
3          3.948390
4          2.471412

# draw boxplot of score for each treatment
>>> boxes = []
>>> for i in range(4):
        boxes.append(SATtest.Score[SATtest.Treatment == i + 1])
>>> plt.figure()
>>> plt.boxplot(boxes)
>>> plt.xticks([1, 2, 3, 4], ['Control','Rock','Country','Metal'])
>>> plt.xlabel("Treatment")
>>> plt.ylabel("Score")
>>> plt.title("Boxplot of Score vs Treatment")
>>> plt.show()
```
We now perform ANOVA analysis and check if the difference is statistically significant. The result shows that F-statistic is 25.81 with degree-of-freedom (96,3). The p-value = 2.50e-12 which is extremely smaller than 0.05, indicating that we reject the null hypothesis that there is no difference in concentration levels among the treatments. In other words, we accept the alternative hypothesis that the concentration levels are different among the treatments. 
```python
>>> model = smf.ols(formula = "Score ~ C(Treatment)", data = SATtest)
>>> result = model.fit()
>>> print(result.summary())

                            OLS Regression Results                            
==============================================================================
Dep. Variable:                  Score   R-squared:                       0.446
Model:                            OLS   Adj. R-squared:                  0.429
Method:                 Least Squares   F-statistic:                     25.81
Date:                Fri, 26 Feb 2016   Prob (F-statistic):           2.50e-12
Time:                        22:27:00   Log-Likelihood:                -247.34
No. Observations:                 100   AIC:                             502.7
Df Residuals:                      96   BIC:                             513.1
Df Model:                           3                                         
Covariance Type:            nonrobust                                         
=====================================================================================
                        coef    std err          t      P>|t|      [95.0% Conf. Int.]
-------------------------------------------------------------------------------------
Intercept             7.4325      0.586     12.685      0.000         6.269     8.596
C(Treatment)[T.2]     2.7094      0.829      3.270      0.001         1.065     4.354
C(Treatment)[T.3]     6.9580      0.829      8.397      0.000         5.313     8.603
C(Treatment)[T.4]     4.8490      0.829      5.852      0.000         3.204     6.494
==============================================================================
Omnibus:                        1.804   Durbin-Watson:                   1.994
Prob(Omnibus):                  0.406   Jarque-Bera (JB):                1.460
Skew:                          -0.294   Prob(JB):                        0.482
Kurtosis:                       3.074   Cond. No.                         4.79
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
```
Since the difference in concentration levels among the treatments exist, we want to know more details of the differences using post hoc test. That is, we are curious about the pairwise difference that whether a specific treatment is different from others. The multi comparison results show that the mean of socre for treatment "Control" is different from that for all other treatments. Also, the mean of score for treatment "Rock" is significantly different from that for treatment "Country". However, "Rock" vs "Metal" and "Country" vs "Metal" have no any difference (reject = False). 
```python
>>> mc = multi.MultiComparison(SATtest['Score'],SATtest['Treatment'])
>>> print(mc.tukeyhsd().summary())
Multiple Comparison of Means - Tukey HSD,FWER=0.05
============================================
group1 group2 meandiff  lower  upper  reject
--------------------------------------------
  1      2     2.7094   0.5427 4.8761  True 
  1      3     6.958    4.7913 9.1247  True 
  1      4     4.849    2.6823 7.0157  True 
  2      3     4.2486   2.0819 6.4153  True 
  2      4     2.1396  -0.0271 4.3063 False 
  3      4     -2.109  -4.2757 0.0577 False 
--------------------------------------------
```
