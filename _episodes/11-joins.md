---
title: "Joining Pandas Dataframes"
teaching: 25
exercises: 10
questions:
- "How can I join two Dataframes with a common key?"
objectives:
- "Understand why we would want to join Dataframes"
- "Know what is needed for a join to be possible"
- "Understand the different types of joins"
- "Understand what the joined results tell us about our data"
keypoints:
- "You can join pandas Dataframes in much the same way as you join tables in SQL"
- "The `concat()` function can be used to concatenate two Dataframes by adding the rows of one to the other."
- "`concat()` can also combine Dataframes by columns but the `merge()` function is the preferred way"
- "The `merge()` function is equivalent to the SQL JOIN clause. 'left', 'right' and 'inner' joins are all possible."
---

## Joining Dataframes

### Why do we want to do this


There are many occasions when we have related data spread across multiple files.

The data can be related to each other in different ways. How they are related and how completely we can join the data from the datasets will vary.

In this episode we will consider different scenarios and show how we might join the data. We will use csv files and in all cases the first step will be to read the datasets into a pandas Dataframe from where we will do the joining. The csv files we are using are different versions of the SAFI_clean dataset. We need the following csv files - [SAFI_God.csv](./data/SAFI_God.csv), [SAFI_Ruaca.csv](./data/SAFI_Ruaca.csv), [SAFI_numCols.csv](./data/SAFI_numCols.csv), [SAFI_charCols.csv](./data/SAFI_charCols.csv). 

### Scenario 1 - Two data sets containing the same columns but different rows of data

Let's assume the data were saved in separate csv files per village, but all with the same columns. Here we want to add the rows from one Dataframe to the rows of the other Dataframe. In order to do this we can use the `concat()` function.

Let's

~~~
import pandas as pd

SAFI_God = pd.read_csv("SAFI_God.csv")
SAFI_Ruaca = pd.read_csv("SAFI_Ruaca.csv")
~~~
{: .language-python}

Have a quick look at what these Dataframes look like with

~~~
print(SAFI_God.head())
print(SAFI_Ruaca.head())
~~~
{: .language-python}

We can also check the columns of each Dataframe

~~~
print(SAFI_God.columns)
print(SAFI_Ruaca.columns)
~~~
{: .language-python}

~~~
Index(['Unnamed: 0', 'key_ID', 'village', 'interview_date', 'no_membrs',
       'years_liv', 'respondent_wall_type', 'rooms', 'memb_assoc',
       'affect_conflicts', 'liv_count', 'items_owned', 'no_meals',
       'months_lack_food', 'instanceID'],
      dtype='object')
      
Index(['Unnamed: 0', 'key_ID', 'village', 'interview_date', 'no_membrs',
       'years_liv', 'respondent_wall_type', 'rooms', 'memb_assoc',
       'affect_conflicts', 'liv_count', 'items_owned', 'no_meals',
       'months_lack_food', 'instanceID'],
      dtype='object')
~~~
{: output}

The `concat()` function appends the rows from the two Dataframes to create the `SAFI_allRows` Dataframe. When you list this out you can see that all of the data rows are there, however there is a problem with the `index`.

~~~
SAFI_allRows = pd.concat([SAFI_God, SAFI_Ruaca])
SAFI_allRows
~~~
{: .language-python}

We didn't explicitly set an index for any of the Dataframes we have used. For `SAFI_God` and `SAFI_Ruaca` default indexes would have been created by pandas. When we concatenated the Dataframes the indexes were also concatenated resulting in duplicate entries.

This is really only a problem if you need to access a row by its index. We can fix the problem with the following code.

~~~
SAFI_allRows = SAFI_concat.reset_index(drop = True)
SAFI_allRows
~~~
{: .language-python}

What if the columns in the Dataframes are not the same?

~~~
SAFI_God = pd.read_csv('SAFI_God.csv', usecols=range(0,4))
print(SAFI_God.columns)
SAFI_Ruaca = pd.read_csv('SAFI_Ruaca.csv', range(1,5))
SAFI_allRows = pd.concat([SAFI_God, SAFI_Ruaca])
SAFI_allRows
~~~
{: .language-python}

In this case `SAFI_God` has the columns `key_ID`, `village`, `interview_date`, `no_membrs` and `SAFI_Ruaca` has the columns `village`, `interview_date`, `no_membrs`, `years_liv`. When they are concatenated, the resulting Dataframe has a column for `key_ID` and `years_liv`. For the rows corresponding to `SAFI_God` the values in the `years_liv` column are missing and denoted by `NaN`. The same applies to `key_ID` for the `SAFI_Ruaca` rows.

(note: the `SAFI_concat` Dataframe is returned with the columns sorted in alphabetical order)


### Scenario 2 - Adding the columns from one Dataframe to those of another Dataframe

~~~
SAFI_num = pd.read_csv('SAFI_numCols.csv')
SAFI_char = pd.read_csv('SAFI_charCols.csv')
SAFI_allCols = pd.concat([SAFI_numCols.csv, SAFI_charCols.csv], axis = 1)
SAFI_allCols
~~~
{: .language-python}

We use the `axis=1` parameter to indicate that it is the columns that need to be joined together. Notice that the `instanceID` column appears twice, because it was a column in each dataset. This is not particularly desirable, but also not necessarily a problem. However there are better ways of combining columns from two Dataframes which avoid this problem.

### Scenario 3 - Using merge to join columns

We can join columns from two Dataframes using the `merge()` function. This is similar to the SQL 'join' functionality.

A detailed discussion of different join types is given in the [SQL lesson](./episodes/sql...).

In order to `merge` the Dataframes we need to identify a column common to both of them.

~~~
SAFI_join = pd.merge(SAFI_num, SAFI_char, how = 'inner')
SAFI_join
~~~
{: .language-python}

In fact if there is only one column with the same name in each Dataframe, it will be assumed to be the one you want to join on. In this example the `instanceID` column

Leaving the join column to default in this way is not best practice. It is better to explicitly name the column using the `on` parameter.

~~~
SAFI_join = pd.merge(SAFI_num, SAFI_char, how = 'inner', on = 'instanceID')
~~~
{: .language-python}

In many circumstances, the column names that you wish to join on are not the same in both Dataframes, in which case you can use the `left_on` and `right_on` parameters to specify them separately.

~~~
SAFI_join = pd.merge(SAFI_num, SAFI_char, how = 'inner', left_on = 'instanceID', right_on = 'instanceID')
~~~
{: .language-python}

You specify the type of join you want using the `how` parameter. The default is the `inner` join which returns the columns from both tables where the `key` or common column values match in both Dataframes.

The possible values of the `how` parameter are shown in the picture below (taken from the Pandas documentation)

![pandas_join_types](../fig/pandas_join_types.png)

The different join types behave in the same way as they do in SQL. In Python/pandas, any missing values are shown as `NaN`


> ## Exercises
>
> 1. Examine the contents of the `SAFI_God` and `SAFI_Ruaca` csv files using Excel or equivalent.
> 2. Using the `SAFI_God` and `SAFI_Ruaca` csv files, create a Dataframe which is the result of an outer join using the `key_ID` column to join on.
> 3. What do you notice about the column names in the new Dataframe?
> 4. Using `shift`+`tab` in Jupyter examine the possible parameters for the `merge()` function.
> 5. re-write the code so that the column names which are common to both files have suffixes indicating the filename from which they come
> 6. If you add the parameter `indicator=True`, what additional information is provided in the resulting Dataframe?
>
> > ## Solution
> >
> > ~~~
> > SAFI_God = pd.read_csv("SAFI_God.csv")
> > SAFI_Ruaca = pd.read_csv("SAFI_Ruaca.csv")
> > SAFI_join = pd.merge(SAFI_God, SAFI_Ruaca, how='outer', on = 'key_ID')
> > SAFI_join
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > SAFI_join = pd.merge(SAFI_God, SAFI_Ruaca, how='outer', on = 'Id',suffixes=('_God', '_Ruaca'), indicator = True)
> > SAFI_join
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}
