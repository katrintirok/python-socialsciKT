---
title: 'Extracting row and columns'
teaching: 15
exercises: 15
questions:
- 'How can I extract specific rows and columns from a Dataframe?'
- 'How can I add or delete columns from a Dataframe?'
- 'How can I find and change missing values in a Dataframe?'
objectives:
- 'Define indexing as it relates to data structures'
- 'Select specific columns from a data frame'
- 'Select specific rows from a data frame based on conditional expressions'
- 'Using indexes to access rows and columns'
- 'Copy a data frame'
- 'Add columns to a data frame'
- 'Analyse datasets having missing/null values'
keypoints:
- 'First key point.'

---

We will continue this episode from where we left off in the last episode. If you have restarted Jupyter or you want to use a new notebook make sure that you import pandas and have read the SAFI_clean.csv dataset into a Dataframe.

~~~
import pandas as pd
df_SAFI = pd.read_csv("SAFI_clean.csv")
~~~
{: .language-python}

### Selecting rows and columns from a pandas Dataframe

If we know which columns we want before we read the data from the file we can tell `read_csv()` to only import those columns by specifying columns either by their index number (starting at 0) as a list to the `usecols` parameter. Alternatively we can also provide a list of column names.  

~~~
df_SAFI_some_cols = pd.read_csv("SAFI_clean.csv", usecols=[0,1,2,12,13,14])
print(df_SAFI_some_cols.shape)
print(df_SAFI_some_cols.columns)
~~~
{: .language-python}

~~~
(131, 6)
Index(['key_ID', 'village', 'interview_date', 'no_meals', 'months_lack_food',
       'instanceID'],
      dtype='object')
~~~
{: .output}

Let us assume for now that we read in the complete file which is now in the Dataframe `df_SAFI`, how can we now refer to specific columns?

There are two ways of doing this using the column names (or labels):

~~~
# Both of these statements are the same
print(df_SAFI['village'])
# and
print(df_SAFI.village)
~~~
{: .language-python}

~~~
0           God
1           God
2           God
3           God
4           God
5           God
6           God
7      Chirodzo
8      Chirodzo
...
~~~
{: .output}

If we are interested in more than one column, the 2nd method above cannot be used. However in the first, although we used a string with the value of `'village'` we could also have provided a list of strings. Remember that lists are enclosed in `[]`.

~~~
print(df_SAFI[['village', 'interview_date', 'no_membrs']])
~~~
{: .language-python}

~~~
     village        interview_date  no_membrs
0         God  2016-11-17T00:00:00Z          3
1         God  2016-11-17T00:00:00Z          7
2         God  2016-11-17T00:00:00Z         10
3         God  2016-11-17T00:00:00Z          7
4         God  2016-11-17T00:00:00Z          7
...
~~~
{: .language-python}
> ## Exercise  
>
> What happens if you:
>
> 1. List the columns you want out of order from the way they appear in the file?
> 2. Put the same column name in twice?
> 3. Put in a non-existing column name? (a.k.a Typo)
>
> > ## Solution
> >
> > ~~~
> > print(df_SAFI[['no_membrs', 'village']])
> > print(df_SAFI[['no_membrs', 'village', 'no_membrs']])
> > print(df_SAFI[['no_members', 'village']])
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

## Filtering by Rows

You can filter the Dataframe by rows by specifying a range in the form of `a:b`. `a` is the first row and `b` is one beyond the last row required.

~~~
# select row with index of 1, 2 and 3 (rows 2, 3 and 4 in the Dataframe)
df_SAFI_some_rows = df_SAFI[1:4]
df_SAFI_some_rows
~~~
{: .language-python}

> ## Exercise
>
> What happens if we ask for a single row instead of a range?
>
> > ## Solution
> >
> > ~~~
> > df_SAFI[1]
> > ~~~
> > {: .language-python}
> >
> > You get an error if you only specify `1`. You need to use `:1` or `0:1` to get the first row returned. The `:` is always required. You can use `:` by itself to return all of the rows.
> >
> {: .solution}
{: .challenge}


## Using criteria to filter rows

It is more likely that you will want to select rows from the Dataframe based on some criteria, such as "all rows where the value for Q2 is -1".


~~~
df_SAFI_some_rows = df_SAFI[(df_SAFI.village == 'Ruaca')]
df_SAFI_some_rows
~~~
{: .language-python}

The criteria can be more complex and isn't limited to a single column's values:

~~~
df_SAFI_some_rows = df_SAFI[ (df_SAFI.village == 'Ruaca') & (df_SAFI.no_membrs > 15)]
df_SAFI_some_rows
~~~
{: .language-python}

We can combine the row selection with column selection:

~~~
df_SAFI_some_rows = df_SAFI[ (df_SAFI.village == 'Ruaca') & (df_SAFI.no_membrs > 15)][['village', 'years_liv','rooms']]
df_SAFI_some_rows
~~~
{: .language-python}

Selecting rows on the row index is of limited use unless you need to select a contiguous range of rows.

There is however another way of selecting rows using the row index:

~~~
df_SAFI_some_rows = df_SAFI.iloc[1:4]
df_SAFI_some_rows
~~~
{: .language-python}

Using the `iloc` method gives the same results as our previous example.

However, now we can specify a single value and more importantly we can use the `range()` function to indicate the records that we want. This can be useful for making pseudo-random selections of rows from across the Dataframe.

~~~
# Select the first row from the Dataframe
df_SAFI_some_rows = df_SAFI.iloc[0]
df_SAFI_some_rows
# select every 100th record from the Dataframe.
df_SAFI_some_rows = df_SAFI.iloc[range(0, len(df_SAFI), 20)]
df_SAFI_some_rows
~~~
{: .language-python}

You can also specify column ranges using the `iloc` method again using the column index numbers:

~~~
# columns 0,1,2 and 3
df_SAFI_some_rows = df_SAFI.iloc[range(0, len(df_SAFI), 20),0:4]
df_SAFI_some_rows
# columns 0,1,2,30 and 60
df_SAFI_some_rows = df_SAFI.iloc[range(0, len(df_SAFI), 20),[0,1,6,12]]
df_SAFI_some_rows
~~~
{: .language-python}

There is also a `loc` method which allows you to use the column names.

~~~
# columns 0,1,6 and 12 using the column names and changing 'iloc' to 'loc'
df_SAFI_some_rows = df_SAFI.loc[range(0, len(df_SAFI), 20),['key_ID', 'village', 'rooms', 'months_lack_food']]
df_SAFI_some_rows
~~~
{: .language-python}

## Sampling

Pandas does have a `sample` method which allows you to extract a sample of the records from the Dataframe.

~~~
df_SAFI.sample(10, replace=False)             # ten records, do not select same record twice (this is the default)
df_SAFI.sample(frac=0.05, random_state=1)     # 5% of records , same records if run again
~~~
{: .language-python}
