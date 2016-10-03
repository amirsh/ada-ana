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
