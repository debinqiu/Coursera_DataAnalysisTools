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
We first run an ANOVA without considering the gender variable. That is, we perform a one-way ANOVA using 'attractiveness' and 'Beer'. The means of attractiveness for each level of alcohol consumption are 63.7500, 64.6875, 46.5625, respectively. This implies that the attractiveness increases as drinking beer increases from 0 to 2, but decreases as drinking beer decreases from 2 to 4. In other words, more drunk would lead to less attractive. Also, the ANOVA yields F-statistic 13.31 and p-value 2.88e-05 indicating to reject the null hypothesis that there is no difference in attractiveness for different levels of alcohol consumption. 
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
