

# Empirical project 1: Measuring climate change. **Working in Python**


## Getting started in Python


Head to the ‘Getting started in Python’ page for help and advice on setting up a Python session to work with. Remember, you can run any project as a *notebook* by downloading the relevant file from this [repository](https://github.com/aeturrell/core_python) and running it on your own computer. Alternatively, you can run pages online in your browser using [Binder](https://mybinder.org/v2/gh/aeturrell/core_python/HEAD).


### Preliminary settings


At the start of every Python session, you first need to import the extensions (also called packages or libraries) that you'll need. These packages extend the base language in ways that are helpful. There are literally hundreds of thousands of libraries but you'll find yourself reaching for the same ones again and again because a core few have most of what you'll need. Among these core few are **pandas** for data analysis, **numpy** for numerical operations, **matplotlib** and **lets-plot** for plotting, and **pingouin** for running statistical tests. Although not in as wide use as the other packages, in some of these projects we'll use **skimpy** for creating summary tables of statistics too.


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




### You don't need to use these settings yourself,
### they are just here to make the book look nicer!
# Set the plot style for prettier charts:
plt.style.use(
    "https://raw.githubusercontent.com/aeturrell/core_python/main/plot_style.txt"
)
```


## Part 1.1 The behaviour of average surface temperature over time


### *Python walk-through 1.1*  Importing the datafile into Python


We want to import the datafile called ‘NH.Ts+dSST.csv’ from NASA's website into Python using Visual Studio Code.


We start by opening Visual Studio Code in the folder we'll be working in. Use File -&#160;> Open Folder to do this. We also need to ensure that the interactive Python window that we'll be using to run Python code opens in this folder. In Visual Studio Code, you can ensure that the interactive window starts in the folder that you have open by setting “Jupyter: Notebook File Root" to `${workspaceFolder}` in the Settings menu.


Now create a new file (File -> New File in the menu) and name it `exercise_1.py`. In the new and empty file, right-click and select 'Run file in interactive window'. This will launch the interactive Python window.


You will need to install the following packages: **pandas**, **numpy**, **lets-plot**, and **matplotlib**. **pandas**, **numpy**, and **matplotlib** provide extra functionality for data analysis, numbers, and plotting respectively. **lets-plot** provides some additional plotting functionality.


We will download the data directly from the internet into our Python session using the **pandas** library. We imported **pandas** as `pd` at the start of the script, so any direct command from that package is going to start with `pd.`.


We're going to use the `pd.read_csv` function to read in some data from a URL.


```python
df = pd.read_csv(
    "https://data.giss.nasa.gov/gistemp/tabledata_v4/NH.Ts+dSST.csv",
    skiprows=1,
    na_values="***",
)
```


When using the `read_csv` function, we added two options. If you open the spreadsheet in Excel, you will see that the real data only starts in Row 2, so we use the `skiprows = 1` option to skip the first row when importing the data. When looking at the spreadsheet, you can see that missing temperature data is coded as `***`. In order to ensure that the missing temperature data is recorded as numbers, we tell **pandas** that `na_values = "***"` which imports those missing values as `NaN`, which means the number is missing.


Note that we ended each line with a comma. Arguments that get passed to functions like `read_csv` need to be separated by commas. You don't have to include the comma after the last argument, but some people prefer this as a matter of style.


You can also use `pd.read_csv` to open files that are stored locally on your computer. Instead of a URL, enter a file path to wherever you saved your data (enclosed in quote marks).


To check that the data has been imported correctly, you can use the `.head()` function to view the first five rows of the dataset, and confirm that they correspond to the columns in the csv file.


```python
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
      <th>Year</th>
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
      <th>J-D</th>
      <th>D-N</th>
      <th>DJF</th>
      <th>MAM</th>
      <th>JJA</th>
      <th>SON</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1880</td>
      <td>-0.37</td>
      <td>-0.53</td>
      <td>-0.25</td>
      <td>-0.32</td>
      <td>-0.08</td>
      <td>-0.18</td>
      <td>-0.20</td>
      <td>-0.28</td>
      <td>-0.25</td>
      <td>-0.34</td>
      <td>-0.45</td>
      <td>-0.42</td>
      <td>-0.31</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>-0.22</td>
      <td>-0.22</td>
      <td>-0.35</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1881</td>
      <td>-0.32</td>
      <td>-0.24</td>
      <td>-0.06</td>
      <td>-0.02</td>
      <td>0.01</td>
      <td>-0.35</td>
      <td>0.06</td>
      <td>-0.06</td>
      <td>-0.28</td>
      <td>-0.46</td>
      <td>-0.39</td>
      <td>-0.25</td>
      <td>-0.20</td>
      <td>-0.21</td>
      <td>-0.33</td>
      <td>-0.02</td>
      <td>-0.12</td>
      <td>-0.38</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1882</td>
      <td>0.24</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>-0.33</td>
      <td>-0.26</td>
      <td>-0.32</td>
      <td>-0.30</td>
      <td>-0.17</td>
      <td>-0.26</td>
      <td>-0.54</td>
      <td>-0.35</td>
      <td>-0.69</td>
      <td>-0.23</td>
      <td>-0.20</td>
      <td>0.06</td>
      <td>-0.20</td>
      <td>-0.26</td>
      <td>-0.38</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1883</td>
      <td>-0.59</td>
      <td>-0.69</td>
      <td>-0.17</td>
      <td>-0.31</td>
      <td>-0.26</td>
      <td>-0.15</td>
      <td>-0.05</td>
      <td>-0.24</td>
      <td>-0.34</td>
      <td>-0.17</td>
      <td>-0.43</td>
      <td>-0.16</td>
      <td>-0.30</td>
      <td>-0.34</td>
      <td>-0.66</td>
      <td>-0.25</td>
      <td>-0.15</td>
      <td>-0.31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1884</td>
      <td>-0.18</td>
      <td>-0.11</td>
      <td>-0.64</td>
      <td>-0.61</td>
      <td>-0.38</td>
      <td>-0.44</td>
      <td>-0.41</td>
      <td>-0.52</td>
      <td>-0.46</td>
      <td>-0.46</td>
      <td>-0.59</td>
      <td>-0.49</td>
      <td>-0.44</td>
      <td>-0.42</td>
      <td>-0.15</td>
      <td>-0.54</td>
      <td>-0.46</td>
      <td>-0.51</td>
    </tr>
  </tbody>
</table>
</div>


Before working with this data, we use the `.info()` function to check that the data was read in as numbers (either real numbers, `float64`, or integers, `int`) rather than strings.


```python
df.info()
```


```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 144 entries, 0 to 143
Data columns (total 19 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   Year    144 non-null    int64  
 1   Jan     144 non-null    float64
 2   Feb     144 non-null    float64
 3   Mar     144 non-null    float64
 4   Apr     144 non-null    float64
 5   May     144 non-null    float64
 6   Jun     144 non-null    float64
 7   Jul     144 non-null    float64
 8   Aug     143 non-null    float64
 9   Sep     143 non-null    float64
 10  Oct     143 non-null    float64
 11  Nov     143 non-null    float64
 12  Dec     143 non-null    float64
 13  J-D     143 non-null    float64
 14  D-N     142 non-null    float64
 15  DJF     143 non-null    float64
 16  MAM     144 non-null    float64
 17  JJA     143 non-null    float64
 18  SON     143 non-null    float64
dtypes: float64(18), int64(1)
memory usage: 21.5 KB
```


You can see that all variables are formatted as either `float64` or `int`, so Python correctly recognises that these data items are numbers.


Try importing the data again without using the keyword argument option `na_values="***"` at all and see what difference it makes.


### *Python walk-through 1.2*  Drawing a line chart of temperature and time


We will now set the year as the *index* of the dataset. (This will make plotting the time series of temperature easier because the index can automatically take on the role of the horizontal axis.)


```python
df = df.set_index("Year")
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
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
      <th>J-D</th>
      <th>D-N</th>
      <th>DJF</th>
      <th>MAM</th>
      <th>JJA</th>
      <th>SON</th>
    </tr>
    <tr>
      <th>Year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1880</th>
      <td>-0.37</td>
      <td>-0.53</td>
      <td>-0.25</td>
      <td>-0.32</td>
      <td>-0.08</td>
      <td>-0.18</td>
      <td>-0.20</td>
      <td>-0.28</td>
      <td>-0.25</td>
      <td>-0.34</td>
      <td>-0.45</td>
      <td>-0.42</td>
      <td>-0.31</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>-0.22</td>
      <td>-0.22</td>
      <td>-0.35</td>
    </tr>
    <tr>
      <th>1881</th>
      <td>-0.32</td>
      <td>-0.24</td>
      <td>-0.06</td>
      <td>-0.02</td>
      <td>0.01</td>
      <td>-0.35</td>
      <td>0.06</td>
      <td>-0.06</td>
      <td>-0.28</td>
      <td>-0.46</td>
      <td>-0.39</td>
      <td>-0.25</td>
      <td>-0.20</td>
      <td>-0.21</td>
      <td>-0.33</td>
      <td>-0.02</td>
      <td>-0.12</td>
      <td>-0.38</td>
    </tr>
    <tr>
      <th>1882</th>
      <td>0.24</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>-0.33</td>
      <td>-0.26</td>
      <td>-0.32</td>
      <td>-0.30</td>
      <td>-0.17</td>
      <td>-0.26</td>
      <td>-0.54</td>
      <td>-0.35</td>
      <td>-0.69</td>
      <td>-0.23</td>
      <td>-0.20</td>
      <td>0.06</td>
      <td>-0.20</td>
      <td>-0.26</td>
      <td>-0.38</td>
    </tr>
    <tr>
      <th>1883</th>
      <td>-0.59</td>
      <td>-0.69</td>
      <td>-0.17</td>
      <td>-0.31</td>
      <td>-0.26</td>
      <td>-0.15</td>
      <td>-0.05</td>
      <td>-0.24</td>
      <td>-0.34</td>
      <td>-0.17</td>
      <td>-0.43</td>
      <td>-0.16</td>
      <td>-0.30</td>
      <td>-0.34</td>
      <td>-0.66</td>
      <td>-0.25</td>
      <td>-0.15</td>
      <td>-0.31</td>
    </tr>
    <tr>
      <th>1884</th>
      <td>-0.18</td>
      <td>-0.11</td>
      <td>-0.64</td>
      <td>-0.61</td>
      <td>-0.38</td>
      <td>-0.44</td>
      <td>-0.41</td>
      <td>-0.52</td>
      <td>-0.46</td>
      <td>-0.46</td>
      <td>-0.59</td>
      <td>-0.49</td>
      <td>-0.44</td>
      <td>-0.42</td>
      <td>-0.15</td>
      <td>-0.54</td>
      <td>-0.46</td>
      <td>-0.51</td>
    </tr>
  </tbody>
</table>
</div>


```python
df.tail()
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
      <th>Jan</th>
      <th>Feb</th>
      <th>Mar</th>
      <th>Apr</th>
      <th>May</th>
      <th>Jun</th>
      <th>Jul</th>
      <th>Aug</th>
      <th>Sep</th>
      <th>Oct</th>
      <th>Nov</th>
      <th>Dec</th>
      <th>J-D</th>
      <th>D-N</th>
      <th>DJF</th>
      <th>MAM</th>
      <th>JJA</th>
      <th>SON</th>
    </tr>
    <tr>
      <th>Year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019</th>
      <td>1.19</td>
      <td>1.11</td>
      <td>1.54</td>
      <td>1.24</td>
      <td>0.98</td>
      <td>1.18</td>
      <td>1.03</td>
      <td>1.08</td>
      <td>1.20</td>
      <td>1.30</td>
      <td>1.19</td>
      <td>1.38</td>
      <td>1.20</td>
      <td>1.18</td>
      <td>1.13</td>
      <td>1.25</td>
      <td>1.10</td>
      <td>1.23</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>1.58</td>
      <td>1.70</td>
      <td>1.65</td>
      <td>1.39</td>
      <td>1.27</td>
      <td>1.12</td>
      <td>1.09</td>
      <td>1.12</td>
      <td>1.19</td>
      <td>1.21</td>
      <td>1.60</td>
      <td>1.22</td>
      <td>1.35</td>
      <td>1.36</td>
      <td>1.55</td>
      <td>1.44</td>
      <td>1.11</td>
      <td>1.33</td>
    </tr>
    <tr>
      <th>2021</th>
      <td>1.25</td>
      <td>0.95</td>
      <td>1.19</td>
      <td>1.12</td>
      <td>1.03</td>
      <td>1.20</td>
      <td>1.06</td>
      <td>1.02</td>
      <td>1.05</td>
      <td>1.31</td>
      <td>1.30</td>
      <td>1.13</td>
      <td>1.13</td>
      <td>1.14</td>
      <td>1.14</td>
      <td>1.11</td>
      <td>1.09</td>
      <td>1.22</td>
    </tr>
    <tr>
      <th>2022</th>
      <td>1.24</td>
      <td>1.16</td>
      <td>1.42</td>
      <td>1.08</td>
      <td>1.00</td>
      <td>1.12</td>
      <td>1.05</td>
      <td>1.16</td>
      <td>1.15</td>
      <td>1.31</td>
      <td>1.09</td>
      <td>1.06</td>
      <td>1.15</td>
      <td>1.16</td>
      <td>1.18</td>
      <td>1.17</td>
      <td>1.11</td>
      <td>1.18</td>
    </tr>
    <tr>
      <th>2023</th>
      <td>1.28</td>
      <td>1.31</td>
      <td>1.59</td>
      <td>1.01</td>
      <td>1.10</td>
      <td>1.18</td>
      <td>1.43</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.22</td>
      <td>1.24</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>


The way we've set up our dataframe, the year variable is special because it forms the index. This has a lot of consequences for the operations we can do on the dataframe; for example, if we do whole-dataframe operations like `df+20`, 20 will be added to every value except the values (here years) in the index.


Next we'll make our chart. We will draw a line chart using data for January, `df["Jan"]` for the years 1880–2016.


We'll use the **matplotlib** package for this (which we imported at the start as `plt`). A typical **matplotlib** line chart has the following elements:


```python
# create a figure (a bit like a canvas) and an axis (ax)
# onto which to put chart elements
fig, ax = plt.subplots()
# select the column to use 'plot' on, and pass the ax object
# note that the horizontal axis is given by the index of the dataframe
df["column_name"].plot(ax=ax)
# set the labels and title
ax.set_ylabel("y label")
ax.set_xlabel("x label")
ax.set_title("title")
# show the plot
plt.show()
```


Let's see an example of this with the data for January.


```python
fig, ax = plt.subplots()
df["Jan"].plot(ax=ax)
ax.set_ylabel("y label")
ax.set_xlabel("x label")
ax.set_title("title")
plt.show()
```


![png](empirical_project_1_files/empirical_project_1_16_0.png)


You'll see that we didn't have to say to use years as the horizontal axis. That's because the years are in the index, and, when we selected a single column of values representing January, **pandas** inferred that we wanted to use the index as the horizontal axis. Try out plotting:


```python
fig, ax = plt.subplots()
ax.plot(df.index, df["Jan"])
ax.set_ylabel("y label")
ax.set_xlabel("x label")
ax.set_title("title")
plt.show()
```


You should find that you get exactly the same chart! But this time, we made it explicit that we wanted the horizontal axis to be drawn from the index.


Note that a lot of what we can do on the chart can be achieved by calling `ax.[something]` and that we finish by showing the chart with `plt.show()`. (You can also save charts to file with `plt.savefig(name-of-chart.pdf)`.)


Now, we'll do a nicer version of the chart. Rather than 'hard-code' the month in, we'll abstract the specific month into a 'month' variable. That way, the code can easily be re-used for any month.


We'll add a horizontal line to make the chart easier to read using `ax.axhline`. As this zero is actually the average over the period we're looking at, we'll add some text annotation with `ax.annotate`. We specify this by passing in `x` and `y` values for where the text appears. For the `x` position, it's convenient to place it two-thirds of the way across the figure regardless of how zoomed-in the chart is so we pass 'figure fraction' for the units of the x-coordinate; we just use the data for the y-coordinate of the text.


The next line plots January's data—but note we don't specify the x-axis for the data because that's in our dataframe's index.


Then we're onto labelling and titling the chart. We can make use of something here called an 'f-string'. These allow us to pass Python variables into a string. In this case, we made `month` a variable but we'd like to put it in the title. The simplest f-string that does this would be `f"Average temperature anomaly in {month}..."` which would result in a title of 'Average temperature anomaly in Jan...' on the chart. You can pass as many variables into the string as you like, and we can use this to automatically retrieve the most recent year in the data via `df.index.max()`—this way, if we update our data, the chart title is automatically updated too!


```python
month = "Jan"
fig, ax = plt.subplots()
ax.axhline(0, color="orange")
ax.annotate("1951—1980 average", xy=(0.66, -0.2), xycoords=("figure fraction", "data"))
df[month].plot(ax=ax)
ax.set_title(
    f"Average temperature anomaly in {month} \n in the northern hemisphere (1880—{df.index.max()})"
)
ax.set_ylabel("Annual temperature anomalies");
```


![png](empirical_project_1_files/empirical_project_1_18_0.png)


Try different values for `color`, `xy`, and the first argument of `ax.axhline` in the plot function to figure out what these options do. (Note that `xycoords` set the behaviour of `xy`.)


It is important to remember that all axis and chart titles should be enclosed in quotation marks (`""`), as well as any words that are not options (for example, colour names or filenames).


### *Python walk-through 1.3* Producing a line chart for the annual temperature anomalies


This is where the power of programming languages becomes evident: to produce the same line chart for a different variable, we simply take the code used in Python walk-through 1.2 and replace the `"Jan"` with the name for the annual variable (`"J-D"`). We don't need to change the rest of the chart because we created it to be flexible. (We selected the `month` column but we changed the value of `month` from "Jan" to "J-D".)


```python
month = "J-D"
fig, ax = plt.subplots()
ax.axhline(0, color="orange")
ax.annotate("1951—1980 average", xy=(0.68, -0.2), xycoords=("figure fraction", "data"))
df[month].plot(ax=ax)
ax.set_title(
    f"Average annual temperature anomaly in \n in the northern hemisphere (1880—{df.index.max()})"
)
ax.set_ylabel("Annual temperature anomalies");
```


![png](empirical_project_1_files/empirical_project_1_21_0.png)


## Part 1.2 Variation in temperature over time


### *Python walk-through 1.4* Creating frequency tables and histograms


Since we will be looking at data from different sub-periods (year intervals) separately, we will create a categorical variable (a variable that has two or more categories) that indicates the sub-period for each observation (row). In Python this type of variable is called a ‘category’ or categorical. When we create a categorical column, we need to define the categories that this variable can take.


We'll achieve this using the `pd.cut` function, which arranges input data into a series of bins that can have labels. We'll give the data labels that reflect what period they correspond to here, and we'll also specify that there is an order for these categories.


```python
df["Period"] = pd.cut(
    df.index,
    bins=[1921, 1950, 1980, 2010],
    labels=["1921—1950", "1951—1980", "1981—2010"],
    ordered=True,
)
```


We created a new variable called `"Period"` and defined the possible categories using the `labels=` keyword argument. Since we will not be using data for some years (before 1921 and after 2010), we want `"Period"` to take the value `NaN` (not a number) for these observations (rows), and the appropriate category for all the other observations. The `pd.cut` function does this automatically.


Let's take a look at the last 20 entries of the new column of data using `.tail`:


```python
df["Period"].tail(20)
```


```
Year
2004    1981—2010
2005    1981—2010
2006    1981—2010
2007    1981—2010
2008    1981—2010
2009    1981—2010
2010    1981—2010
2011          NaN
2012          NaN
2013          NaN
2014          NaN
2015          NaN
2016          NaN
2017          NaN
2018          NaN
2019          NaN
2020          NaN
2021          NaN
2022          NaN
2023          NaN
Name: Period, dtype: category
Categories (3, object): ['1921—1950' < '1951—1980' < '1981—2010']
```


We'd really like to combine the data from the three summer months. This is easy to do using the `.stack` function. Let's look at the first few rows of the data once stacked using `.head()`:


```python
list_of_months = ["Jun", "Jul", "Aug"]
df[list_of_months].stack().head()
```


```
Year     
1880  Jun   -0.18
      Jul   -0.20
      Aug   -0.28
1881  Jun   -0.35
      Jul    0.06
dtype: float64
```


Now we need to think about how we can plot the three different periods. **matplotlib** has plenty of ways to do this; one of the easiest is to ask for more than one axis object to put plots on. So, in the below, we ask for `ncols=3`, and this returns multiple `axes` instead of just one axis called `ax`. `axes` is actually a list that we can access individual plots in. To iterate over both axes and periods, we use the `zip` function which works exactly like a zipper: it brings together one entry from each list in turn—in this case, one axis and one period. We can use this to plot the histogram data one axis at a time in the zipped `for` loop. Within the loop the data is filtered just to the period, using `==period`, and months, using `list_of_months`, that we want.


Finally we set an overall title and a single horizontal axis label (on the middle chart).


```python
fig, axes = plt.subplots(ncols=3, figsize=(9, 4), sharex=True, sharey=True)
for ax, period in zip(axes, df["Period"].dropna().unique()):
    df.loc[df["Period"] == period, list_of_months].stack().hist(ax=ax)
    ax.set_title(period)
plt.suptitle("Histogram of temperature anomalies")
axes[1].set_xlabel("Summer temperature distribution")
plt.tight_layout();
```


![png](empirical_project_1_files/empirical_project_1_30_0.png)


To explain what a histogram displays, let's take a look at the histogram for the period from 1921 to 1950. On the horizontal axis we have a number of bins, for example 0 to 0.1, 0.1 to 0.2, and so on. The height of the bar over each interval represents the count of the number of anomalies that fall in the interval. The bar with the greatest height indicates the most frequently encountered temperature interval.


As you can see from the earlier data, there are virtually no temperature anomalies larger than 0.3. The height of these bars gives a useful overview of the *distribution* of the temperature anomalies.


Now consider how this distribution changes as we move through the three distinct time periods. The distribution is clearly moving to the right for the period 1981–2010, which is an indication that the temperature is increasing; in other words, an indication of global warming.




### *Python walk-through 1.5* Using the `np.quantile` function


First, we need to create a variable that contains all monthly anomalies in the years 1951–1980. Then, we'll use **numpy**'s `np.quantile` function to find the required percentiles (0.3 and 0.7 refer to the 3rd and 7th deciles, respectively).


To help us create a variable that encodes this information, we're going to filter our **pandas** dataframe. We want to filter it both by the index and by only the single month columns. We can do both of these at once using the `.loc` method. The `.loc` method works like this `df.loc[[rows you would like], [columns you would like]]`. For rows, we're going to ask for everything in between two year limits. For columns, we can do something called 'slicing', which selects every column from within a given range in the format \`"first column you want":"last column you want".


Note: You may get slightly different values to those shown here if you are using the latest data.


```python
# Create a variable that has years 1951 to 1980, and months Jan to Dec (inclusive)
temp_all_months = df.loc[(df.index >= 1951) & (df.index <= 1980), "Jan":"Dec"]
# Put all the data in stacked format and give the new columns sensible names
temp_all_months = (
    temp_all_months.stack()
    .reset_index()
    .rename(columns={"level_1": "month", 0: "values"})
)
# Take a look at this data:
temp_all_months
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
      <th>Year</th>
      <th>month</th>
      <th>values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1951</td>
      <td>Jan</td>
      <td>-0.36</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1951</td>
      <td>Feb</td>
      <td>-0.52</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1951</td>
      <td>Mar</td>
      <td>-0.18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1951</td>
      <td>Apr</td>
      <td>0.06</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1951</td>
      <td>May</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>355</th>
      <td>1980</td>
      <td>Aug</td>
      <td>0.09</td>
    </tr>
    <tr>
      <th>356</th>
      <td>1980</td>
      <td>Sep</td>
      <td>0.10</td>
    </tr>
    <tr>
      <th>357</th>
      <td>1980</td>
      <td>Oct</td>
      <td>0.12</td>
    </tr>
    <tr>
      <th>358</th>
      <td>1980</td>
      <td>Nov</td>
      <td>0.20</td>
    </tr>
    <tr>
      <th>359</th>
      <td>1980</td>
      <td>Dec</td>
      <td>0.09</td>
    </tr>
  </tbody>
</table>
<p>360 rows × 3 columns</p>
</div>


```python
quantiles = [0.3, 0.7]
list_of_percentiles = np.quantile(temp_all_months["values"], q=quantiles)


print(f"The cold threshold of {quantiles[0]*100}% is {list_of_percentiles[0]}")
print(f"The hot threshold of {quantiles[1]*100}% is {list_of_percentiles[1]}")
```


```
The cold threshold of 30.0% is -0.1
The hot threshold of 70.0% is 0.1
```


After we have filled it, the variable `list_of_percentiles` (which is a list) contains two numbers, the 30th percentile (1st value) and the 70th percentile (2nd value). When we print out these values using f-strings, we want to access the 1st and 2nd value for the cold and hot thresholds respectively. Python indexes lists and arrays from zero (not from 1!), so, to access the 1st entry, we use `list_of_percentiles[0]`.


It may seem odd to index from zero but many programming languages do this. [Some programmers think it's better](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html), while some think it's worse (they're more similar to economists than you'd think).


### *Python walk-through 1.6* Computing the proportion of anomalies at a given quantile using the `.mean()` function


*Note:* You may get slightly different values to those shown here if you are using the latest data.


We repeat the steps used in Python walk-through 1.5, now looking at monthly anomalies in the years 1981–2010. We can simply change the year values in the code from Python walk-through 1.5.


```python
# Create a variable that has years 1981 to 2010, and months Jan to Dec (inclusive)
temp_all_months = df.loc[(df.index >= 1981) & (df.index <= 2010), "Jan":"Dec"]
# Put all the data in stacked format and give the new columns sensible names
temp_all_months = (
    temp_all_months.stack()
    .reset_index()
    .rename(columns={"level_1": "month", 0: "values"})
)
# Take a look at the start of this data data:
temp_all_months.head()
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
      <th>Year</th>
      <th>month</th>
      <th>values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1981</td>
      <td>Jan</td>
      <td>0.79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1981</td>
      <td>Feb</td>
      <td>0.63</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1981</td>
      <td>Mar</td>
      <td>0.67</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1981</td>
      <td>Apr</td>
      <td>0.39</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1981</td>
      <td>May</td>
      <td>0.18</td>
    </tr>
  </tbody>
</table>
</div>


Now that we have all the monthly data for 1981–2010, we want to count the proportion of observations that are smaller than –0.1. We'll first create an *indicator variable* (i.e., it's True or False) that says, for each row (observation) in `temp_all_months`, whether the number is lower than the 0.3 quantile or not (given by `list_of_percentiles[0]`). Then we'll take the mean of this list of True and False values; when you take the mean of indicator variables, each True evaluates to 1 and each False to 0, so the mean gives us the proportion of entries in `temp_all_months` that are lower than the 0.3 quantile:


```python
entries_less_than_q30 = temp_all_months["values"] < list_of_percentiles[0]
proportion_under_q30 = entries_less_than_q30.mean()
print(
    f"The proportion under {list_of_percentiles[0]} is {proportion_under_q30*100:.2f}%"
)
```


```
The proportion under -0.1 is 1.94%
```


When we printed out the answer, we used some *number formatting*. This is written as `:.2f` within the curly brackets part of an f-string—this tells Python to display the number with two decimal places. You should also note that, as well as the mean given by `.mean()`, there are various other built-in functions like `.std()` for the standard deviation and `.var()` for the variance.


Now we can assess that between 1951 and 1980, 30% of observations for the temperature anomaly were smaller than –0.10, but between 1981 and 2010 only about two per cent of months are considered cold. That is a large change.


Let’s check whether we get a similar result for the number of observations that are larger than 0.11.


```python
proportion_over_q70 = (temp_all_months["values"] > list_of_percentiles[1]).mean()
print(f"The proportion over {list_of_percentiles[1]} is {proportion_over_q70*100:.2f}%")
```


```
The proportion over 0.1 is 84.72%
```


### *Python walk-through 1.7* Calculating and understanding mean and variance


The process of computing the mean and variance separately for each period and season separately would be quite tedious. We would prefer a way to cover all of them at once. Let's re-stack the data in a form where `season` is one of the columns and could take the values `DJF`, `MAM`, `JJA`, or `SON`. Let's also have check the structure of the data while we're at it:


```python
temp_all_months = (
    df.loc[:, "DJF":"SON"]
    .stack()
    .reset_index()
    .rename(columns={"level_1": "Season", 0: "Values"})
)
temp_all_months["Period"] = pd.cut(
    temp_all_months["Year"],
    bins=[1921, 1950, 1980, 2010],
    labels=["1921—1950", "1951—1980", "1981—2010"],
    ordered=True,
)
# Take a look at a cut of the data using `.iloc`, which provides position
temp_all_months.iloc[-135:-125]
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
      <th>Year</th>
      <th>season</th>
      <th>values</th>
      <th>Period</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>438</th>
      <td>1989</td>
      <td>SON</td>
      <td>0.20</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>439</th>
      <td>1990</td>
      <td>DJF</td>
      <td>0.45</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>440</th>
      <td>1990</td>
      <td>MAM</td>
      <td>0.79</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>441</th>
      <td>1990</td>
      <td>JJA</td>
      <td>0.40</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>442</th>
      <td>1990</td>
      <td>SON</td>
      <td>0.45</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>443</th>
      <td>1991</td>
      <td>DJF</td>
      <td>0.51</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>444</th>
      <td>1991</td>
      <td>MAM</td>
      <td>0.45</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>445</th>
      <td>1991</td>
      <td>JJA</td>
      <td>0.42</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>446</th>
      <td>1991</td>
      <td>SON</td>
      <td>0.32</td>
      <td>1981—2010</td>
    </tr>
    <tr>
      <th>447</th>
      <td>1992</td>
      <td>DJF</td>
      <td>0.44</td>
      <td>1981—2010</td>
    </tr>
  </tbody>
</table>
</div>


Now we'll take the mean and variance at once.


The following line of code will do a lot of things at the same time. First we will allocate each observation (Year–Season–Period–Temperature allocation combination) into one of 12 groups defined by the different possible combinations of season and period, e.g. "MAM; 1981-2010". Think of these twelve groups as each having a label like "MAM; 1981-2010" or "JJA; 1921-1950". This is grouping our data and we do this with a `groupby` operation, to which we pass a list of the variables we'd like to group together; here that will be `"Period"` and `"Season"`.


The variable we'd like to apply the grouping to is `"Values"` so we then filter down to just the `"Values"` column with `["Values"]`.


Then, once we have allocated each observation in the 'Values' column to one of these groups, we ask **pandas** to calculate the mean and variance of the observations of each of the groups. That is what the `agg([np.mean, np.var])` function does.


```python
grp_mean_var = temp_all_months.groupby(["Season", "Period"])["Values"].agg(
    [np.mean, np.var]
)
grp_mean_var
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
      <th></th>
      <th>mean</th>
      <th>var</th>
    </tr>
    <tr>
      <th>season</th>
      <th>Period</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">DJF</th>
      <th>1921—1950</th>
      <td>-0.030000</td>
      <td>0.057650</td>
    </tr>
    <tr>
      <th>1951—1980</th>
      <td>-0.002333</td>
      <td>0.050494</td>
    </tr>
    <tr>
      <th>1981—2010</th>
      <td>0.524333</td>
      <td>0.079198</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">JJA</th>
      <th>1921—1950</th>
      <td>-0.057931</td>
      <td>0.021846</td>
    </tr>
    <tr>
      <th>1951—1980</th>
      <td>0.000333</td>
      <td>0.014527</td>
    </tr>
    <tr>
      <th>1981—2010</th>
      <td>0.399667</td>
      <td>0.067686</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">MAM</th>
      <th>1921—1950</th>
      <td>-0.046207</td>
      <td>0.032196</td>
    </tr>
    <tr>
      <th>1951—1980</th>
      <td>0.000333</td>
      <td>0.025217</td>
    </tr>
    <tr>
      <th>1981—2010</th>
      <td>0.508667</td>
      <td>0.075433</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">SON</th>
      <th>1921—1950</th>
      <td>0.077931</td>
      <td>0.027881</td>
    </tr>
    <tr>
      <th>1951—1980</th>
      <td>-0.001000</td>
      <td>0.026133</td>
    </tr>
    <tr>
      <th>1981—2010</th>
      <td>0.428333</td>
      <td>0.110883</td>
    </tr>
  </tbody>
</table>
</div>


We recognise that the variances seem to remain fairly constant across the first two periods, but they do increase markedly for the 1981–2010 period.


We can plot a line chart to see these changes graphically—to do that in the most convenient way, we're going to make use of a different plotting library that works really well with data that is arranged in this format where *each row is an observation, each column is a variable, and each entry is a value*. This is called ‘tidy’ data.


There are broadly two categories of approach to using code to create data visualisations: imperative, where you build what you want, and declarative, where you say what you want. Choosing which to use involves a trade-off: imperative libraries (like **matplotlib**) offer you flexibility but at the cost of some verbosity; declarative libraries offer you a quick way to plot your data, but only if it’s in the right format to begin with (the *tidy* format!), and customisation to special chart types is more difficult. Python has many excellent plotting packages, but for these projects we recommend a tidy data-friendly declarative library called **lets-plot** for the vast majority of charts you might want to make and then **matplotlib** when you need something much more customised.


The **lets-plot** plotting library follows what is called a *grammar of graphics* approach. You don't need to worry too much about what that means for now, only that it is a coherent declarative system for describing and building graphs. There is some background information that you might find useful in getting to grips with [**lets-plot**](https://lets-plot.org/). All plots are composed of the data, the information you want to visualise, and a mapping: the description of how the data’s variables are mapped to aesthetic attributes. There are five mapping components:


- A *layer* is a collection of geometric elements and statistical transformations. Geometric elements, *geoms* for short, represent what you actually see in the plot: points, lines, polygons, etc. Statistical transformations, stats for short, summarise the data: for example, binning and counting observations to create a histogram, or fitting a linear model.


- *Scales* map values in the data space to values in the aesthetic space. This includes the use of colour, shape, or size. Scales also draw the legend and axes.


- A *coord*, or coordinate system, describes how data coordinates are mapped to the plane of the graphic. It also provides axes and gridlines to help read the graph. We normally use the Cartesian coordinate system, but a number of others are available, including polar coordinates and map projections.


- A *facet* specifies how to break up and display subsets of data as small multiples.


- A *theme* controls the finer points of display, like the font size and background colour. While the defaults have been chosen with care, you may need to consult other references to create an attractive plot.


At the start of this project, we imported **lets_plot** and set it up using `LetsPlot.setup_html(no_js=True)`, so we should be ready to use it!


```python
min_year = 1880
(
    ggplot(temp_all_months, aes(x="Year", y="Values", color="Season"))
    + geom_abline(slope=0, color="black", size=1)
    + geom_line(size=1)
    + labs(
        title=f"Average annual temperature anomaly in \n in the northern hemisphere ({min_year}—{temp_all_months['Year'].max()})",
        y="Annual temperature anomalies",
    )
    + scale_x_continuous(format="d")
    + geom_text(
        x=min_year, y=0.1, label="1951—1980 average", hjust=”left”, color="black"
    )
)
```
![png](empirical_project_1_files/empirical_project_1_WT7_1.png)


Let's talk through what each part of the code did here:


- `ggplot` took the data and a second argument, the `aes` (short for aesthetic mappings) function.
- In `aes`, we passed the mappings we wanted: year along the horizontal axis, the values column on the y, and colour to distinguish between different seasons (via `color="season"`).
- `geom_abline` adds a line from a to b, in this case just along the horizontal axis at y = 0.
- `geom_text` adds the text annotation.
- `geom_line` adds a line for each season. Because we already said `color="season"` earlier, we actually get as many lines as there are seasons in our data.
- `labs` sets the labels.
- `scale_x_continuous(format="d")` tells **lets-plot** that the horizontal axis is a continuous (rather than discrete) scale, and that the format should just be a number with no decimal points and no commas (which is what we want for displaying years).


## Part 1.3 Carbon emissions and the environment


### Python walk-through 1.8 Scatterplots and the correlation coefficient


First we will use the `pd.read_csv` function to import the CO2 datafile into Python, and call it `df_co2`.


```python
df_co2 = pd.read_csv("data/1_CO2-data.csv")
df_co2.head()
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
      <th>Year</th>
      <th>Month</th>
      <th>Monthly average</th>
      <th>Interpolated</th>
      <th>Trend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1958</td>
      <td>3</td>
      <td>315.71</td>
      <td>315.71</td>
      <td>314.62</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1958</td>
      <td>4</td>
      <td>317.45</td>
      <td>317.45</td>
      <td>315.29</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1958</td>
      <td>5</td>
      <td>317.50</td>
      <td>317.50</td>
      <td>314.71</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1958</td>
      <td>6</td>
      <td>-99.99</td>
      <td>317.10</td>
      <td>314.85</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1958</td>
      <td>7</td>
      <td>315.86</td>
      <td>315.86</td>
      <td>314.98</td>
    </tr>
  </tbody>
</table>
</div>


This file has monthly data, but in contrast to the data in `df` from earlier, the data is all in so-called ‘tidy’ format (one observation per row, one column per variable). To make this task easier, we will pick only the June data from the CO2 emissions and add them as an additional variable to the `df` dataset.


Python's **pandas** package has a convenient function called `merge` to do this. First we create a new dataset that contains only the June emissions data (`df_co2_june`).


```python
df_co2_june = df_co2.loc[df_co2["Month"] == 6]
df_co2_june.head()
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
      <th>Year</th>
      <th>Month</th>
      <th>Monthly average</th>
      <th>Interpolated</th>
      <th>Trend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>1958</td>
      <td>6</td>
      <td>-99.99</td>
      <td>317.10</td>
      <td>314.85</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1959</td>
      <td>6</td>
      <td>318.15</td>
      <td>318.15</td>
      <td>315.92</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1960</td>
      <td>6</td>
      <td>319.59</td>
      <td>319.59</td>
      <td>317.36</td>
    </tr>
    <tr>
      <th>39</th>
      <td>1961</td>
      <td>6</td>
      <td>319.77</td>
      <td>319.77</td>
      <td>317.48</td>
    </tr>
    <tr>
      <th>51</th>
      <td>1962</td>
      <td>6</td>
      <td>320.55</td>
      <td>320.55</td>
      <td>318.27</td>
    </tr>
  </tbody>
</table>
</div>


Then we use this data in the `pd.merge` function. The merge function takes the original `df` and the `df_co2_june` and merges (combines) them together. To merge, we need some commonality between the columns (or indexes) in the dataframe. In this case, the common variable is `"Year"` so we will use that to merge the data.


(*Extension:* Hover your cursor over `pd.merge` in Visual Studio Code, type `help(pd.merge)` into the interactive window, or Google ‘pandas merge’ to see the many other options that `pd.merge` allows.)


```python
df_temp_co2 = pd.merge(df_co2_june, df, on="Year")
df_temp_co2[["Year", "Jun", "Trend"]].head()
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
      <th>Year</th>
      <th>Jun</th>
      <th>Trend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1958</td>
      <td>0.05</td>
      <td>314.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1959</td>
      <td>0.14</td>
      <td>315.92</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1960</td>
      <td>0.18</td>
      <td>317.36</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1961</td>
      <td>0.18</td>
      <td>317.48</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1962</td>
      <td>-0.13</td>
      <td>318.27</td>
    </tr>
  </tbody>
</table>
</div>


It looks like it worked! We now have some extra columns from the carbon dioxide data in addition to the temperature anomaly data from before.


To make a scatterplot, we use **lets-plot** again:


```python
(
    ggplot(df_temp_co2, aes(x="Jun", y="Trend"))
    + geom_point(color="black", size=3)
    + labs(
        title="Scatterplot of temperature anomalies vs carbon dioxide emissions",
        y="Carbon dioxide levels (trend, mole fraction)",
        x="Temperature anomaly (degrees Celsius)",
    )
)
```
![png](empirical_project_1_files/empirical_project_1_WT8_1.png)


To calculate the correlation coefficient, we can use the `.corr()` function. *Note:* you may get slightly different results if you are using the latest data.


```python
df_temp_co2[["Jun", "Trend"]].corr(method="pearson")
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
      <th>Jun</th>
      <th>Trend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Jun</th>
      <td>1.000000</td>
      <td>0.915272</td>
    </tr>
    <tr>
      <th>Trend</th>
      <td>0.915272</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>


In this case, the correlation coefficient tells us that an upward-sloping straight line is quite a good fit to the date (as seen on the scatterplot). There is a strong positive association between the two variables (higher temperature anomalies are associated with higher CO2 levels).


One limitation of this correlation measure is that it only tells us about the strength of the upward- or downward-sloping linear relationship between two variables; in other words, how closely the scatterplot aligns along an upward- or downward-sloping straight line. The correlation coefficient cannot tell us if the two variables have a different kind of relationship (such as that represented by a wavy line).


*Note:* The word ‘strong’ is often used for coefficients that are close to 1 or −1, and ‘weak’ is often used for coefficients that are close to 0, though there is no precise range of values that are considered ‘strong’ or ‘weak’.


If you need more insight into correlation coefficients, you may find it helpful to watch online tutorials such as [‘Correlation coefficient intuition’](https://tinyco.re/4363520) from the Khan Academy.


As we are dealing with time-series data, it is often more instructive to look at a line plot, as a scatterplot cannot convey how the observations relate to each other in the time dimension.


Let’s start by plotting the June temperature anomalies.


```python
(
    ggplot(df_temp_co2, aes(x="Year", y="Jun"))
    + geom_line(size=1)
    + labs(
        title="June temperature anomalies",
    )
    + scale_x_continuous(format="d")
)
```


![png](empirical_project_1_files/empirical_project_1_WT8_2.png)


Typically, when using the `plot` function, we would now only need to add the line for the second variable using the lines command. The issue, however, is that the CO2 emissions variable (Trend) is on a different scale, and the automatic vertical axis scale (from –0.2 to about 1.2) would not allow for the display of Trend. To resolve this issue, we can introduce a second panel and put them side by side. You can think of the new plotting space as being like a table, with an overall title and two columns.


We'll do this using **lets-plot** again. We're going to create a base plot and save it to a variable called `base_plot`, then we'll create two panels of an overall figure by adding different elements (one for June, one for the trend) onto this base plot. Finally, we'll bring them both together using the `gggrid` function.


```python
base_plot = ggplot(df_temp_co2) + scale_x_continuous(format="d")
plot_p = (
    base_plot
    + geom_line(aes(x="Year", y="Jun"), size=1)
    + labs(title="June temperature anomalies")
)
plot_q = (
    base_plot
    + geom_line(aes(x="Year", y="Trend"), size=1)
    + labs(title="Carbon dioxide emissions")
)
gggrid([plot_p, plot_q], ncol=2)
```
![png](empirical_project_1_files/empirical_project_1_WT8_3.png)








