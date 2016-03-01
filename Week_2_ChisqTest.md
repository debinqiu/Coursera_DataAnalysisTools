## Study association between student's Exercise and Smoke ##

In this assignment, we are required to run a chi-squared test of independence. To do so, we used a survey dataset that contains the responses of 237 Statistics I students at the University of Adelaide to a number of questions. We selected a subset of the data consisting of "Exer" and "Smoke" features because we want to study the dependence between Exercise and Smoke. To be specific, we were interested in columns:
- **Exercise**: how often the student exercises- "Freq" (frequently), "Some", "None".
- **Smoke**: how much the student smokes-"Heavy", "Regul" (regularly), "Occas" (occasionally), "Never".

The **null hypothesis** is that the frequency of student's smoking is not associated with the frequency of student's exercise. To make problem simpler, we classified "Exercise" into two levels: "Yes" for combining "Freq" and "Some", meaning student exercises and "None" meaning student does not exercise. We also recoded the categorical values into numeric values. That is,
- **Exercise**: "None" -> 0, "Freq" & "Some" -> 1 ("Yes").
- **Smoke**: "Never" -> 1, "Occas" -> 2, "Regul" -> 3, "Heavy" -> 4.

The contingency table is summarized as follows.

|       | Heavy   |  Regul  |  Occas  |  Never  |
|-------|-----|-----|-----|-----|
|  None    |  18  | 3  | 1   |  1  |
|  Yes    |  171 | 16 | 16  |  10 |

This can be obtained by the following Python code.
```python
>>> import pandas as pd
>>> from scipy.stats import chi2_contingency
>>> import seaborn
>>> import matplotlib.pyplot as plt

>>> data = pd.read_csv("/Users/Deman/Desktop/Survey.csv")
>>> survey = data.dropna()

>>> recode_exer = {"Freq": 1, "None":0, "Some":1}
>>> recode_smok = {"Never": 1, "Occas": 2, "Regul": 3, "Heavy": 4}
>>> survey1 = survey.copy()
>>> survey1["Exercise"] = survey1["Exercise"].map(recode_exer)
>>> survey1["Smoke"] = survey1["Smoke"].map(recode_smok)

>>> ct1 = pd.crosstab(survey1['Exercise'],survey1['Smoke'])
>>> print(ct1)
Smoke      1    2   3   4
Exercise                 
0          18   3   1   1
1         171  16  16  10
```
Before checking the association, we first look at the column percentages of the contingency table as follows. The column percentages indicate that there may be no association between student's exercise and smoke because as the frequency of smoking increases, the frequency of exercise ("1") or no exercise ("0") does not have a monotonic pattern. 
```python
>>> colsum = ct1.sum(axis = 0)
>>> colpct = ct1/colsum
>>> print(colpct)

Smoke            1         2         3         4
Exercise                                        
0         0.095238  0.157895  0.058824  0.090909
1         0.904762  0.842105  0.941176  0.909091
```

To verify our conjecture, we run a chi-square test on this contingency table. The chi-square statistic is 1.093 with degree of freedom 3, resulting in p-value = 0.779 which is much larger than the significant level 0.05. Thus, we do not have enough evidence to reject the null hypothesis. That is, there is no association between student's exercise and smoking in terms of the frequency. This is also verfied by the bar plot shown as below.
```python
print("chi-square value, p value, degree freedom")
cs1 = chi2_contingency(ct1)
print(cs1[0:3])
print("Expected counts")
print(cs1[3:4])

chi-square value, p value, degree freedom
(1.093423342257849, 0.77866194761347707, 3)
Expected counts
(array([[  18.41949153,    1.85169492,    1.65677966,    1.0720339 ],
       [ 170.58050847,   17.14830508,   15.34322034,    9.9279661 ]]),)
       
>>> seaborn.factorplot(x="Smoke", y="Exercise", data=survey1, kind="bar", ci=None)
>>> plt.xlabel('Smoke')
>>> plt.ylabel('Proportion Exercise')
```
Even though the chi-square test is not significant, we still perform post hoc comparison just for exercise. We first define a function to run the chi-square test for each comparison of smoking, such as "Never" vs "Occas". The Bonferrnoi adjustment p-value is 0.05/6 = 0.0083. In other words, the difference of comparison like "Never" vs "Occas" is significant if the p-value is smaller than 0.0083. 
```python
def comp(smk_level_cmp):
    ctab = pd.crosstab(survey['Exercise'].map(recode_exer),survey['Smoke'].map(smk_level_cmp))
    colsum = ctab.sum(axis = 0)
    colpct = ctab/colsum
    print(colpct)
    print("------------------------------")
    cs =  chi2_contingency(ctab)
    print("chi-square value, p value, degree freedom")
    print(cs[0:3])
    
>>> print("'Never' vs 'Occas'")    
>>> comp({"Never":1, "Occas":2})   
   'Never' vs 'Occas'
Smoke            1         2
Exercise                    
0         0.095238  0.157895
1         0.904762  0.842105
------------------------------
chi-square value, p value, degree freedom
(0.21595226784670993, 0.64214154758039343, 1)

>>> print("'Never' vs 'Regul'")    
>>> comp({"Never":1, "Regul":3})
'Never' vs 'Regul'
Smoke            1         3
Exercise                    
0         0.095238  0.058824
1         0.904762  0.941176
------------------------------
chi-square value, p value, degree freedom
(0.0035368558406256396, 0.95257658823847302, 1)

>>> print("'Never' vs 'Heavy'")  
>>> comp({"Never":1, "Heavy":4})
'Never' vs 'Heavy'
Smoke            1         4
Exercise                    
0         0.095238  0.090909
1         0.904762  0.909091
------------------------------
chi-square value, p value, degree freedom
(0.23164669864291856, 0.6303055053870541, 1)

>>> print("'Occas' vs 'Regul'")  
>>> comp({"Occas":2, "Regul":3})
'Occas' vs 'Regul'
Smoke            2         3
Exercise                    
0         0.157895  0.058824
1         0.842105  0.941176
------------------------------
chi-square value, p value, degree freedom
(0.17066563467492257, 0.67952091342098364, 1)

>>> print("'Occas' vs 'Heavy'")  
>>> comp({"Occas":2, "Heavy":4})
'Occas' vs 'Heavy'
Smoke            2         4
Exercise                    
0         0.157895  0.090909
1         0.842105  0.909091
------------------------------
chi-square value, p value, degree freedom
(0.0013801987486198087, 0.97036458705070494, 1)

>>> print("'Regul' vs 'Heavy'")  
>>> comp({"Regul":3, "Heavy":4})
'Regul' vs 'Heavy'
Smoke            3         4
Exercise                    
0         0.058824  0.090909
1         0.941176  0.909091
------------------------------
chi-square value, p value, degree freedom
(0.18428630193336085, 0.66771524617245703, 1)
```
In summary, the six post hoc comparison p-values are listed as follows. They are all greater than 0.0083 which indicates no any difference between the comparison. 

| Comparison | p-value |
|------------|---------|
|"Never" vs "Occas"| 0.64 |
|"Never" vs "Regul"| 0.95 |
|"Never" vs "Heavy"| 0.63 |
|"Occas" vs "Regul"| 0.68 |
|"Occas" vs "Heavy"| 0.97 |
|"Regul" vs "Heavy"| 0.67 |
