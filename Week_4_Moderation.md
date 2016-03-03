## Alcohol consumption on the attractiveness of members of the opposite sex ##

In this assignment, we need to deal with testing a potential moderator (interaction). To do so, we run an ANOVA which includes the moderator. We used "beer goggles" data to determine whether the alcohol consumption has on the apparent attractiveness of members of the opposite sex. The data were collected in an experiment such that 24 men and 24 women were randomly assigned to a placebo (nonalcoholic
lager), 2-pint, or 4-pint drink. The independent raters give a photo of the person that participated in this experiment an attractiveness rating on a scale from 1 to 100. To be specific, the data contains the following columns:
- **attractiveness**: attractiveness rating from 1 to 100.
- **Beer**: drink level in pints(Categorical: 0,2,4).
- **Gender**: Sex (Categorical: Male, Female). 

The details of the data can be seen as follows. 
```python
>>> import os
>>> import pandas as pd
>>> import statsmodels.formula.api as smf
>>> import numpy as np
>>> import matplotlib.pyplot as plt
# import attractiveness data
>>> data = pd.read_csv("attractiveness.csv")
# drop missing data
>>> data = data.dropna()
>>> data.head()

Out[5]: 
   Attractiveness  Beer(pints)  Gender
0              65            0  Female
1              70            0  Female
2              60            0  Female
3              60            0  Female
4              60            0  Female
```
We first run an ANOVA without considering the gender variable. That is, we perform a one-way ANOVA using 'attractiveness' and 'Beer'. The means of attractiveness for each level of alcohol consumption are 63.7500, 64.6875, 46.5625, respectively. This implies that the attractiveness increases as drinking beer increases from 0 to 2, but decreases as drinking beer decreases from 2 to 4. In other words, more drunk would lead to less attractive. Also, the ANOVA yields F-statistic 13.31 and p-value 2.88e-05 indicating to reject the null hypothesis that there is no difference in attractiveness for different levels of alcohol consumption. There is sufficient
evidence to indicate that different levels of alcohol consumption yields a different mean attractiveness rating in conversation partners.
```python
# convert 'Beer(pints)' into categorical values
>>> data['Beer'] = data['Beer(pints)'].astype('category')
>>> del data['Beer(pints)']

# means for each consumption of Beer in pints
>>> print(data.groupby("Beer").mean())
      Attractiveness
Beer                
0            63.7500
2            64.6875
4            46.5625

# run an ANOVA for attractiveness with different levels of consumption in Beer
>>> model = smf.ols(formula = "Attractiveness ~ C(Beer)", data = data).fit()
>>> print(model.summary())
                            OLS Regression Results                            
==============================================================================
Dep. Variable:         Attractiveness   R-squared:                       0.372
Model:                            OLS   Adj. R-squared:                  0.344
Method:                 Least Squares   F-statistic:                     13.31
Date:                Wed, 02 Mar 2016   Prob (F-statistic):           2.88e-05
Time:                        21:18:34   Log-Likelihood:                -182.48
No. Observations:                  48   AIC:                             371.0
Df Residuals:                      45   BIC:                             376.6
Df Model:                           2                                         
Covariance Type:            nonrobust                                         
================================================================================
                   coef    std err          t      P>|t|      [95.0% Conf. Int.]
--------------------------------------------------------------------------------
Intercept       63.7500      2.797     22.789      0.000        58.116    69.384
C(Beer)[T.2]     0.9375      3.956      0.237      0.814        -7.031     8.906
C(Beer)[T.4]   -17.1875      3.956     -4.345      0.000       -25.156    -9.219
==============================================================================
Omnibus:                        0.105   Durbin-Watson:                   1.121
Prob(Omnibus):                  0.949   Jarque-Bera (JB):                0.234
Skew:                          -0.100   Prob(JB):                        0.890
Kurtosis:                       2.722   Cond. No.                         3.73
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
```

We now consider 'Gender' as the potential moderator that may affect the association between attractiveness and alcohol consumption. In other words, we run an ANOVA on "attractiveness" and "beer" for different gender. First of all, we run an ANOVA for the subset of gender = Male. The ANOVA result shows F-statistic 20.52 and p-value 1.15e-05 indicating different levels of alcohol consumption yields a different mean attractiveness rating in conversation partners for males. 
```python
# consider a moderator 'Gender' variable
>>> data_male = data[(data['Gender'] == 'Male')]
>>> data_female = data[(data['Gender'] == 'Female')]

# run an ANOVA for Gender = Male
>>> act_mean_male = data_male.groupby("Beer").mean()
>>> print(act_mean_male)
      Attractiveness
Beer                
0             66.875
2             66.875
4             35.625

>>> model_male = smf.ols(formula = "Attractiveness ~ C(Beer)", data = data_male).fit()
>>> print(model_male.summary())
                            OLS Regression Results                            
==============================================================================
Dep. Variable:         Attractiveness   R-squared:                       0.661
Model:                            OLS   Adj. R-squared:                  0.629
Method:                 Least Squares   F-statistic:                     20.52
Date:                Wed, 02 Mar 2016   Prob (F-statistic):           1.15e-05
Time:                        21:32:28   Log-Likelihood:                -90.576
No. Observations:                  24   AIC:                             187.2
Df Residuals:                      21   BIC:                             190.7
Df Model:                           2                                         
Covariance Type:            nonrobust                                         
================================================================================
                   coef    std err          t      P>|t|      [95.0% Conf. Int.]
--------------------------------------------------------------------------------
Intercept       66.8750      3.983     16.789      0.000        58.591    75.159
C(Beer)[T.2] -2.132e-14      5.633  -3.78e-15      1.000       -11.715    11.715
C(Beer)[T.4]   -31.2500      5.633     -5.547      0.000       -42.965   -19.535
==============================================================================
Omnibus:                        0.122   Durbin-Watson:                   1.914
Prob(Omnibus):                  0.941   Jarque-Bera (JB):                0.337
Skew:                          -0.076   Prob(JB):                        0.845
Kurtosis:                       2.440   Cond. No.                         3.73
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
```
However, the ANOVA result below shows that there is no difference in attractiveness rating for different alcohol consumption for females. In summary, the average attractiveness for females for the three levels of alcohol is almost the same. Nevertheless, for males the difference in average attractiveness for the three levels is quite difference. This indicates gender is a moderator. 

```python
# run an ANOVA for Gender = Female
>>> act_mean_female = data_female.groupby("Beer").mean()
>>> print(act_mean_female)
      Attractiveness
Beer                
0             60.625
2             62.500
4             57.500

>>> model_female = smf.ols(formula = "Attractiveness ~ C(Beer)", data = data_female).fit()
>>> print(model_female.summary())
                            OLS Regression Results                            
==============================================================================
Dep. Variable:         Attractiveness   R-squared:                       0.110
Model:                            OLS   Adj. R-squared:                  0.026
Method:                 Least Squares   F-statistic:                     1.304
Date:                Wed, 02 Mar 2016   Prob (F-statistic):              0.292
Time:                        21:33:17   Log-Likelihood:                -76.457
No. Observations:                  24   AIC:                             158.9
Df Residuals:                      21   BIC:                             162.4
Df Model:                           2                                         
Covariance Type:            nonrobust                                         
================================================================================
                   coef    std err          t      P>|t|      [95.0% Conf. Int.]
--------------------------------------------------------------------------------
Intercept       60.6250      2.212     27.410      0.000        56.025    65.225
C(Beer)[T.2]     1.8750      3.128      0.599      0.555        -4.630     8.380
C(Beer)[T.4]    -3.1250      3.128     -0.999      0.329        -9.630     3.380
==============================================================================
Omnibus:                        0.221   Durbin-Watson:                   1.518
Prob(Omnibus):                  0.895   Jarque-Bera (JB):                0.258
Skew:                           0.192   Prob(JB):                        0.879
Kurtosis:                       2.668   Cond. No.                         3.73
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
```

To see the moderator (interaction) "gender" with "beer", we look at the interaction graph which confirms the existness of interaction between "gender" and "beer".
```python
>>> p1, = plt.plot(np.array([0,2,4]), act_mean_male.Attractiveness.values)
>>> p2, = plt.plot(np.array([0,2,4]), act_mean_female.Attractiveness.values)
>>> plt.xlim([-1,5])
>>> plt.legend([p1,p2],['Male','Female'],loc = 'lower left')
>>> plt.xlabel("Beer (pints)")
>>> plt.ylabel("Attractiveness")
>>> plt.title("Mean plot of attractiveness for Male and Female")
```
