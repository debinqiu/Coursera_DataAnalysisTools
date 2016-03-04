## Study the association between the number of distressed O-rings vs temperature and pressure##

In this assignment, we are dealing with pearson correlation which measures the linear association between two quantitative variables. We used shuttle launch data containing four variables from NASA:

- distress_ct: number of distressed O-rings
- temperature: shuttle launch temperature
- field_check_pressure: field check pressure
- flight_num: flight No.

```python
>>> import os
>>> import pandas as pd
>>> from scipy.stats import pearsonr
>>> import seaborn
>>> import matplotlib.pylab as plt

# import challenger data
>>> challenger = pd.read_csv("challenger.csv")
# drop missing data
>>> challenger = challenger.dropna()
```

We will examine the relationship between the number of distressed O-rings and launch temperature, the number of distressed O-rings and field check pressure, respectively. To explore the possible associations, we first make scatter plots to visualize the linear relationships between distress_ct versus temperature, and field_check_pressure. 
```python
# scatter plot of 'temperature' vs 'distress_ct'
>>> scat = seaborn.regplot(x = 'temperature',y = 'distress_ct',fit_reg = True, data = challenger)
>>> plt.xlabel('Temperature')
>>> plt.ylabel('Number of distressed O-rings')
>>> plt.title('Scatterplot for the Association Between Temperature and Number of Distressed O-rings')

# scatter plot of 'field_check_pressure' vs 'distress_ct'
>>> scat1 = seaborn.regplot(x = 'field_check_pressure',y = 'distress_ct',fit_reg = True, data = challenger)
>>> plt.xlabel('Temperature')
>>> plt.ylabel('Number of distressed O-rings')
>>> plt.title('Scatterplot for the Association Between Field Check Pressure and Number of Distressed O-rings')
```

![figure_1](https://cloud.githubusercontent.com/assets/16762941/13470615/09f5e41a-e07b-11e5-89db-6e06a01425c3.png)

![figure_2](https://cloud.githubusercontent.com/assets/16762941/13470616/09f6371c-e07b-11e5-9777-563dd745b4a5.png)

We can see from the scatter plots that the number of distressed O-rings may have a negative correlation with temperature but may have a slightly positive correlation with the field check pressure. To verify our conjectures, we calculate the pearson correlation coefficients as follows.
```python
# calculate the pearson correlation coefficients
>>> print('Association between temperature and distress_ct')
>>> print('---------------------------------------------')
>>> print('r-squared, p-value')
>>> print(pearsonr(challenger['temperature'], challenger['distress_ct']))
Association between temperature and distress_ct
---------------------------------------------
r-squared, p-value
(-0.51112638557405587, 0.012681761033635625)

>>> print('Association between pressure and distress_ct')
>>> print('---------------------------------------------')
>>> print('r-squared, p-value')
>>> print(pearsonr(challenger['field_check_pressure'], challenger['distress_ct']))
Association between pressure and distress_ct
---------------------------------------------
r-squared, p-value
(0.2846662708495058, 0.18800176478256808)
```
Thus, it can be seen that the number of distressed O-rings has a correlation coefficient -0.511 with temperature and 0.285 with field check pressure, which is consistent with our previous guesses. Also, p-values show that the correlation between the number of distressed O-rings and temperature is significant (0.013 < 0.05) but not significant (0.188 > 0.05) for the correlation between the number of distressed O-rings and field check pressure using significant level 0.05. In addition, since -0.51 is halfway to the maximum negative correlation of -1, this indicates the moderately strong negative linear relationship between the number of O-rings and temperature. This also implies a low temperature launch could be problematic for NASA engineers studying the O-ring data. Finally, 26.1% (= (-0.511)^2) of the variability in the number of distredded O-rings is described by variation in the temperature.
