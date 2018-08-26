---
title: "Reading data from a file using Pandas"
teaching: 15
exercises: 5
questions:
- "What is Pandas?"
- "How do I read files using Pandas?"
- "What is the difference between reading files using Pandas and other methods of reading files?"

objectives:
- "Explain what a module is and how they are used in Python"
- "Describe what the Python Data Analysis Library (pandas) is"
- "Load the Python Data Analysis Library (pandas)"
- "Use read_csv to read tabular data into Python"

keypoints:
- "pandas is a Python library containing functions and data structures to assist in data analysis"
- "pandas data structures are the Series (like a vector) and the Dataframe (like a table)"
- "the pandas `read_csv` function allows you to read an entire `csv` file into a Dataframe"
---

## What is Pandas?

pandas is a Python library containing a set of functions and specialised data structures that have been designed to help Python programmers to perform data analysis tasks in a structured way.

Most of the things that pandas can do can be done with basic Python, but the collected set of pandas functions and data structure makes the data analysis tasks more consistent in terms of syntax and therefore aids readabilty.

Particular features of pandas that we will be looking at over this and the next couple of episodes include:


* Reading data stored in CSV files (other file formats can be read as well)
* Slicing and subsetting data in Dataframes (tables!)
* Dealing with missing data
* Reshaping data (long -> wide,  wide -> long)
* Inserting and deleting columns from data structures
* Aggregating data using data grouping facilities using the split-apply-combine paradigm
* Joining of datasets (after they have been loaded into Dataframes)


If you are wondering why I write pandas with a lower case 'p' it is because it is the name of the package and Python is case sensitive.


## Importing the pandas library

Importing the pandas library is done in exactly the same way as for any other library. In almost all examples of Python code using the pandas library, it will have been imported and given an alias of `pd`. We will follow the same convention.


~~~
import pandas as pd
~~~
{: .language-python}

## Pandas data structures

There are two main data structures used by pandas, they are the Series and the Dataframe. The Series equates in general to a vector or a list. The Dataframe is equivalent to a table. Each column in a pandas Dataframe is a pandas Series data structure.

We will mainly be looking at the Dataframe.

We can easily create a Pandas Dataframe by reading a .csv file

## Reading a csv file

When we read a csv dataset in base Python we did so by opening the dataset, reading and processing a record at a time and then closing the dataset after we had read the last record. Reading datasets in this way is slow and places all of the responsibility for extracting individual data items of information from the records on the programmer.

The main advantage of this approach, however, is that you only have to store one dataset record in memory at a time. This means that if you have the time, you can process datasets of any size.

In Pandas, csv files are read as complete datasets. You do not have to explicitly open and close the dataset. All of the dataset records are assembled into a Dataframe. If your dataset has column headers in the first record then these can be used as the Dataframe column names. You can explicitly state this in the parameters to the call, but pandas is usually able to infer that there ia a header row and use it automatically.


We are going to read in our SAFI_clean.csv file. We will use the pandas `read_csv` method, which takes a comma as the default character to separate between columns.

~~~
SAFI = pd.read_csv("SAFI_clean.csv")
~~~
{: .language-python}

> ## Exercise
>
> What happens if you specify the tab character as column separator with `sep='\t'`?
>
> > ## Solution
> >
> > ~~~
> > SAFI_oops = pd.read_csv("SAFI_clean.csv", sep = '\t')
> > print(SAFI_oops.shape)
> > print(SAFI_oops)
> > ~~~
> > {: .language-python}
> >
> > If you use a separation character that is not used in the data, then each record will be treated as a single column. So the shape is given as 131 rows (correct) but only one column.
> > When the contents is displayed the only column name is the complete first record.
> >
> {: .solution}
{: .challenge}

##  Getting information about a Dataframe

You can find out the type of the variable `SAFI` by using the `type` function.

~~~
print(type(SAFI))
~~~
{: .language-python}

~~~
<class 'pandas.core.frame.DataFrame'>
~~~
{: .output}

You can see the contents by simply entering the variable name. You can see from the output that it is a tabular format. The column names have been taken from the first record of the file. On the left hand side is a column with no name. The entries here have been provided by pandas and act as an index to reference the individual rows of the Dataframe.

The `read_csv()` function has an `index_col` parameter which you can use to indicate which of the columns in the file you wish to use as the index instead. 
The SAFI dataset has the `instanceID` column which uniquely identifies each row. 

~~~
SAFI = pd.read_csv('SAFI_clean.csv', index_col = 'instanceID')
SAFI.index
~~~
{: .language-python}

~~~
Index(['uuid:ec241f2c-0609-46ed-b5e8-fe575f6cefef',
       'uuid:099de9c9-3e5e-427b-8452-26250e840d6e',
       'uuid:193d7daf-9582-409b-bf09-027dd36f9007',
       'uuid:148d1105-778a-4755-aa71-281eadd4a973',
       'uuid:2c867811-9696-4966-9866-f35c3e97d02d',
       'uuid:daa56c91-c8e3-44c3-a663-af6a49a2ca70',
       'uuid:ae20a58d-56f4-43d7-bafa-e7963d850844',
       'uuid:d6cee930-7be1-4fd9-88c0-82a08f90fb5a',
       'uuid:846103d2-b1db-4055-b502-9cd510bb7b37',
       'uuid:8f4e49bc-da81-4356-ae34-e0d794a23721',
       ...
~~~
{: .output}

Another thing to notice about the display is that it is truncated. By default you will see the first and last 30 rows. For the columns you will always get the first few columns and typically the last few depending on display space.

~~~
print(SAFI)
~~~
{: .language-python}

Similar information can be obtained with `SAFI.head()` But here you are only returned the first 5 rows by default.

~~~
SAFI.head()
~~~
{: .language-python}

> ## Exercise
>
> 1. As well as the `head()` method there is a `tail()` method. What do you think it does? Try it.
> 2. Both methods accept a single numeric parameter. What do you think it does? Try it.
> 
{: .challenge}

You can obtain other basic information about your Dataframe of data with:

~~~
# How many rows?
print(len(SAFI))
# How many rows and columns - returned as a tuple
print(SAFI.shape)
#How many 'cells' in the table
print(SAFI.size)
# What are the column names
print(SAFI.columns)
# what are the data types of the columns?
print(SAFI.dtypes)
~~~
{: .language-python}

~~~
131
(131, 13)
1703
Index(['key_ID', 'village', 'interview_date', 'no_membrs', 'years_liv',
       'respondent_wall_type', 'rooms', 'memb_assoc', 'affect_conflicts',
       'liv_count', 'items_owned', 'no_meals', 'months_lack_food'],
      dtype='object')
key_ID                   int64
village                 object
interview_date          object
no_membrs                int64
years_liv                int64
respondent_wall_type    object
rooms                    int64
memb_assoc              object
affect_conflicts        object
liv_count                int64
items_owned             object
no_meals                 int64
months_lack_food        object
dtype: object
~~~
{: .output}
