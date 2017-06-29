
# Using Python for Data Analysis

This first session of ResSearch will be focused on the exploration, cleaning and basic visualisation of a prepared set of sample data - the [pedestrian counts](https://github.com/resbaz/pedestriancounts) at various locations around Melbourne. If you have not already downloaded this data, please do so now, and place this data file in the same folder as this jupyter notebook. 

## Learning objectives

Thorughout this session we're going to be teaching you a range of tools and skills related to cleaning, manipulation and visualising large datasets. Using the pandas package, we can read in and manipulate large spreadsheets of data, and matplotlib lets you visualise these datasets in a useable, customisable format.

The three sections I'll be taking you through today are:

- Data examination
- Dataframe manipulation
- Asking a research question
- Plotting your data with pyplot


## Setting Up

First, we need to import Python's *pandas*, *matplotlib* and *numpy* packages, and then use inline plotting "magic" command so that all plots generated will appear within this notebook instead of in a new browser tab.


```python
# installing packages
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from mpl_toolkits.basemap import Basemap
```


```python
# inline plotting magic command
# allows figures to be generated inside the notebook, instead of in a separate window
%pylab inline
```

## Reading in Data

The first step to any data exploration and manipulation is to open your data within your program. We are going to do this using the **pandas** package, which reads in spread-sheet style data and converts them into *dataframes*. 

These dataframes work with rows and columns, like a spreadsheet, except that all data within a single column has to be the same data type. 

For example, imagine you had a spreadsheet containing two columns - "labels" and "numbers", and that the rows in the "labels" column contains either a text or number sequence. Because you cannot turn text into a number, every single row in that "labels" column would need to be a string (text) type. Similarly, if some (but not all) of the rows in the "numbers" column contained decimals, **all** of the rows within this column would need to be of a decimal (float) data type.

To read in a comma-separated file, or \*.csv, you can use the pandas function read_csv()


```python
# Reading in a *.csv file
pdsn = pd.read_csv("pedestriancounts_melbourne.csv",
                   # Because the file is missing column names:
                   header = None, names = ["Timestamp","Day", "Hour","SensorID","Location","Counts"])

```

You can also open a variety of other file types using the "reader" functions found in this [IO tools documentation](http://pandas.pydata.org/pandas-docs/version/0.20/io.html "Pandas IO tools"). 

This includes file types such as excel (\*.xlsx) files, and text (\*.txt) files. You can use the parameters within these functions to specify file or data attributes such as column separators, whether there's column/row names, and even specifying your own column names.

## Examining your Data

- `head()`, `tail()`
- `columns`
- `df.Column` vs `df['Column']`
- `dtypes`
- `describe()`
- `shape`; `shape[0]` vs. `shape[1]`
- `iloc` vs `loc`

One of the first steps in exploring your data is to see what it looks like, what data types are present, and how many rows/columns there are.

For example, `df.head()` and `df.tail()` show you the first/last 5 rows of your dataframe selection


```python
#Shows the first 5 rows of your input
pdsn.head()
```


```python
#Shows the last 5 rows of your input
pdsn.tail()
```

The `shape` function gives you the dimensions of your data, in the form (rows, columns).


```python
# This returns a tuple (or linked pairs) of the number of rows and columns in your dataframe.
pdsn.shape
```


```python
# Calling the first or second element of the tuple can give you either the rows or the columns
#Gives you the rows (remember, 0 indexing!)
pdsn.shape[0]

#Gives you the columns
pdsn.shape[1]
```

You can also use `df.columns` to examine the column names.


```python
# What are the column names in your dataframe?
pdsn.columns
```

To select a particular column in your dataframe, you can use one of two options:

1) Calling the column as an "attribute"
    - `df.columnName`

2) Subsetting the dataframe
    - `df['columnName']`

The first option is useful for some functions, but the second form is essential if you want to call more than column at once. You do this by inserting a list, [], of column names, instead a single column.

For example: `df[["Column1", "Column2", ... , etc]]`

Consider this toy example of a dataframe, s.


```python
s = pd.DataFrame(
                 {'A':['a', 'b', 'c','d','e','f'], 
                  'B':[54, 67, 89, 100, None, 64],
                  'C': np.random.randn(6),
                  'D': [2.6,None, 8.0, 9.4, 3.3, None]},
                 index=[49, 48, 47, 1, 2, 3])
s
```


```python
# Try subsetting the first two columns
s[["A","B"]]
```


```python
# What happens if you change the order of the columns around?
s[["B","C","A"]]
```


```python
# What about a column that doesnt exist?
s["b"]

```

### Data Types

Knowing what type of data is in your dataframe is extremely important, as it limits what functions you can and can't do on that column. It's useless to try and do string manipulations on an integer, or try to find the sum of a column of names.

We can quickly view the data in our table by using the `dtypes` function. 

Within pandas, str = "object", int = "int64", and float = "float64"


```python
# What data types are in each column?
pdsn.dtypes
```

Knowing this, you can now examine a quick summary of your data to see what you're working with with `describe()`


```python
pdsn.describe()
```

Unfortunately describe will preferentially only output a summary of any numeric type columns, such as Counts, the SensorID and the Hour. To examine the string columns, you need to use the `include = 'all'` parameter.

As all of this data is output in the same table, the rows which aren't applicable to that data type are filled that NaN's


```python
#Looking at all of the columns
pdsn.describe(include = 'all')
```

We can also find specific statistics for each of these columns by using `max()`, `min()`, `count()`, `std()`, and `sum()`. 

Just remember though that many of these functions rely on numeric data types, and will cause errors if used on a str type. Calling `sum()` on a string however will concatentate those strings.


```python
#Try finding the maximum of the Location (a string) column
pdsn.Location.max()
```


```python
# what about the Standard Deviation (std) of Counts (numeric)?
pdsn.Counts.std()
```

Other useful functions include `mean()` and `unique()`.

Where `mean()` takes the average of numeric data, `unique()` works on both numeric and string data, and gives you a list of all of the unique values within a particular column.



```python
# Unique works on numeric data
pdsn.Hour.unique()
```


```python
# As well as on str data
pdsn.Day.unique()
```

As this returns a numpy array though, rather than a pandas dataframe or series object, you can use either len() or .size to find the number of unique values for that column


```python
# Using the numpy "size" function
pdsn.Day.unique().size

# Using the len() function
len(pdsn.Day.unique())
```

### Dataframe slicing
Sometimes you need to examine specific rows and columns in the middle of your data though, which aren't covered by `head()` or `tail()`. Instead, you can use `df.iloc['row#','column#']` for position based dataframe navigation, `df.loc[]` for label based navigation, or simple index slicing `df[rowNumbers]`

Slicing works similarly to how you might slice a string, or a list. You simply call the indexes of the rows you want from the dataframe:

`df[rowNumbers]`


```python
pdsn.Location[5:10] # Gives rows 5, 6, 7, 8, 9
```


```python
pdsn[["Location","Counts"]][:8] #gives rows 0 through 7
```

You can also use `loc` and `iloc` to slice rows and columns.

`iloc` is positional based, so only takes integer values that correlate with the row and column numbers you want to subset

`loc` is label based, and takes the row and column **labels** as inputs


```python
#Using iloc to get rows 0, 1 and 2
s.iloc[:3]
```


```python
# Using loc to get row labels 1, 2 and 3
s.loc[1:3]
```

If you only enter one set of values into `loc` and `iloc`, they will return the values for every column in your dataset.

By using a second integer though, you can choose which rows and columns you specifically want to subset. The first value corresponds to the row, and the second to the columns, or rows x columns. You can also think of this with the moniker *"Roman Catholic"*


```python
# Using loc to get row labels 1, 2 and 3 for Columns A and B
s.loc[1:3,["A","B"]]
```


```python
# Using iloc to get rows 0, 1 and 2 for columns 0 and 1.
s.iloc[:3,:2]
```

The differences between `loc` and `iloc` can seem minor, but they're very important, and which one you should use depends on what your needs are at the time.

`iloc` is based on dataframe position, so calling `iloc[:3]` would give you rows 0 through 3. 

`loc` however is based on the index label, so if you were to call `df.loc[:3]`, it would give you all rows UP TO the row labelled as index 3.

While the indexes are in order and all present, this isn't an issue. Consider what happens when the indexes are out of order though, with our dataframe 's'


```python
# Can see that this only takes the first 2 rows
s.iloc[:2]
```


```python
# Whereas this takes all rows UP TO index label 2
s.loc[:2]
```

Since `loc` is based on labels, if you try to subset a row or column label that doesn't exist, even if it corresponds to a positional row, python will throw you an error


```python
# For example, as Python has -indexing, the first row of the dataframe would have position = 0
# If we try to subset using this position inside loc however, it will throw an error as there is no row labelled as '0'
s.loc[0]
```

Due to this, it's important that you carefully consider which tool is appropriate for your needs


```python
# Find the 11th through 20th (inclusive) rows  for the SensorId, Location and Counts columns in pdsn using loc, iloc and slicing
#iloc
pdsn.iloc[10:20,3:]

```


```python
# loc
pdsn.loc[10:19,["SensorID", "Location", "Counts"]]

```


```python
#slicing
pdsn[["SensorID","Location","Counts"]][10:20]

```

#### Subsetting with conditionals
You can also subset using conditional statements, such as ==, !=, >, <, etc.

For example, if I want to find all of the rows in s where B > 60, I would type:


```python
# All columns/rows where the value in column B > 60
s[s.B > 60]
```

Just as with lists and for loops, etc, you can also combine these conditionals using & {and} , or | {or}


```python
#Find the rows where C < 0.5 AND B != 100
s[(s.B != 100) & (s.C < 0.5)]
```


```python
#You can also get a list of the row indexes for your subset
list(s[s.B > 60].index)
```

Similarly, you can subset using boolean lists


```python
# a boolean list. The 1st, 3rd and 5th values are True.
na = [True,False,True,False,True,False]

s[na] #subsets the 1st, 3rd and 5th rows
```

##### Challenge 1

Find how many rows belong to the sensor at Lygon St (West) between the hours of 12am and 2am?

*Hint 1: an entry of "3" in the Hour column means that the pedestrian counts have been monitored from 3am to 4am*

*Hint 2: remember that you find the length of a list using the "len()" function*


```python
# Challenge 1: how many entries in this dataframe are for the sensor at Lygon St (West) between the hours of 0am and 2am?


```


```python

```

## Manipulating Your Dataframe

- Finding NaNs: 
    - `count()` 
    - Using booleans: `isnull()`
    - Chaining: `isnull().any()`, `isnull().sum()`
    - Subsetting: `df[df['Column'].isnull()]`
- Data type conversion
- Using the "timestamp" data type
- Adding and deleting columns/rows
- `groupby()`

### Finding Null Data

Earlier, we explored the use of `shape` to determine the dimensions of our dataframe. Another way to do this is with `count`.


```python
pdsn.count()
```

But you can see that not all of the columns have the same length. This is because count sums the number of rows within that column that contain values - so missing data, or NaNs, will cause the count to be smaller.

You can check which columns inside our test dataframe, s, contains null values by using `isnull()`.



```python
s.isnull()
```

Good thing we didn't do that on our pedestrian dataframe!

`isnull()` returns a boolean list for each entry in every column - which is useful for subsetting, but not ideal for a quick summary.

By chaining the `isnull()` and `any()` functions together, like `df.isnull().any()`, we can quickly see if a column contains any null values.


```python
#Check for nulls in each column
pdsn.isnull().any()
```

We can also chain `isnull()` and `sum()` to find an exact count of how many NaNs are in each column as well, like `df.isnull().sum()`


```python
# Finding the number of NaNs

```

### Resolving Null Data

There are 2 ways to resolve Null data - replace it with new data, or delete them.

For the purposes of this event, we are going to teach you how to delete them (though another section below this will teach you to replace it, for the purposes of your own data investigations).

Firstly, let's view the columns where SensorID, Location and Counts have null values.

We can do this using the `isnull()` function. As mentioned before, we can subset with a boolean list, which is what `isnull()` returns. Where the test evaluates to 'True', the row is output from the dataframe.


```python
#Show the rows where SensorID has null values
pdsn[pdsn['SensorID'].isnull()]
```


```python
#Show the rows where Location has null values
pdsn[pdsn['Location'].isnull()]
```


```python
#Show the rows where Counts has null values
pdsn[pdsn['Counts'].isnull()]
```

You can also show *every* row with null values at once using the isnull().any() chained operation. any() contains the optional parameter "axis", which allows you to choose whether it operates on the rows or the columns. By default axis = 0, which gives you columns, but setting axis = 1 checks over the rows instead


```python
pdsn[pdsn.isnull().any(axis = 1)] # nulls in rows
```

To get rid of these null values, we need to delete them from each column in separate operations. However, this can easily done by subsetting with the `notnull()` function.

`notnull()` performs just as `isnull()` does, and returns a boolean (True/False) list for every entry in that column based on whether that value is null.


```python
pdsn = pdsn[pdsn.SensorID.notnull()] #removing SensorID nulls
pdsn.shape
```


```python
pdsn = pdsn[pdsn.Location.notnull()] #removing Location nulls
pdsn.shape
```


```python
pdsn = pdsn[pdsn.Counts.notnull()] #removing Counts nulls
pdsn.shape
```

Conversely, if you have a list of the row indexes you wish to remove, you can also use the `drop` function

`drop df.[indexes]`

If we examine the `tail()` of the altered dataframe though you can see that the row indexes are still the same from before the deletions though. We can fix this using `reset_index()`.

Remember that while the indexes aren't that important if you're using `iloc`, but if you're using `loc` it's important that these index labels are correct.


```python
pdsn.tail()
```


```python
pdsn = pdsn.reset_index()
```

That ends the "deleting nulls" section of data manipulation. To continue with the exercises, now go to "Converting Data Types, Dataframe Manipulation". 

This next text section will show instead how you might replace your null values with useable data.

### Replacing Nulls - Personal reading section
###### Where one column is dependant on another
In the case of SensorID and Location, each of these values are dependent on the other. Where the SensorID is 13, the Location will always be 'Flagstaff Station', etc. In these cases, you can use previous values from your table - where you DON'T have nulls, to replacce your missing data.

Where there are only a few points, you might choose to do this manually. This can be done using the `set_value(index,"Column", value)` function. For the index parameter, you can pass it either a single index, or a list, [], of indexes to be replaced. 

For example, if you wanted to replace the singular null value for SensorID at index 243, you would type:

`pdsn.set_value(243, "SensorID", 9)`

Say that you noticed a repeated spelling error in some of the labels, and wanted to fix them, or change something for more clarity. For example, if you wanted to replace all instance of "Flagstaff Station" in the Location column with "Flagstaff", you might use:

`pdsn.set_value(list(pdsn[pdsn.SensorID == 9].index), "Location", "Flagstaff")`

###### Replacing all null values with a single value
In the Counts column for example, you can see that there are 14 missing values. You might choose to just replace all of these with 0. You can do this using the `fillna()` function, and specifying the value you want to replace it with.

`pdsn['Counts'] = pdsn['Counts'].fillna(value=0)`










There are a great many other options when replacing nulls, such as using the mean of previous values, or even "imputing" your data based on probabalistic models.

There are many resources online on how to do these, and I would greatly encourage you to go and explore these. Generally speaking, the best way to find something is to throw as many key words into Google as you can, and something relevant will usually pop up. While it's unlikely that a single question on Stack Overflow will tell you everything you need to know for your specific problem, reading a few related questions will usually help you figure out your issue.

### Converting Data Types, Dataframe Manipulation

While we currently have a "Timestamp" column, if you go into dtypes you can see that pandas has read this in as string, rather than as a datetime type. If we left it as a string, to get the Month, Day or Year we would need to perform a series of string manipulations every time we wanted to access these values - a costly, redundant and time consuming exercise if trying to use the whole dataframe. 

Instead, we can convert this column to a datetime data type, using the `to_datetime()` function.


```python
pdsn['Timestamp'] = pd.to_datetime(pdsn['Timestamp'])
```


```python
pdsn.dtypes
```


```python
# Adding a Column with the Day
pdsn['Date'] = pd.DatetimeIndex(pdsn['Timestamp']).day

# Adding a Column with the Month (from 1-12)
pdsn['Month'] = pd.DatetimeIndex(pdsn['Timestamp']).month

# Adding a Column with the Year
pdsn['year'] = pd.DatetimeIndex(pdsn['Timestamp']).year
```


```python
pdsn.head()
```

Oops! We accidentally named the 'Year' column with a lowercase. While not necessarily a bad thing, it's good for column names to remain consistent, to reduce confusion and errors while programming.

Lets add in another column "Year", and get rid of the incorrectly named one.


```python
#Adding a new "Year" column
pdsn['Year'] = pd.DatetimeIndex(pdsn['Timestamp']).year
```


```python
pdsn.head()
```


```python
#Deleting the "year" column
    # MUST use the df[column] format, df.column will throw an error
del pdsn['year']
```


```python
pdsn.columns
```

You can also convert other columns by using the df[column].astype(<datatype>) function. This includes 'str', 'int64' and 'float64' data types, amongst others.

You must be careful **not to use `astype()` on a column with null values**. `astype()` does not preserve the null values, and makes resolving them later more difficult.

Let's use our dataframe, s, in an example of converting types


```python
# No assignment means that this isn't a permanent change to the dataframe
s.C.astype(str)
```


```python
s.dtypes
```

#### Challenge 2
Convert the D column in s to a string type, and then shown what the resulting values at the previously null indexes are

*Hint: you can get the index of the current null values before the conversion*


```python
# Challenge 2 - Convert the data types and show the new values and their data type

```


```python

```


```python

```

### Grouping data

It's often useful to be able to group the data within a column according to the data in another column. 

For example, you might wish to take the mean pedestrian counts for each month, or each year. We could do this is a complicated and time consuming way where you subset the data for each month, and then take the mean of the Counts column...or we could just use the `groupby` function. `groupby` outputs a reformatted version of your data where all values associated with your "grouping factor" are taken together. You can then perform mathematical and statistical tests on this grouped output, and the tests will be performed within each of the "groups" you defined.

For example, say that we wanted to find the mean pedestrian counts seen in each month:


```python
pdsn.groupby(by=["Month"]).mean()
```

Unfortunately these tests are non-discriminatory, and will take the `mean()` of each numeric data column. 

To get the columns you want, such as Counts, you need to specify this in the chained command:


```python
# the mean pedestrian counts within each month
pdsn.groupby(by=["Month"]).mean().Counts
```


```python
# Can also specificy multiple columns to be output
    # the mean "Hour" and pedestrian counts within each month
pdsn.groupby(by=["Month"]).mean()[["Counts","Hour"]]
```

You can also choose to group over multiple factors, such as by Month and Year together, by specifying a list for the `by` parameter within `groupby()`. The order of the values in this list matters, as `groupby` will first group your data based on the first factor, and THEN the second factor.


```python
# Grouping by Year, then by month
#The average number of pedestrians 
pdsn.groupby(by=["Year","Month"]).mean().Counts
```


```python
# the order of the groupby factors matter
pdsn.groupby(by=["Month","Year"]).mean().Counts
```

By chaining more commands together you can also find the maximum, minimum, etc, values for your groups


```python
# Minimum
pdsn.groupby(by=["Month"]).Counts.sum().min()
```

But which month is this associated with?


```python
# Finding the Month associated with the minimum value
monMin = pdsn.groupby(by=["Month"]).Counts.sum().min()

pdsn.groupby(by=["Month"]).Counts.sum()[pdsn.groupby(by=["Month"]).Counts.sum() == monMin]
```

#### Optional Challenge
Which sensor has the greatest average Pedestrian traffic? 


```python
#Optional Challenge Code


```


```python

```


```python

```


```python

```


```python

```

### Asking a Research Question

- Asking your question
- Plotting your data in pyplot
    - line plots
    - bar graphs
    - histograms
    - scatter plots
- Plotting location data on a map (Basemap)


Now that we've cleaned all of our data, and lerned how to view it according to different factors, it's time to think about what kind of questions we might be able to answer with it.

A question that we might ask, for example, is "Does Summer have a greater amount of pedestrian traffic than Winter? Does this change across years?"

To examine this, we would first need to subset the dataframe for the appropriate calendar months:


```python
seasons = pdsn.loc[pdsn["Month"].isin([1,2,12,6,7,8])]
```


```python
seasons.shape
```


```python
seasons.head()
```


```python
seasons.tail()
```


```python
seasons = seasons.reset_index()

seasons.tail()
```


```python
seasons.groupby(by=["Year","Month"]).sum().Counts
```

We can also add another column, "Season", based on the month column


```python
# New Column                  If Month in this list        True =   False = 
seasons["Season"] = np.where(seasons['Month'].isin([1,2,12]), 'Summer', 'Winter')

# Check it's been added in
seasons.Season.unique()
```

Remember to check that your values have been assigned properly


```python
#Checking Summer
seasons[(seasons.Season == "Summer") & (seasons.Month == 12)].head()
```


```python
#Checking Winter
seasons[(seasons.Season == "Winter")].head()
```

Now we have another column we can group on!

### Plotting Your Data

Now that we have a research question to explore, it's time to look at how we might explore that visually.

This section will teach you the basics of plotting within matplotlib. This is hardly comprehensive, and there's a wide array of customisability and control with matplotlib and plotly. Feel free to delve into some of user guides and tutorials and questions available on Google and Stack Overflow to learn more about these tools and how to make them work for you.

A reasonably good matplotlib.pyplot tutorial can be found at https://matplotlib.org/users/pyplot_tutorial.html

The [matplotlib API](https://matplotlib.org/devdocs/api/_as_gen/matplotlib.pyplot.html) contains all of the functions you can call within matplotlib, along with detailed information about how you can use them and their parameters

A handy "quick guide" to the different kinds of plotting functions within pyplot can also be found at the [pyplot API](https://matplotlib.org/api/pyplot_api.html)

Let's begin by trying to plot the seasons data


```python
seasons.plot() #Plots everything on top of each other
                 # Not particularly informative, eh?
```

But that's not particularly informative, and much of the graph is completely unreadable.

Matplotlib.pyplot has great customisability, so let's play around with trying to plot different types of graphs with our data

#### Line Plot

Line plots are great for plotting series data, usually a time series of some description.

Line plots are the default plot drawn when calling `.plot()`.

Within the plot function you can also specify the Figure title and the figure size


```python
# Line plot
pdsn.groupby(by = ['Year', 'Month']).mean().Counts.plot( # Drawing a pot from the grouped Year, Month data
                                                figsize = (8,6), # The figure size
                                                title = 'Mean Pedestrian Counts per Month, per Year') #Figure Title

#specifying the x-axis label
plt.xlabel('Pedestrian Counts per Year, Month')

# the y-axis label
plt.ylabel('Counts (mean)')

#Rotating the x-axis labels
plt.xticks(rotation=90)

#If you want to SAVE your plot, make sure you do it BEFORE you show it in the notebook
plt.savefig('PedestrianHistogram.png', dpi=300)

#Generating an image
plt.show()

```

#### Bar Chart
Bar charts are great for categorical data, like examining "Seasons", for example.

Bar charts aren't generally recommended for time series data, such as plotting counts/hour, or mean counts per month, as these are more suited for line plots.


```python
# You can still use the plot function, but specify which columns and plot type you would like to use
seasons.groupby(by = ['Season']).mean().Counts.plot(kind = 'barh',figsize = (8, 6)) 

#Figure title
title("Mean Pedestrian Counts per Season")

# x-axis label
plt.xlabel('Mean Counts')

#y-axis label
plt.ylabel('Season')

#If you want to SAVE your plot, make sure you do it BEFORE you show it in the notebook
plt.savefig('SeasonsBarchart.png', dpi=300)

#Generating an image
plt.show()
```

#### Boxplot
Box plots are good for viewing the spread of data within certain categories. These can be useful to measure whether two conditions could be said to be approximately equal, for example.


```python
# The data and plot to use                                      
plt.fig = seasons[["Season", "Counts"]].mean().boxplot(by = 'Season', # Separating the data according to season
                                                              figsize=(8, 6)) #Specifying the figure size

# x-axis label
plt.xlabel('Season')

#y-axis label
plt.ylabel('Mean Counts')

#figure title
title("Pedestrian Counts per Season")

#If you want to SAVE your plot, make sure you do it BEFORE you show it in the notebook
#plt.savefig('SeasonsBoxplot.png', dpi=300)

#Generating an image
plt.show()
```

#### Histograms

Histograms let you observe the distribution of your (continuous) data, which means that it isn't suitable for all data types.

One thing we could use to observe, however, are how the average number of pedestrians per month is distributed


```python
# Create a figure of given size
fig = plt.figure(figsize=(10,8))

#plt.hist takes the data you want to plot
plt.hist(pdsn.groupby(by = "Month").mean().Counts, bins = 12, normed = False, title = 'Pedestrian Counts per Month')

#specifying the x-axis label
plt.xlabel('Mean Pedestrian Counts')

# the y-axis label
plt.ylabel('Frequency')

#If you want to SAVE your plot, make sure you do it BEFORE you show it in the notebook
plt.savefig('PedestrianHistogram.png', dpi=300)

#Generating an image
plt.show()

```

#### Scatter Plot

Scatter plots also allow you observe the relationship between two conditions within your data. This works best when you have two continuous variables. With the use of colours and shapes, you can also observe how certain conditions cluster throughout this relationship.


```python
pdsn.plot(kind='scatter', x = "Counts", y = "Month")

plt.show()
```

Unfortunately we don't actually have two continuous variables within this dataset, so our scatter plot sorts according to their discrete categories.

Instead, let's try reading in the metadata for the sensor location, in the "pedestrian_sensor_locations.csv" file.

Tihs file contains latitude and longitude data, and would show a more ideal scatterplot projection.


```python
sensorLt = pd.read_csv("pedestrian_sensor_locations.csv")
sensorLt.head()
```


```python
(pdsn.groupby("Location").mean().Counts)/(pdsn.groupby("Location").mean().Counts.max())
```


```python
# Setting up a list of "area" values, scaled according to the mean pedestrian counts for each location
area = (pdsn.groupby("Location").mean().Counts)/(pdsn.groupby("Location").mean().Counts.max()) * 100

sensorLt.plot(kind='scatter', x = "Latitude", y = "Longitude", s = area)

plt.show()
```


```python

```

#### Longitude and Latitude Plotting using Basemap

Plotting UK Median House prices for 2015 onto a map of UK based on postcode:

http://www.datadependence.com/2016/06/creating-map-visualisations-in-python/

Another tutorial:

https://peak5390.wordpress.com/2012/12/08/matplotlib-basemap-tutorial-plotting-points-on-a-simple-map/


```python
#Setting the size of the figure
fig, ax = plt.subplots(figsize=(10,20))

# Creating the basic area of the map
m = Basemap(resolution='c', # c, l, i, h, f or None - the image resolution, ranging from crude (c) to full (f)
            projection='merc', #the type of map used. This is a mercator projection
            lat_0=54.5, lon_0=-4.36, # The latitude and the longitude to centre the map on
            llcrnrlon=-6., llcrnrlat= 49.5, # The latitude and the longitude of the upper RH corner
            urcrnrlon=2., urcrnrlat=55.2) # The latitude and the longitude of the lower LH corner
# Worth nothing that the latitude and the longitude must be between -90 and 90.

# Setting up the colour of the map
m.drawmapboundary(fill_color='#46bcec') 
m.fillcontinents(color='#f2f2f2',lake_color='#46bcec') #land mass and water colour
m.drawcoastlines() # boundary lines

#making a function that takes a position and then plots the number of new houses 
    #associated with that position onto our map represented by the size of the point
def plot_area(pos):
    count = new_areas.loc[new_areas.pos == pos]['count']
    x, y = m(pos[1], pos[0])
    size = (count/1000) ** 2 + 3
    m.plot(x, y, 'o', markersize=size, color='#444444', alpha=0.8)

    #applying this function to the data
new_areas.pos.apply(plot_area)


#reading in a shapefile of the UK postcode boundaries
m.readshapefile('data/uk_postcode_bounds/Areas', 'areas')


# Using data to colour in areas 

# higher the number of new houses in an area, the darker the colour of the area. 
# We’ll also add a colour bar in to give people looking at the map an idea of what kind of number a colour represents

df_poly = pd.DataFrame({ #Creating a dataframe of these colour values
        'shapes': [Polygon(np.array(shape), True) for shape in m.areas], #Using the shapefile previously imported
        'area': [area['name'] for area in m.areas_info]
    })
df_poly = df_poly.merge(new_areas, on='area', how='left')


#Actually colouring in the areas 
cmap = plt.get_cmap('Oranges')   
pc = PatchCollection(df_poly.shapes, zorder=2)
norm = Normalize()
 
pc.set_facecolor(cmap(norm(df_poly['count'].fillna(0).values)))
ax.add_collection(pc)

#Adding a colour bar
mapper = matplotlib.cm.ScalarMappable(norm=norm, cmap=cmap)

mapper.set_array(df_poly['count'])
plt.colorbar(mapper, shrink=0.4)


```

### Challenge 3

Design your own research question, and interrogate and plot your data to investigate your question


```python

```


```python

```


```python

```


```python

```

## Summary

This session has taken you through the beginner's guide of how to visualise, clean and investigate your data, and given you the basic tools to answer your own research questions about your data. 

Python is a very versatile tool, and can go much further than what we've shown you today. Many packages are open source and being developed for a range of purpoess all the time. Scipy allows you to perform basic math and statistical tests for example, there are a host of packages related to investigating biological data.

Using a programming language allows you to investigate much larger data than you can do by hand or by eye, and the customisability of the plotting tools makes it ideal for generating useful and professional images  ideal for publication.

```python

```
