Homework 1
==========

Ex1
---
`data.head()` returns a DataFrame containing the first 5 elements of the `data` DataFrame.

`data.tail(3)` returns a DataFrame containing the last 3 elements of the `data` DataFrame.

`data.shape` returns the shape of the `data` DataFrame object which is a tuple containing 
the number of rows as the first element and the number of columns as the second element. 
In this case it is `(8, 3)`.

Ex2
---
The required index is the conjunction of two indices: 1. The index for the elements which 
their name ends with 'bacteria', 2. The index for the elements which have a value of more
than 1000.
```python
idx = [name.endswith('bacteria') for name in data.phylum] and [v>1000 for v in data.value]
data[idx]
```

Ex3
---
We first use the `isin` method to construct the index for the given condition. Then, we use
this index in order to retrieve the satisfying players which are 15. The corresponding code
is as follows:

```python
teamidx=baseball['team'].isin(['LAN', 'SFN'])
baseball[teamidx]
```

Ex4
---
First, we have to define a function which implements the given formula for the base percentage.
Then, we apply this function on the series followed by sorting the serie based on this value.

```python
def base_percentage(x) :
   a = x['h'] + x['bb'] + x['hbp']
   b = x['ab'] + x['bb'] + x['hbp'] + x['sf'] + 1e-6
   return a/b

baseball.apply(base_percentage, axis=1).round(3).sort_values()
```

Ex4.5
---
For `b2` we have to construct the tuple `('b', 2)` and for `'Colorado'` we have to put itself
as the input to `loc`.

```python
frame.loc[('b', 2), 'Colorado']
```

Ex5
---
We have to set the `axis` parameter of the `dropna` method.
```python
data.dropna(axis=1)
```

Ex5.5
---
The `corr` method returns the covariance matrix of the given DataFrame.
```python
baseball.corr()
```

Advanced Exercise
---
First of all, we have to provide a facility for reading a DataFrame from CSV files in a particular folder.
This is done in the `getCSVData` method.

Then, for the produced DataFrame corresponding to a country, we have to extract the information about the new cases and the deaths.
To do so, we provide the method `getDF` which gets as input the following parameters. The first
parameter specifies the name of the folder that we are reading the CSV files from.
The next 3 parameters specify the name of the columns for the description (or variable in the case of some countries) ,
date, and the total national number. The reason for passing them as parameters is because of the different titles associated with them
across the files of different countries. The next parameter specifies the name of the country to be stored in the produced
data frame. Finally, the last two parameters are functions which specify whether the description corresponds to a new case and new death respectively.

In the `getDF` method, first we use the `getCSVData` to get the DataFrame out of the CSV files in the corresponding folder of a particular 
country. Then, we filter the death cases and new cases rows based on the input functions. Then we substitute their values by either 
"Death" or "New" for consistency across different country formats. Afterwards, we concatenate the rows of death and new cases and
we project over the description, date, and status fields. Then, we add a country field which has the passed country name parameter as 
its value. Finally, we rename the columns to proper names.

For each of the countries we have defined functions which specify whether a description corresponds to a new case or new death.

Then, the `getDF` function is invoked for each of the countries using an appropriate folder name, column names, and functions for checking their status.

All these data frames are concatenated as the next step. 

Then, the rows that have missing values are dropped.

Finally, a hierarchical index is created using the country, status, and date columns.

```python
import glob
import os
data_path = r'Data/ebola'
def getCSVData(folder):  
    folder = glob.glob(os.path.join(data_path, folder))
    for f in folder:
        all_files = glob.glob(os.path.join(f, "*.csv"))
    all_data=[]
    for file in all_files:
       all_data.append(pd.read_csv(file))
    big_data = pd.concat(all_data)
    return big_data
def isNewCaseG(name):
   ln = name.lower()
   return "new" in ln and "confirm" in ln and "death" not in ln and "among" not in ln
def isDeathG(name):
   ln = name.lower()
   return "new" in ln and "register" in ln and "death" in ln and "among" not in ln and "(" not in ln
def isNewCaseL(name):
   ln = name.lower()
   return "new" in ln and "confirm" in ln and "death" not in ln and "case" in ln
def isDeathL(name):
   ln = name.lower()
   return "new" in ln and "reported" in ln and "deaths" in ln and "hcw" not in ln
def isNewCaseS(name):
   return name == "new_confirmed"
def isDeathS(name):
   return name == "death_confirmed"
def getDF(folder, descCol, dateCol, totalsCol, cntryName, isNewCase, isDeath):
    big_data = getCSVData(folder)
    deathidx = [isDeath(name) for name in big_data[descCol]]
    death_data = big_data[deathidx]
    death_data[descCol] = "Death"
    newcaseidx = [isNewCase(name) for name in big_data[descCol]]
    new_data = big_data[newcaseidx]
    new_data[descCol] = "New"
    new_death = pd.concat([death_data, new_data])
    pnd = new_death[[dateCol, descCol, totalsCol]]
    pnd["Country"] = cntryName
    pnd.columns = ["Date", "Status", "Totals", "Country"]
    return pnd
g = getDF("guinea_data", 'Description', 'Date', 'Totals', 'Guinea', isNewCaseG, isDeathG)
l = getDF("liberia_data", 'Variable', 'Date', 'National', 'Liberia', isNewCaseL, isDeathL)
s = getDF("sl_data", 'variable', 'date', 'National', 'Sl', isNewCaseS, isDeathS)
all = pd.concat([g, l, s])
allna = all.dropna()
idx_cols = ["Country", "Status", "Date"]
allidx = allna.set_index(idx_cols)
from IPython.display import HTML
HTML(allidx.to_html())
```
