## Study the association between the number of distressed O-rings vs temperature and pressure##

In this assignment, we are dealing with pearson correlation which measures the linear association between two variables. We used shuttle launch data containing four variables from NASA:

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

We will examine the relationship between the number of distressed O-rings and launch temperature, the number of distressed O-rings and field check pressure, respectively. To explore the possible association, we first make scatter plots to visualize the linear relationships between distress_ct versus temperature, and field_check_pressure. 
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
