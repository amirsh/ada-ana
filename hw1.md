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
