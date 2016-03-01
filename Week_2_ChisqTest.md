## Study association between Exercise and Smoke ##

In this assignment, we are required to run a chi-squared test of independence. To do so, we used survey data that contains the responses of 237 Statistics I students at the University of Adelaide to a number of questions. We selected a subset data consisting of "Exer" and "Smoke" columns because we want to study the dependence of Exercise and Smoke. To be specific, we were interested in columns:
- **Exercise**: how often the student exercises- "Freq" (frequently), "Some", "None".
- **Smoke**: how much the student smokes-"Heavy", "Regul" (regularly), "Occas" (occasionally), "Never".

The **null hypothesis** is that the frequency of student smoking is not associated with the frequency of student exercise. To make problem simpler, we classified "Exercise" as two levels: "Yes" for combining "Freq" and "Some" meaning student exercises and "None" meaning student does not exercise. We also recoded the categorical values into numeric values. That is,
- **Exercise**: "None" -> 0, "Freq" & "Some" -> 1.
- **Smoke**: "Heavy" -> 1, "Regul" -> 2, "Occas" -> 3, "Never" -> 4.
