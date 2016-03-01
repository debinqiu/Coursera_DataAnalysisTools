## Study association between student's Exercise and Smoke ##

In this assignment, we are required to run a chi-squared test of independence. To do so, we used a survey dataset that contains the responses of 237 Statistics I students at the University of Adelaide to a number of questions. We selected a subset of the data consisting of "Exer" and "Smoke" features because we want to study the dependence between Exercise and Smoke. To be specific, we were interested in columns:
- **Exercise**: how often the student exercises- "Freq" (frequently), "Some", "None".
- **Smoke**: how much the student smokes-"Heavy", "Regul" (regularly), "Occas" (occasionally), "Never".

The **null hypothesis** is that the frequency of student's smoking is not associated with the frequency of student's exercise. To make problem simpler, we classified "Exercise" into two levels: "Yes" for combining "Freq" and "Some", meaning student exercises and "None" meaning student does not exercise. We also recoded the categorical values into numeric values. That is,
- **Exercise**: "None" -> 0, "Freq" & "Some" -> 1 ("Yes").
- **Smoke**: "Heavy" -> 1, "Regul" -> 2, "Occas" -> 3, "Never" -> 4.

The contingency table is summarized as follows.

|       | Heavy   |  Regul  |  Occas  |  Never  |
|-------|-----|-----|-----|-----|
|  None    |  1  | 18  | 1   |  3  |
|  Yes    |  10 | 171 | 16  |  16 |

This can be obtained by the following Python code.
```python
>>> import pandas as pd
>>> from scipy.stats import chi2_contingency
>>> import seaborn
>>> import matplotlib.pyplot as plt

>>> data = pd.read_csv("/Users/Deman/Desktop/Survey.csv")
>>> survey = data.dropna()

>>> recode_exer = {"Freq": 1, "None":0, "Some":1}
>>> recode_smok = {"Heavy": 1, "Never": 2, "Regul": 3, "Occas": 4}
>>> survey1 = survey.copy()
>>> survey1["Exercise"] = survey1["Exercise"].map(recode_exer)
>>> survey1["Smoke"] = survey1["Smoke"].map(recode_smok)

>>> ct1 = pd.crosstab(survey1['Exercise'],survey1['Smoke'])
>>> print(ct1)
Smoke      1    2   3   4
Exercise                 
0          1   18   1   3
1         10  171  16  16
```
