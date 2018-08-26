---
title: "Data Aggregation using Pandas"
teaching: 20
exercises: 10
questions:
- "How can I summarise the data in a data frame?"
objectives:
- "Access and summarize data stored in a Data Frame"
- "Perform basic mathematical operations and summary statistics on data in a Pandas Data Frame"
- "Understand missing data"
- "Changing to and from 'NaN' values"
keypoints:
- "Summarising numerical and categorical variables is a very common requirement"
- "Missing data can interfere with how statistical summaries are calculated"
- "Missing data can be replaced or created depending on requirement"
- "Summarising or aggregation can be done over single or multiple variables at the same time"
---

## Using Pandas functions to summarise data in a Data Frame

For variables which contain numerical values we are often interested in various statistical measures relating to those values. For categorical variables we are often interested in how many of each unique values are present in the dataset.

We shall use the SAFI_clean.csv dataset to demonstrate how we can obtain these pieces of information

~~~
import pandas as pd
df_SAFI = pd.read_csv("SAFI_clean.csv")
df_SAFI
~~~
{: .language-python}

For numeric variables we can obtain a variety of basic statistical information by using the `describe()` method.

~~~
df_SAFI.describe()
~~~
{: .language-python}

~~~
           key_ID  no_membrs   years_liv       rooms   liv_count    no_meals
count  131.000000  131.00000  131.000000  131.000000  131.000000  131.000000
mean    85.473282    7.19084   23.053435    1.740458    2.366412    2.603053
std     63.151628    3.17227   16.913041    1.092547    1.082775    0.491143
min      1.000000    2.00000    1.000000    1.000000    1.000000    2.000000
25%     32.500000    5.00000   12.000000    1.000000    1.000000    2.000000
50%     66.000000    7.00000   20.000000    1.000000    2.000000    3.000000
75%    138.000000    9.00000   27.500000    2.000000    3.000000    3.000000
max    202.000000   19.00000   96.000000    8.000000    5.000000    3.000000
~~~
{: .output}

This can be done for the Dataframe as a whole, in which case some of the results might have no sensible meaning. If there are any missing values, represented in the display as `NaN` you will get a warning message.

You can also `.describe()` on a single variable basis.

~~~
df_SAFI['no_membrs'].describe()
~~~
{: .language-python}

There are also a set of methods which allow us to obtain individual values.

~~~
print(df_SAFI['no_membrs'].min())
print(df_SAFI['no_membrs'].max())
print(df_SAFI['no_membrs'].mean())
print(df_SAFI['no_membrs'].std())
print(df_SAFI['no_membrs'].count())
print(df_SAFI['no_membrs'].sum())
~~~
{: .language-python}

~~~
2
19
7.190839694656488
3.1722704895263734
131
942
~~~
{: .output}

Unlike the `describe()` method which converts the variable to a float (when it was originally an integer), the individual summary methods only do so for the returned result if needed.

<!--
We can do the same thing for the `rooms` variable

~~~
print(df_SAFI['rooms'].min())
print(df_SAFI['rooms'].max())
print(df_SAFI['rooms'].mean())
print(df_SAFI['rooms'].std())
print(df_SAFI['rooms'].count())
print(df_SAFI['rooms'].sum())
~~~
{: .language-python}

~~~
1
8
1.7404580152671756
1.092546680992075
131
228
~~~
{: output}
-->

<!--
> ## Exercise
>
> Compare the count values returned for the `no_membrs` and the `room` variables.
>
> 1. Why do you think they are different?
> 2. How does this affect the calculation of the mean values?
>
> > ## Solution
> >
> > 1. We know from when we originally displayed the contents of the `df_SAFI` Dataframe that there are 131 rows in it. This matches the value for the `B_no_membrs` count. The count for `E19_period_use` however is only 92. If you look at the values in the `E19_period_use` column using
> >
> > ~~~
> > df_SAFI['E19_period_use']
> > ~~~
> > {: .language-python}
> >
> > you will see that there are several `NaN` values. They also occurred when we used `describe()` on the full Dataframe. `NaN` stands for Not a Number, ie. the value is missing. There are only 92 non-missing values and this is what is reported by the `count()` method. This value is also used in the calculation of the mean and std values.
> >
> {: .solution}
{: .challenge}
-->

## Categorical variables

For categorical variables, numerical statistics don't make any sense.
For a categorical variable we can obtain a list of unique values used by the variable by using the `unique()` method.

~~~
pd.unique(df_SAFI['respondent_wall_type'])
~~~
{: .language-python}

~~~
array(['muddaub', ' muddaub', ' burntbricks', 'burntbricks', 'sunbricks', 'cement'], dtype=object)
~~~
{: output}

Do you notice how 'muddaub' and 'burntbricks' appear twice, once with and once without a leading whitespace? We obviously overlooked to remove these whitespaces correctly in OpenRefine. There are lots of ways to do that in Python, one is to use the argument `skipinitialspace=True` in the `pd.read_csv()` function. You can read more about this and other arguments for `pd.read_csv()` in the [pandas documentation](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html).

~~~
SAFI_df = pd.read_csv('SAFI_clean.csv', skipinitialspace=True)
pd.unique(df_SAFI['respondent_wall_type'])
~~~
{: .language-python}

~~~
array(['muddaub', 'burntbricks', 'sunbricks', 'cement'], dtype=object)
~~~
{: .output}


We can also count the number of values in categorial variables in the same way as for numeric variables using `count()`.

~~~
df_SAFI['respondent_wall_type'].count()
~~~
{: .language-python}


> ## Exercise
>
> 1. Get the unique entries for the `affect_conflicts` variable.
> 
> 2. Get the count value for `affect_conflicts` and compare it with the count for `respondent_wall_type`.
> 
> 3. Why do you think they are different?
>
> > ## Solution
> > 
> > ~~~
> > print(pd.unique(df_SAFI['affect_conflicts']))
> > print(df_SAFI['affect_conflicts'].count())
> > ~~~
> > {: .language-python}
> > 
> > 
> > We know from when we originally displayed the contents of the `df_SAFI` Dataframe that there are 131 rows in it. This matches the value for the `respondent_wall_type` count. The count for `affect_conflicts` however is only 92. If you look at the values in the `affect_conflicts` column using
> > 
> > ~~~
> > df_SAFI['affect_conflicts']
> > ~~~
> > {: .language-python}
> > 
> > you will see that there are several `NaN` values. `NaN` stands for Not a Number, i.e. the value is missing. There are only 92 non-missing values and this is what is reported by the `count()` method. For numeric variables, this value is also used in the calculation of the mean and std values.
> >
> {: .solution}
{: .challenge}


## Dealing with missing values

We can find out how many variables in our Dataframe contain any `NaN` values with the code

~~~
df_SAFI.isna().sum()
~~~
{: .language-python}

~~~
key_ID                   0
village                  0
interview_date           0
no_membrs                0
years_liv                0
respondent_wall_type     0
rooms                    0
memb_assoc              39
affect_conflicts        39
liv_count                0
items_owned             10
no_meals                 0
months_lack_food         0
instanceID               0
dtype: int64
~~~
{: output}

or for a specific variable

~~~
df_SAFI['affect_conflicts'].isna().sum()
~~~
{: .language-python}

~~~
39
~~~
{: output}

Data from most sources have the potential to include missing values. Whether or not this presents a problem at all depends on what you are planning to do.

<!-- We have been using data from two very different sources.

The SN7577 dataset is provided by the [UK Data Service](https://www.ukdataservice.ac.uk). Datasets from the UK data Service, have already been 'cleaned' and it is unlikely that there will be any genuinely missing data. However you may find that data which was missing has been replaced with a value such as '-1' or 'Not Specified'. In cases like these it may be appropriate to replace these values with 'NaN' before you try to process the data further.
-->

The SAFI dataset we are using comes from a project called 'Studying African Farmer-led Irrigation'. The data for this project is questionnaire based, but rather than using a paper-based questionnaire, it has been created and is completed electronically via an app on a smartphone. This provides flexibility in the design and presentation of the questionnaire; a section of the questionnaire may only be presented depending on the answer given to some preceding question. This means that there can quite legitimately be a set of 'NaN' values in a record (one complete questionnaire) where you would still consider the record to be complete.

We have already seen how we can check for missing values. There are three other actions we need to be able to do:

1. Remove complete rows which contain `NaN`
2. Replace `NaN` with a value of our choice
3. Replace specific values with `NaN`


With these options we can ensure that the data is suitable for the further processing we have planned.


### Completely remove rows with NaNs

The `dropna()` method will delete all rows if *any* of the variables contain an `NaN`. For some datasets this may be acceptable. You will need to take care that you have enough rows left for your analysis to have meaning.

~~~
print(df_SAFI.shape)
df_SAFI.dropna(inplace=True)
print(df_SAFI.shape)
~~~
{: .language-python}

~~~
(131, 14)
(88, 14)
~~~
{: output}

Note about the `inplace=True` argument: 
When `inplace=True` is passed to a Dataframe method, the resulting (changed) data are put in place of the original data and nothing is returned, i.e. the orginal Dataframe is changed accordingly. If using `inplace=False` instead, the default setting, the original Dataframe is not changed and a copy with the changed data is returned.
<!-- Because there are variables in the SAFI dataset which are all `NaN` using the `dropna()` method effectively deletes all of the rows from the Dataframe, probably not what you wanted. --> 

If we only want to remove the rows where a specific variable has `NaN` values, we can 
use the `subset` argument in `dropna()`.


~~~
df_SAFI = pd.read_csv("SAFI_clean.csv")
print(df_SAFI.shape)
df_SAFI.dropna(subset = ['items_owned'], inplace=True)
print(df_SAFI.shape)
~~~
{: .language-python}

~~~
(131, 14)
(121, 14)
~~~
{: output}

 
### Replace NaN with a value of our choice

The `affect_conflicts` variable answers the question: "Have you been affected by conflicts with other irrigators in the area ?". There are 39 NaN values probably originating from interviewees not answering the question. We might want to have something like 'no_response' for certain analyses. 
We can replace values in a Dataframe with the `replace()` method; to create `NaN` values as the `to_replace` value we require the `numpy` module.

~~~
import numpy as np
df_SAFI.affect_conflicts.replace(to_replace=np.NaN, value='no_response', inplace=True)
df_SAFI
~~~
{: .language-python}

By using `replace()` with `value=np.NaN` we can replace a specific values with NaN:

~~~
df_SAFI.affect_conflicts.replace(to_replace='no_response', value=np.NaN, inplace=True)
df_SAFI
~~~
{: .language-python}


## Aggregating data

Knowing all of the unique values is useful but what is more useful is knowing how many occurrences of each there are. In order to do this we can use the `groupby` method.

Having performed the `groupby()` we can then `describe()` the results. The format is similar to that which we have seen before except that the 'grouped by' variable appears to the left and there is a set of statistics for each unique value of the variable.

~~~
grouped_data = df_SAFI.groupby('respondent_wall_type')
grouped_data.describe()
~~~
{: .language-python}

You can group by more than one variable at a time by providing them as a list.

~~~
grouped_data = df_SAFI.groupby(['respondent_wall_type', 'village'])
grouped_data.describe()
~~~
{: .language-python}

You can also obtain individual statistics if you want.

~~~
no_membrs = df_SAFI.groupby(['village', 'respondent_wall_type'])['no_membrs'].mean()
no_membrs
~~~
{: .language-python}

~~~
village   respondent_wall_type
Chirodzo  burntbricks             8.181818
          muddaub                 5.625000
          sunbricks               6.000000
God       burntbricks             7.473684
          muddaub                 5.466667
          sunbricks               7.888889
Ruaca     burntbricks             7.730769
          cement                  7.000000
          muddaub                 7.000000
          sunbricks               8.285714
Name: no_membrs, dtype: float64
~~~
{: output}

> ## Exercise
>
> 1. Read in the SAFI_clean.csv dataset.
> 2. Get a list of the different `rooms` values.
> 3. Groupby `rooms` and describe the results.
> 4. Remove all rows with NaN values.
> 5. repeat steps 2 & 3 and compare the results.
>
> > ## Solution
> >
> > ~~~
> > # Steps 1 and 2
> > import pandas as pd
> > df_SAFI = pd.read_csv("SAFI_clean.csv")
> > print(df_SAFI.shape)
> > print(pd.unique(df_SAFI['rooms']))
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > # Step 3
> > grouped_data = df_SAFI.groupby('rooms')
> > grouped_data.describe()
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > # steps 4 and 5
> > df_SAFI.dropna(inplace=True)
> > grouped_data = df_SAFI.groupby('rooms')
> > print(df_SAFI.shape)
> > print(pd.unique(df_SAFI['rooms']))
> > grouped_data.describe()
> > ~~~
> > {: .language-python}
> >
<!-- 
> > `E_no_group_count` is related to whether or not farm plots are irrigated or not. It has no obvious connection to farm buildings.
> > By restricting the data to non-irrigated plots we have accidentally? removed one of the roof_types completely.
> > -->
> >
> {: .solution}
{: .challenge}
