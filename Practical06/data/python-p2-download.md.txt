
# Empirical project 2 — Working in Python


## Getting started in Python


Read the 'Getting Started in Python' page for help and advice on setting up a Python session to work with. Remember, you can run any page from this book as a *notebook* by downloading the relevant file from this [repository](https://github.com/aeturrell/core_python) and running it on your own computer. Alternatively, you can run pages online in your browser over at [Binder](https://mybinder.org/v2/gh/aeturrell/core_python/HEAD).


### Preliminary settings


Let's import the packages we'll need and also configure the settings we want:


```python
import pandas as pd
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
from pathlib import Path
import pingouin as pg
from lets_plot import *


LetsPlot.setup_html(no_js=True)


### You don't need to use these settings yourself
### — they are just here to make the book look nicer!
# Set the plot style for prettier charts:
plt.style.use(
    "https://raw.githubusercontent.com/aeturrell/core_python/main/plot_style.txt"
)
```


## Part 2.1 Collecting data by playing a public goods game


### Python walk-through 2.1 Plotting a line chart with multiple variables


Use the data from your own experiment to answer Question 1. As an example, we will use the data for the first three cities of the dataset that will be introduced in Part 2.2.


```python
# Create a dictionary with the data in
data = {
    "Copenhagen": [14.1, 14.1, 13.7, 12.9, 12.3, 11.7, 10.8, 10.6, 9.8, 5.3],
    "Dniprop": [11.0, 12.6, 12.1, 11.2, 11.3, 10.5, 9.5, 10.3, 9.0, 8.7],
    "Minsk": [12.8, 12.3, 12.6, 12.3, 11.8, 9.9, 9.9, 8.4, 8.3, 6.9],
}


df = pd.DataFrame.from_dict(data)
df.head()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Copenhagen</th>
      <th>Dniprop</th>
      <th>Minsk</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14.1</td>
      <td>11.0</td>
      <td>12.8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>14.1</td>
      <td>12.6</td>
      <td>12.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13.7</td>
      <td>12.1</td>
      <td>12.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12.9</td>
      <td>11.2</td>
      <td>12.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12.3</td>
      <td>11.3</td>
      <td>11.8</td>
    </tr>
  </tbody>
</table>
</div>


Now we need to plot the data. Note that, with data in 'wide' format (one column per city) and with an index, simply calling `.plot` on a **pandas** dataframe will create a **matplotlib** line chart. We could also use the **lets-plot** package to make this kind of chart, but it expects data in 'tidy' or 'long' format—and for that, we would have to reshape the data so that the city names were values in a single column called 'city' or similar. Let's just use **matplotlib** for now.


```python
# Plot the data
fig, ax = plt.subplots()
df.plot(ax=ax)
ax.set_title("Average contributions to the public goods game: Without punishment")
ax.set_ylabel("Average contribution")
ax.set_xlabel("Round");
```


![png](empirical_project_2_files/empirical_project_WT1_1.png)


**Figure 2.1** Average contribution to the public goods game: without punishment.


*Tip:* When using **pandas**, there are several different types of brackets for accessing data values. Let's list them so that you know the differences. Here are the different ways to get the first column of a dataframe (when that first column is called `column` and the dataframe is `df`):


- `df.column`
- `df["column"]`
- `df.loc[:, "column"]`
- `df.iloc[:, 0]`


Note that `:` means 'give me everything'! The ways to access rows are similar (here assuming the first row is called `row`):


- `df.loc["row", :]`
- `df.iloc[0, :]`


And to access the first value (i.e. the value in first row, first column):


- `df.column[0]`
- `df["column"][0]`
- `df.iloc[0, 0]`
- `df.loc["row", "column"]`


In the above examples, square brackets are instructions to Python about *where* to grab information from the dataframe. They are like an address system for values within a dataframe. However, square brackets also denote lists, so if you want to select multiple columns or rows, you might see syntax like this:


`df.loc[["row0", "row1"], ["column0", "column2"]]`


This code picks out two rows and two columns via the lists `["row0", "row1"]` and `["column0", "column2"]`. Because there are lists as well as the usual system of selecting values, there are two sets of square brackets.


## Part 2.2 Describing the data


### Python walk-through 2.2 Importing the datafile into Python


Both the tables you need are in a single Excel worksheet. Note down the cell ranges of each table, in this case A2:Q12 for the without punishment data and A16:Q26 for the punishment data. We will use this range information to import the data into two dataframes (`data_n` and `data_p` respectively).


In the code below, we'll use the `.copy` method, which we'll explain more about in a moment.


```python
data_np = pd.read_excel(
    "data/doing-economics-datafile-working-in-excel-project-2.xlsx",
    usecols="A:Q",
    header=1,
    index_col="Period",
)
data_n = data_np.iloc[:10, :].copy()
data_p = data_np.iloc[14:24, :].copy()
```


When loading the data from Excel, you may see an error message about an 'unknown extension'. Note that this particular Excel file has some issues that mean **pandas** will warn you about an 'unknown extension': an Excel file is actually a bundle of files tied up to look like one file, and what has happened here is that **pandas** doesn't recognise one of the files in the bundle. Despite this issue, we can still import the data we need in the worksheets.


In the code above, we used `.copy` and you may be wondering what it does. When a new object (say `data_p`) is created from an *existing* object (here `data_np`), programming languages have a few different options for how to do it. In this case, Python has two options: it could allocate some entirely new memory to store the new variable, `data_p`, or it could just create a link to the *existing* bit of memory where some of `data_np` is stored.


The two different approaches behave differently. Under the former, changes to `data_p` won't affect `data_np` because `data_p` gets its own bit of memory and is entirely independent of the existing variable. But in the latter case, any changes to `data_p` will also be applied to `data_np`! This is because, underneath it all, they're both ‘pointing’ to the same bit of computer memory. Indeed, that is why variables that do this are sometimes called *pointers*. They're common to most programming languages and **pandas** tends to use them by default because they save on memory. This case is just an example of a situation where we don't want to change `data_np` by changing `data_p`, so we use the `.copy` method to allocate new memory and avoid creating a pointer.


Let's see a simple example of how this `.copy` method works:


```python
test_data = {
    "City A": [14.1, 14.1, 13.7],
    "City B": [11.0, 12.6, 12.1],
}


# Original dataframe
test_df = pd.DataFrame.from_dict(test_data)
# A copy of the dataframe
test_copy = test_df.copy()
# A pointer to the dataframe
test_pointer = test_df


test_pointer.iloc[1, 1] = 99
```


Now, even though we only modified `test_pointer`, we can look at both the original data frame and the copy that we took earlier:


```python
print("test_df=")
print(f"{test_df}\n")
print("test_copy=")
print(f"{test_copy}\n")
```


```
test_df=
   City A  City B
0    14.1    11.0
1    14.1    99.0
2    13.7    12.1


test_copy=
   City A  City B
0    14.1    11.0
1    14.1    12.6
2    13.7    12.1
```


We see that `test_df` has changed because `test_pointer` pointed to it, but our pure copy, `test_copy`, hasn't changed.


As well as importing the correct data, we're going to ensure it is of the correct *datatype*. Common datatypes include ‘double’ and ‘integer’ (for numbers), string (for words), and ‘category’ (for variables that take on a fixed number of categories, like ethnicity or educational attainment). We can check the datatypes of the data we just read in using `data_n.info()` (you can do the same for `data_p`).


```python
data_n.info()
```


```
<class 'pandas.core.frame.DataFrame'>
Index: 10 entries, 1 to 10
Data columns (total 16 columns):
 #   Column           Non-Null Count  Dtype
---  ------           --------------  -----
 0   Copenhagen       10 non-null     object
 1   Dnipropetrovs’k  10 non-null     object
 2   Minsk            10 non-null     object
 3   St. Gallen       10 non-null     object
 4   Muscat           10 non-null     object
 5   Samara           10 non-null     object
 6   Zurich           10 non-null     object
 7   Boston           10 non-null     object
 8   Bonn             10 non-null     object
 9   Chengdu          10 non-null     object
 10  Seoul            10 non-null     object
 11  Riyadh           10 non-null     object
 12  Nottingham       10 non-null     object
 13  Athens           10 non-null     object
 14  Istanbul         10 non-null     object
 15  Melbourne        10 non-null     object
dtypes: object(16)
memory usage: 1.3+ KB
```


All of the columns are of the 'object' type, which is Python’s default when it's not clear what datatype to use.


We have continuous real numbers in the columns of `data_n` and `data_p` here, so we'll set the datatypes to be `double`, which is a datatype used for continuous real numbers.


```python
data_n = data_n.astype("double")
data_p = data_p.astype("double")
```


You can look at the data either by opening the dataframes from the Environment window or by typing `data_n` or `data_p` into the interactive Python window.


You can see that in each row, the average contribution varies across countries; in other words, there is a distribution of average contributions in each period.


### Python walk-through 2.3 Calculating the mean using the `.mean()` or the `agg` function


We calculate the mean using two different methods, to illustrate that there are usually many ways of achieving the same thing. We apply the first method on `data_n`, which uses the built-in `.mean()` function to calculate the average separately over each column except the first. We use the second method (the `agg` function) on `data_p`.


```python
mean_n_c = data_n.mean(axis=1)
mean_p_c = data_p.agg(np.mean, axis=1)
```


As the name suggests, the `agg` function applies an aggregation function (the mean function in this case) to all rows or columns in a dataframe. The second input, `axis=1`, applies the specified function to all rows in `data_p`, so we are taking the average over cities for each period.


Typing `axis=0` would have calculated column means instead, i.e. it would have averaged over periods to produce one value per city (run this code to see for yourself). Type `help(pd.DataFrame.agg)` in your interactive Python window for more details, or see Python walk-through 2.5 for further practice.


####Plot the mean contribution####


Now we will produce a line chart showing the mean contributions.


```python
fig, ax = plt.subplots()
mean_n_c.plot(ax=ax, label="Without punishment")
mean_p_c.plot(ax=ax, label="With punishment")
ax.set_title("Average contributions to the public goods game")
ax.set_ylabel("Average contribution")
ax.legend();
```


![png](empirical_project_2_files/empirical_project_2_WT2_1.png)


**Figure 2.2** Average contributions to the public goods game, with and without punishment.


The difference between experiments is stark, as the contributions increase and then stabilise at around 13 in the case where there is punishment, but decrease consistently from around 11 to 4 across the rounds when there is no punishment.


### Python walk-through 2.4 Drawing a column chart to compare two groups


To do this next part, we're going to use something called a 'list comprehension', which is a special kind of loop. Loops are very useful in programming when you have the same task that you want to execute for a sequence of values. You could use a loop to find the squares of the first 10 numbers, for example.


A list comprehension is a way of writing a loop that creates a Python list. The loops it creates tend to be fast to run too.


As a specific example, let's say we wanted to add the first name 'John' to a list of names. Using a list comprehension, the code would be:


```python
partial_names_list = ["F. Kennedy", "Lennon", "Maynard Keynes", "Wayne"]
["John " + name for name in partial_names_list]
```


```
['John F. Kennedy', 'John Lennon', 'John Maynard Keynes', 'John Wayne']
```


The second line shows the syntax: square bracket (which usually signifies a list), then an operation (here `"John" + name`), and then `for name_of_thing in name_of_list` (replace `name_of_thing` and `name_of_list` with the thing you would like to apply the loop to, and your list name).


To make a column chart, we will use the `.plot.bar()` function. We first extract the four data points we need (Periods 1 and 10, with and without punishment) and place them into another dataframe (called `compare_grps`).


```python
# Create new dataframe with bars in
compare_grps = pd.DataFrame(
    [mean_n_c.loc[[1, 10]], mean_p_c.loc[[1, 10]]],
    index=["Without punishment", "With punishment"],
)
# Rename columns to have 'round' in them
compare_grps.columns = ["Round " + str(i) for i in compare_grps.columns]
# Swap the column and index variables around with the transpose function, ready for plotting (.T is transpose)
compare_grps = compare_grps.T
# Make a bar chart
compare_grps.plot.bar(rot=0);
```


![png](empirical_project_2_files/empirical_project_2_31_0.png)


**Figure 2.3** Mean contributions in a public goods game.


*Tip:* Experimenting with these charts will help you to learn how to use Python and its packages. Try using `.plot.bar(stacked=True)` or using `rot=45` as *keyword arguments*, or using `.plot.barh()` instead.


### Python walk-through 2.5 Calculating and understanding the standard deviation


In order to calculate these standard deviations and variances, we will use the `agg` function, which we introduced in Python walk-through 2.3. As we saw, `agg` is a command that asks **pandas** to aggregate a set of rows or columns of the dataframe using a particular aggregation function. The basic structure is as follows: `dataframe_name.agg([function1, function2, ...], rows/columns)`. So to calculate the variances and more, we use the following command:


```python
n_c = data_n.agg(["std", "var", "mean"], 1)
n_c
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>std</th>
      <th>var</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>Period</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2.020724</td>
      <td>4.083325</td>
      <td>10.578313</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.238129</td>
      <td>5.009220</td>
      <td>10.628398</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2.329569</td>
      <td>5.426891</td>
      <td>10.407079</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2.068213</td>
      <td>4.277504</td>
      <td>9.813033</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2.108329</td>
      <td>4.445049</td>
      <td>9.305433</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2.240881</td>
      <td>5.021549</td>
      <td>8.454844</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2.136614</td>
      <td>4.565117</td>
      <td>7.837568</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2.349442</td>
      <td>5.519880</td>
      <td>7.376388</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2.413845</td>
      <td>5.826645</td>
      <td>6.392985</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2.187126</td>
      <td>4.783520</td>
      <td>4.383769</td>
    </tr>
  </tbody>
</table>
</div>


Here we take `data_n` and apply the `"var"` and `"std"` functions to each row (recall that the second input 1 does this; 0 would indicate columns). Note that the index column, which contains the period numbers, is automatically excluded from the calculation. The result is saved as a new variable called `n_c`.


We then apply the same principle to the `data_p` dataframe.


```python
p_c = data_p.agg(["std", "var", "mean"], 1)
```


*Aside:* In the next chart, we will use another kind of loop. The syntax for this one is 'for ‘thing’ in list of things', then a colon (':'), then an indented operation that uses 'thing'.


To determine whether 95% of the observations fall within two standard deviations of the mean, we can use a line chart. As we have 16 countries in every period, we would expect about one observation (0.05 × 16 = 0.8) to fall outside this interval.


```python
fig, ax = plt.subplots()
n_c["mean"].plot(ax=ax, label="mean")
# mean + 2 standard deviations
(n_c["mean"] + 2 * n_c["std"]).plot(ax=ax, ylim=(0, None), color="red", label="±2 s.d.")
# mean - 2 standard deviations
(n_c["mean"] - 2 * n_c["std"]).plot(ax=ax, ylim=(0, None), color="red", label="")
for i in range(len(data_n.columns)):
    ax.scatter(x=data_n.index, y=data_n.iloc[:, i], color="k", alpha=0.3)
ax.legend()
ax.set_ylabel("Average contribution")
ax.set_title("Contribution to public goods game without punishment")
plt.show();
```


![png](empirical_project_2_files/empirical_project_2_39_0.png)


**Figure 2.4** Contribution to public goods game without punishment.


None of the observations fall outside the mean ± two standard deviations interval for the public goods game without punishment. Let’s plot the equivalent chart for the version with punishment.


```python
fig, ax = plt.subplots()
p_c["mean"].plot(ax=ax, label="mean")
# mean + 2 sd
(p_c["mean"] + 2 * p_c["std"]).plot(ax=ax, ylim=(0, None), color="red", label="±2 s.d.")
# mean - 2 sd
(p_c["mean"] - 2 * p_c["std"]).plot(ax=ax, ylim=(0, None), color="red", label="")
for i in range(len(data_p.columns)):
    ax.scatter(x=data_p.index, y=data_p.iloc[:, i], color="k", alpha=0.3)
ax.legend()
ax.set_ylabel("Average contribution")
ax.set_title("Contribution to public goods game with punishment")
plt.show();
```


![png](empirical_project_2_files/empirical_project_2_WT5_1.png)


**Figure 2.5** Contribution to public goods game with punishment.


Here, we only have one observation outside the interval (in Period 8). In that aspect the two experiments look similar. However, from comparing these two charts, the game with punishment displays a greater variation of responses than the game without punishment. In other words, there is a larger standard deviation and variance for the observations coming from the game with punishment.


### Python walk-through 2.6 Finding the minimum, maximum, and range of a variable


We're now going to see one of our first *functions*. A function takes inputs, does some operations on them, and returns outputs.


You can imagine functions as vending machines: for them to work you need some inputs (money, and a choice of snack or drink), then an operation happens (your drink or snack is dropped into the tray), and finally there is an output (your drink or snack as you grab it).


Functions are incredibly useful in programming because they are separate units that can be tested in isolation, re-used, and given helpful ‘dressing’ (such as information on how they work) that make code more readable.


To calculate the range for both experiments and for all periods, we will use an `apply` method in combination with the `max` and `min` methods that apply to a column or row. We'll also use a *lambda function* to bring these all together. In our case, it's going to look like this:


```python
data_p.apply(lambda x: x.max() - x.min(), axis=1)
```


```
Period
1     10.199675
2     12.185065
3     12.689935
4     12.625000
5     12.140375
6     12.827541
7     13.098931
8     13.482621
9     13.496754
10    11.307360
dtype: float64
```


This lambda function tells Python to take the difference between the maximum and minimum of each row.


A *lambda function* is an idea in programming (and mathematics) that has a long and interesting history. You don't need to know all that, but it is instructive to look at a more general example of a lambda function:


```python
# A lambda function accepting three inputs, a, b, and c, and calculating the sum of the squares
test_function = lambda a, b, c: a**2 + b**2 + c**2


# Now we apply the function by handing over (in parenthesis) the following inputs: a=3, b=4 and c=5
test_function(3, 4, 5)
```


```
50
```


Above, we defined a lambda function that looked like `lambda x: x.max() - x.min()`. It accepts one input, `x` (which could be a row or column), and returns the range of `x`. Because making code reusable is good programming practice, we will define this function and give it a name using a separate line of code like this:


`range_function = lambda x: x.max() - x.min()`


When we call `data_p.apply(range_function, axis=1)`, the following will happen: `data_p` contains the experimental data (with punishment). We will apply the `range_function` to that data. As `data_p` has two dimensions, we also need to let Python know over which dimension it should calculate the minimum and maximum. The `axis=1` option in the apply function tells the apply function that it should apply the `range_function` over rows rather than columns (to get columns, it would be `axis=0`, which is also the default if you don't specify the axis keyword argument).


```python
range_function = lambda x: x.max() - x.min()
range_p = data_p.apply(range_function, axis=1)
range_n = data_n.apply(range_function, axis=1)
```


Let’s create a chart of the ranges for both experiments for all periods in order to compare them.


```python
fig, ax = plt.subplots()
range_p.plot(ax=ax, label="With punishment")
range_n.plot(ax=ax, label="Without punishment")
ax.set_ylim(0, None)
ax.legend()
ax.set_title("Range of contributions to the public goods game")
plt.show();
```


![png](empirical_project_2_files/empirical_project_2_51_0.png)


**Figure 2.6** Range of contributions to the public goods game.


This chart confirms what we found in Python walk-through 2.5, which is that there is a greater spread (variation) of contributions in the game with punishment.


### Python walk-through 2.7 Creating a table of summary statistics


We have already done most of the work for creating this summary table in Python walk-through 2.6. Since we also want to display the minimum and maximum values, we should create these too. And it's convenient to add in `std` and `mean` using the same syntax (even though we created a separate mean earlier), so we have all the information in one place. We'll call our new summary statistics `summ_p` and `summ_n`.


```python
funcs_to_apply = [range_function, "max", "min", "std", "mean"]
summ_p = data_p.apply(funcs_to_apply, axis=1).rename(columns={"<lambda>": "range"})
summ_n = data_n.apply(funcs_to_apply, axis=1).rename(columns={"<lambda>": "range"})
```


Note that as well as applying all of the functions in the list `funcs_to_apply`, we also renamed the first function using the `rename` method. Because the range isn't a built-in aggregation function and we defined it, it is automatically given a column name—and because the range function we supplied is a lambda function, the name it gets is `"<lambda>"`. Using `rename(columns=`, we change this name to `"range"` using a dictionary object (`{ : }`) that maps the old name to the new name.


Now we display the summary statistics in a table. We use the `round` method, which reduces the number of digits displayed after the decimal point (`2` in our case) and makes the table easier to read. We're only interested in periods 1 and 10, so we pass a list, `[1, 10]`, to the `.loc` selector in the first position (which corresponds to rows and the index). We want all columns, so we pass `:` to the second position of the `.loc` selector.


```python
summ_n.loc[[1, 10], :].round(2)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>range</th>
      <th>max</th>
      <th>min</th>
      <th>std</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>Period</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>6.14</td>
      <td>14.10</td>
      <td>7.96</td>
      <td>2.02</td>
      <td>10.58</td>
    </tr>
    <tr>
      <th>10</th>
      <td>7.38</td>
      <td>8.68</td>
      <td>1.30</td>
      <td>2.19</td>
      <td>4.38</td>
    </tr>
  </tbody>
</table>
</div>


Now we do the same for the version with punishment.


```python
summ_p.loc[[1, 10], :].round(2)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>range</th>
      <th>max</th>
      <th>min</th>
      <th>std</th>
      <th>mean</th>
    </tr>
    <tr>
      <th>Period</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>10.20</td>
      <td>16.02</td>
      <td>5.82</td>
      <td>3.21</td>
      <td>10.64</td>
    </tr>
    <tr>
      <th>10</th>
      <td>11.31</td>
      <td>17.51</td>
      <td>6.20</td>
      <td>3.90</td>
      <td>12.87</td>
    </tr>
  </tbody>
</table>
</div>


## Part 2.3 Describing the data


### Python walk-through 2.8 Calculating the p-value for the difference in means


We need to extract the observations in Period 1 for the data for with and without punishment, and then feed the observations into a function that performs a t-test. We'll use the statistics package `pingouin` for this, which you will need to install on the command line using `pip install pingouin`. Once installed, import it using `import pingouin as pg`, just like we did at the start of the chapter.


*Tip:* you can open up the command line, also known as the *terminal* or *command prompt*, in order to install packages in multiple ways.  If you're working within Visual Studio Code use the <kbd>⌃</kbd> + <kbd>\`</kbd> keyboard shortcut (Mac) or <kbd>ctrl</kbd> + <kbd>\`</kbd> (Windows and Linux), or click 'View > Terminal'. If you want to open up the command line independently of Visual Studio Code, search for 'Terminal' on Mac and Linux, and 'Anaconda Prompt' on Windows.


**pingouin**'s t-test function is called `ttest`. The `ttest` function is extremely flexible: if you input two variables (`x` and `y`) as shown below, it will automatically test whether the difference in means is likely to be due to chance or not (formally speaking, it tests the null hypothesis that the means of both variables are equal).


Note that the `ttest` function will only accept one series of data, not multiple data series. By subsetting (`iloc[1, :]`), we are passing in the 0th row (the first period) for all columns (cities).


```python
pg.ttest(x=data_n.iloc[0, :], y=data_p.iloc[0, :])
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>T</th>
      <th>dof</th>
      <th>alternative</th>
      <th>p-val</th>
      <th>CI95%</th>
      <th>cohen-d</th>
      <th>BF10</th>
      <th>power</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>t-test</th>
      <td>-0.063782</td>
      <td>30</td>
      <td>two-sided</td>
      <td>0.949567</td>
      <td>[-2.0, 1.87]</td>
      <td>0.02255</td>
      <td>0.337</td>
      <td>0.050437</td>
    </tr>
  </tbody>
</table>
</div>


Note that as well as the t-statistic (`T`), the p-value (`p-val`), the degrees of freedom (`dof`), the alternative hypothesis (`two-sided`) and the confidence interval (`CI95%`), we get some other variables that help us put the main results into context.


This result delivers a p-value of 0.9496. This means it is very likely that the assumption that there are no differences in the populations is likely to be true (formally speaking, we cannot reject the null hypothesis).


The `ttest` function automatically assumes that both variables were generated by different groups of people. When calculating the p-value, it assumes that the observed differences are partly due to some variation in characteristics between these two groups, and not just the differences in experimental conditions. However, in this case, the same groups of people did both experiments, so there will not be any variation in characteristics between the groups. When calculating the p-value, we account for this fact with the `paired=True` option.


```python
pg.ttest(x=data_n.iloc[0, :], y=data_p.iloc[0, :], paired=True)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


```
.dataframe tbody tr th {
    vertical-align: top;
}


.dataframe thead th {
    text-align: right;
}
```


</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>T</th>
      <th>dof</th>
      <th>alternative</th>
      <th>p-val</th>
      <th>CI95%</th>
      <th>cohen-d</th>
      <th>BF10</th>
      <th>power</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>t-test</th>
      <td>-0.149959</td>
      <td>15</td>
      <td>two-sided</td>
      <td>0.882795</td>
      <td>[-0.92, 0.8]</td>
      <td>0.02255</td>
      <td>0.258</td>
      <td>0.05082</td>
    </tr>
  </tbody>
</table>
</div>


The p-value becomes smaller as we can attribute more of the differences to the ‘with punishment’ treatment, but the p-value is still very large (0.8828), so we still conclude that the differences in Period 1 are likely to be due to chance.
