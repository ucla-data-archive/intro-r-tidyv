---
title: "Data Visualization via ggplot2"
author: "Tim Dennis"
date: "10/15/2018"
output: html_document
---




# (PART) Data Science via the tidyverse {-} 

# Data Visualization via ggplot2 {#viz}



* development of your **data science toolbox** with data visualization.
* By visualizing our data, we will be able to gain valuable insights from our data that we couldn't see from just looking at the raw data in spreadsheet form.  
* We will use the `ggplot2` package because it provides an easy way to customize your plots and is rooted in the data visualization theory known as _The Grammar of Graphics_ [@wilkinson2005].

* graphics/plots/charts (we use these terms interchangeably in this book) provide a nice way for us to get a sense for how quantitative variables compare in terms of their **center** (where the values tend to be located) and their spread (how they vary around the center).  
* The most important thing to know about graphics is that they should be created to make it obvious for your audience to understand the findings and insight you want to get across.  
* This does however require a balancing act. 
* On the one hand, you want to highlight as many meaningful relationships and interesting findings as possible, but on the other you don't want to include so many as to overwhelm your audience.  

* identify patterns and outliers in our data.  We will see that a common extension of these ideas is to compare the *distribution* of one quantitative variable (i.e., what the spread of a variable looks like or how the variable is *distributed* in terms of its values) as we go across the levels of a different categorical variable.



### Needed packages {-}

Let's load all the packages needed for this chapter (this assumes you've already installed them). Read Section \@ref(packages) for information on how to install and load R packages.



```r
library(nycflights13)
library(ggplot2)
library(dplyr)
```





## The Grammar of Graphics {#grammarofgraphics}

* start with a theoretical framework for data visualization known as the "The Grammar of Graphics," which serves as the basis for the `ggplot2` package.  
* like how we construct sentences in any language by using a linguistic grammar (nouns, verbs, subjects, objects, etc.), the theoretical framework given by Leland Wilkinson [@wilkinson2005] allows us to specify the components of a statistical graphic.

### Components of the Grammar

In short, the grammar tells us that:

> **A statistical graphic is a `mapping` of `data` variables to `aes`thetic attributes of `geom`etric objects.**

Specifically, we can break a graphic into the following three essential components:

1. `data`: the data-set comprised of variables that we map.
1. `geom`: the geometric object in question. This refers to our type of objects we can observe in our plot. For example, points, lines, bars, etc.
1. `aes`: aesthetic attributes of the geometric object that we can perceive on a graphic. For example, x/y position, color, shape, and size.  Each assigned aesthetic attribute can be mapped to a variable in our data-set.

Let's break down the grammar with an example.

### Gapminder {#gapminder}



In February 2006, a statistician named Hans Rosling gave a TED talk titled ["The best stats you've ever seen"](https://www.ted.com/talks/hans_rosling_shows_the_best_stats_you_ve_ever_seen) where he presented global economic, health, and development data from the website [gapminder.org](http://www.gapminder.org/tools/#_locale_id=en;&chart-type=bubbles). For example, from the 1704 countries included from 2007, consider only the first 6 countries when listed alphabetically:

<table class="table" style="font-size: 16px; margin-left: auto; margin-right: auto;">
<caption style="font-size: initial !important;">Gapminder 2007 Data: First 6 of 142 countries</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Country </th>
   <th style="text-align:left;"> Continent </th>
   <th style="text-align:right;"> Life Expectancy </th>
   <th style="text-align:right;"> Population </th>
   <th style="text-align:right;"> GDP per Capita </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Afghanistan </td>
   <td style="text-align:left;"> Asia </td>
   <td style="text-align:right;"> 43.83 </td>
   <td style="text-align:right;"> 31889923 </td>
   <td style="text-align:right;"> 974.58 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Albania </td>
   <td style="text-align:left;"> Europe </td>
   <td style="text-align:right;"> 76.42 </td>
   <td style="text-align:right;"> 3600523 </td>
   <td style="text-align:right;"> 5937.03 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Algeria </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:right;"> 72.30 </td>
   <td style="text-align:right;"> 33333216 </td>
   <td style="text-align:right;"> 6223.37 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Angola </td>
   <td style="text-align:left;"> Africa </td>
   <td style="text-align:right;"> 42.73 </td>
   <td style="text-align:right;"> 12420476 </td>
   <td style="text-align:right;"> 4797.23 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Argentina </td>
   <td style="text-align:left;"> Americas </td>
   <td style="text-align:right;"> 75.32 </td>
   <td style="text-align:right;"> 40301927 </td>
   <td style="text-align:right;"> 12779.38 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Australia </td>
   <td style="text-align:left;"> Oceania </td>
   <td style="text-align:right;"> 81.23 </td>
   <td style="text-align:right;"> 20434176 </td>
   <td style="text-align:right;"> 34435.37 </td>
  </tr>
</tbody>
</table>

Each row in this table corresponds to a country in 2007. For each row, we have 5 columns:

1. **Country**: Name of country.
1. **Continent**: Which of the five continents the country is part of. (Note that `Americas` groups North and South America and that Antarctica is excluded here.)
1. **Life Expectancy**: Life expectancy in years.
1. **Population**: Number of people living in the country.
1. **GDP per Capita**: Gross domestic product (in US dollars).

Now consider Figure \@ref(fig:gapminder), which plots this data for all 142 countries in the data frame. Note that R will deal with large numbers using scientific notation.  So in the legend for "Population", 1.25e+09 = $1.25 \times 10^{9}$ = 1,250,000,000 = 1.25 billion. 

<img src="figure/gapminder-1.png" title="Life Expectancy over GDP per Capita in 2007" alt="Life Expectancy over GDP per Capita in 2007" width="\textwidth" />

Let's view this plot through the grammar of graphics:

1. The `data` variable **GDP per Capita** gets mapped to the `x`-position `aes`thetic of the points.
1. The `data` variable **Life Expectancy** gets mapped to the `y`-position `aes`thetic of the points.
1. The `data` variable **Population** gets mapped to the `size` `aes`thetic of the points.
1. The `data` variable **Continent** gets mapped to the `color` `aes`thetic of the points.

Recall that `data` here corresponds to each of the variables being in the same `data` frame and the "data variable" corresponds to a column in a data frame.

While in this example we are considering one type of `geom`etric object (of type `point`), graphics are not limited to just points. Some plots involve lines while others involve bars. Let's summarize the three essential components of the grammar in a table:

<table class="table" style="font-size: 16px; margin-left: auto; margin-right: auto;">
<caption style="font-size: initial !important;">Summary of Grammar of Graphics for this plot</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> data variable </th>
   <th style="text-align:left;"> aes </th>
   <th style="text-align:left;"> geom </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> GDP per Capita </td>
   <td style="text-align:left;"> x </td>
   <td style="text-align:left;"> point </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Life Expectancy </td>
   <td style="text-align:left;"> y </td>
   <td style="text-align:left;"> point </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Population </td>
   <td style="text-align:left;"> size </td>
   <td style="text-align:left;"> point </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Continent </td>
   <td style="text-align:left;"> color </td>
   <td style="text-align:left;"> point </td>
  </tr>
</tbody>
</table>

### Other components of the Grammar

* Some other components of the Grammar of Graphics we can control.  

- `facet`ing breaks up a plot into small multiples corresponding to the levels of another variable (Section \@ref(facets))
- `position` adjustments for barplots (Section \@ref(geombar))
<!--
- `scales` that both
    + convert *data units* to *physical units* the computer can display. For example, apply a log-transformation on one of the axes to focus on multiplicative rather than additive changes.
    + draw a legend and/or axes, which provide an inverse mapping to make it possible to read the original data values from the graph.
- `coord`inate system for x/y values: typically `cartesian`, but can also be `map` or `polar`.
- `stat`istical transformations: this includes smoothing, binning values into a histogram, or no transformation at all (known as the `"identity"` transformation).
-->

* Grammar of Graphics allows for a high degree of customization and also a consistent framework for easy updating/modification of plots.

### The ggplot2 package

* ggplot2 is an implementation of the g - o - g
* it's layered - think of photoshop - we define the data, specified  components, we then add *layers* to the plot using the `+` sign. 
* The most essential layer to add to a plot is the specification of which type of `geom`etric object we want the plot to involve; e.g. points, lines, bars. 
* Other layers we can add include the specification of the plot title, axes labels, facets, and visual themes for the plot.
* Let's now put the theory of the Grammar of Graphics into practice.

<!--
The plot given above is not a histogram, but the output does show us a bit of what is going on with `ggplot(data = weather, mapping = aes(x = temp))`.  It is producing a backdrop onto which we will "paint" elements.  We next proceed by adding a layer---hence, the use of the `+` symbol---to the plot to produce a histogram.  (Note also here that we don't have to specify the `data = ` and `mapping = ` text in our function calls.  This is covered in more detail in Chapter 5 of the "Getting Used to R, RStudio, and R Markdown" book [@usedtor2016]).
-->

<!--
<div class="review">
**_Review questions_**
</div>
**`paste0("(RQ", chap, ".", (rq <- rq + 1), ")")`**
- Have a variety of bad plots with data for the readers and have readers create better plots with `ggplot2`
- Have sample datasets to work with from problem statements
    + Identify the appropriate plot to address the questions of interest
- Why is it important for barplots to start at zero?
-->



## Five Named Graphs - The 5NG {#FiveNG}

For our purposes, we will be limiting consideration to five different types of graphs.  We term these five named graphs the **5NG**:

1. scatterplots
1. linegraphs
1. boxplots
1. histograms
1. barplots

We will discuss some variations of these plots, but with this basic repertoire in your toolbox you can visualize a wide array of different data variable types. Note that certain plots are only appropriate for categorical/logical variables and others only for quantitative variables. You'll want to quiz yourself often as we go along on which plot makes sense a given a particular problem or data-set.

<!--Subsection on scatter plots-->
## 5NG#1: Scatterplots {#scatterplots}

* simplest of the 5NG are *scatterplots* (also called bivariate plots); 
* they allow you to investigate the relationship between two numerical variables.
* say we want to investigate the relationship between:

1. `dep_delay`: departure delay on the horizontal "x" axis and
1. `arr_delay`: arrival delay on the vertical "y" axis

* for Alaska Airlines flights leaving NYC in 2013. 
* This requires paring down the data from all 336,776 flights that left NYC in 2013, to only the 714 *Alaska Airlines* flights that left NYC in 2013.

**What does this mean computationally?** 

Answer: 
* we'll take the `flights` data frame, extract only the 714 rows corresponding to Alaska Airlines flights, and save this in a new data frame called `alaska_flights`. 
* Run the code below in your console to do this: 


```r
alaska_flights <- flights %>% 
  filter(carrier == "AS")
```

* For now, ignore how this code works; 
* Convince yourself that this code does what it is supposed to by running `View(alaska_flights)` in the console: it creates a new data frame `alaska_flights` consisting of only the 714 Alaska Airlines flights.
* time permitting we'll see that this code uses the `dplyr` package for data wrangling to achieve our goal: it takes the `flights` data frame and `filter`s it to only return the rows where `carrier` is equal to `"AS"`, Alaska Airlines' carrier code. 
* Some other examples of carrier codes include "AA" for American Airlines and "UA" for United Airlines. Recall from Section \@ref(code) that testing for equality is specified with `==` and not `=`. Fasten your seat belts and sit tight for now however, we'll introduce these ideas more fully in Chapter \@ref(wrangling).

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.1)**  Take a look at both the `flights` and `alaska_flights` data frames by running `View(flights)` and `View(alaska_flights)` in the console. In what respect do these data frames differ?



<div class="learncheck">

</div>

### Scatterplots via geom_point {#geompoint}

We proceed to create the scatterplot using the `ggplot()` function:


```r
ggplot(data = alaska_flights, 
       mapping = aes(x = dep_delay, y = arr_delay)) + 
  geom_point()
```

<img src="figure/noalpha-1.png" title="Arrival Delays vs Departure Delays for Alaska Airlines flights from NYC in 2013" alt="Arrival Delays vs Departure Delays for Alaska Airlines flights from NYC in 2013" width="\textwidth" />

* we see that a positive relationship exists between `dep_delay` and `arr_delay`: 
   * as departure delays increase, arrival delays tend to also increase.  
* We also note that the majority of points fall near the point (0, 0) -- There is a large mass of points clustered there. 
* R also returns a warning message alerting us to the fact that 5 rows were ignored due to missing values. For 5 rows either the value for `dep_delay` or `arr_delay` or both were missing, and thus these rows were ignored in our plot.

* Let's go back to the `ggplot()` function call that created this visualization, keeping in mind our discussion in Section \@ref(grammarofgraphics):

* Within the `ggplot()` function call, we specify two of the components of the grammar:
    1. The `data` frame to be `alaska_flights` by setting `data = alaska_flights`
    1. The `aes`thetic `mapping` by setting `aes(x = dep_delay, y = arr_delay)`. Specifically
        * the variable `dep_delay` maps to the `x` position aesthetic
        * the variable `arr_delay` maps to the `y` position aesthetic
* We add a layer to the `ggplot()` function call using the `+` sign. The layer in question specifies the third component of the grammar:  the `geom`etric object. In this case the geometric object are `point`s, set by specifying `geom_point()`.

Some notes on layers:

* Note that the `+` sign comes at the end of lines, and not at the beginning.  You'll get an error in R if you put it at the beginning.
* When adding layers to a plot, you are encouraged to hit *Return* on your keyboard after entering the `+` so that the code for each layer is on a new line.  As we add more and more layers to plots, you'll see this will greatly improve the legibility of your code.
* To stress the importance of adding layers, in particular the layer specifying the `geom`etric object, consider Figure \@ref(fig:nolayers) where no layers are added. A not very useful plot!


```r
ggplot(data = alaska_flights, 
       mapping = aes(x = dep_delay, y = arr_delay))
```

<img src="figure/nolayers-1.png" title="Plot with No Layers" alt="Plot with No Layers" width="\textwidth" />


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.2)**  What are some practical reasons why `dep_delay` and `arr_delay` have a positive relationship?

**(LC3.3)**  What variables (not necessarily in the `flights` data frame) would you expect to have a negative correlation (i.e. a negative relationship) with `dep_delay`? Why? Remember that we are focusing on numerical variables here.

**(LC3.4)** Why do you believe there is a cluster of points near (0, 0)? What does (0, 0) correspond to in terms of the Alaskan flights?

**(LC3.5)** What are some other features of the plot that stand out to you?

**(LC3.6)** Create a new scatterplot using different variables in the `alaska_flights` data frame by modifying the example above.





<div class="learncheck">

</div>

### Over-plotting {#overplotting}

* The large mass of points near (0, 0) in can cause some confusion.  
* This is the result of a phenomenon called *overplotting* and this corresponds to values being plotted on top of each other _over_ and _over_ again.  
* It is often difficult to know just how many values are plotted in this way when looking at a basic scatterplot as we have here. There are two ways to address this issue:

1. By adjusting the transparency of the points via the `alpha` argument
1. By jittering the points via `geom_jitter()`

* The first way of relieving overplotting is by changing the `alpha` argument in `geom_point()` which controls the transparency of the points.  
* By default, this value is set to `1`.  
* We can change this to any value between `0` and `1` where `0` sets the points to be 100% transparent and `1` sets the points to be 100% opaque. Note how the following function call is identical to the one in Section \@ref(scatterplots), but with `alpha = 0.2` added to the `geom_point()`.


```r
ggplot(data = alaska_flights, 
       mapping = aes(x = dep_delay, y = arr_delay)) + 
  geom_point(alpha = 0.2)
```

<img src="figure/alpha-1.png" title="Delay scatterplot with alpha=0.2" alt="Delay scatterplot with alpha=0.2" width="\textwidth" />

* The key feature to note in Figure \@ref(fig:alpha) is that the transparency of the points is cumulative: areas with a high-degree of overplotting are darker, whereas areas with a lower degree are less dark. 
* Note that there is no `aes()` surrounding `alpha = 0.2` here. 
* Since we are **NOT mapping a variable to an aesthetic** but instead are changing a setting, we don't need to create a mapping with `aes()`.  
* In fact, you'll receive an error if you try to change the second line above to `geom_point(aes(alpha = 0.2))`.

* **second way** of relieving overplotting is to *jitter* the points a bit. 
  * In other words, we are going to add just a bit of random noise to the points to better see them and alleviate some of the overplotting.  
* You can think of "jittering" as shaking the points around a bit on the plot. 

* To create a jittered scatterplot, instead of using `geom_point`, we use `geom_jitter`. 
* To specify how much jitter to add, we adjust the `width` and `height` arguments.  
* This corresponds to how hard you'd like to shake the plot in units corresponding to those for both the horizontal and vertical variables (in this case, minutes). 
* It is important to add just enough jitter to break any overlap in points, but not so much that we completely obscure the overall pattern in points. 


```r
ggplot(data = alaska_flights, 
       mapping = aes(x = dep_delay, y = arr_delay)) + 
  geom_jitter(width = 30, height = 30)
```

<img src="figure/jitter-1.png" title="Jittered delay scatterplot" alt="Jittered delay scatterplot" width="\textwidth" />

* Observe how this function call is identical to the one in Subsection \@ref(geompoint), but with `geom_point()` replaced with `geom_jitter()`. 
* Also, it is important to note that `geom_jitter()` is strictly a visualization tool and that does not alter the original values saved in `jitter_example`.

The plot in Figure \@ref(fig:jitter) helps us a little bit in getting a sense for the overplotting, but with a relatively large data-set like this one (714 flights), it can be argued that changing the transparency of the points by setting `alpha` proved more effective. 

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.7)**  Why is setting the `alpha` argument value useful with scatterplots? What further information does it give you that a regular scatterplot cannot?

**(LC3.8)** After viewing the Figure \@ref(fig:alpha) above, give an approximate range of arrival delays and departure delays that occur the most frequently.  How has that region changed compared to when you observed the same plot without the `alpha = 0.2` set in Figure \@ref(fig:noalpha)?



<div class="learncheck">

</div>

<!--
Maybe include a shading of the points by another variable example here for multivariate thinking?
-->

### Summary

Scatterplots display the relationship between two numerical variables.  They are among the most commonly used plots because they can provide an immediate way to see the trend in one variable versus another.  However, if you try to create a scatterplot where either one of the two variables is not numerical, you will get strange results.  Be careful!

With medium to large data-sets, you may need to play with either `geom_jitter()` or the `alpha` argument in order to get a good feel for relationships in your data.  This tweaking is often a fun part of data visualization since you'll have the chance to see different relationships come about as you make subtle changes to your plots.


## 5NG#2: Linegraphs {#linegraphs}

* Most frequently used when the x-axis represents time and the y-axis represents some other numerical variable; such plots are known as *time series*.  
* Time represents a variable that is connected together by each day following the previous day.  
* In other words, time has a natural ordering.  
* Linegraphs should be avoided when there is not a clear sequential ordering to the explanatory variable, i.e. the x-variable or the *predictor* variable.

Our focus now turns to the `temp` variable in this `weather` data-set. By

* Looking over the `weather` data-set by typing `View(weather)` in the console. 
* Running `?weather` to bring up the help file.

* We can see that the `temp` variable corresponds to hourly temperature (in Fahrenheit) recordings at weather stations near airports in New York City. 
* Instead of considering all hours in 2013 for all three airports in NYC, let's focus on the hourly temperature at Newark airport (`origin` code "EWR") for the first 15 days in January 2013. 
* The `weather` data frame in the `nycflights13` package contains this data, but we first need to filter it to only include those rows that correspond to Newark in the first 15 days of January.


```r
early_january_weather <- weather %>% 
  filter(origin == "EWR" & month == 1 & day <= 15)
```

* This is similar to the previous use of the `filter` command in Section \@ref(scatterplots), however we now use the `&` operator. The above selects only those rows in `weather` where the originating airport is `"EWR"` **and** we are in the first month **and** the day is from 1 to 15 inclusive.  
     

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.9)** Take a look at both the `weather` and `early_january_weather` data frames by running `View(weather)` and `View(early_january_weather)` in the console. In what respect do these data frames differ?

**(LC3.10)** `View()` the `flights` data frame again. Why does the `time_hour` variable uniquely identify the hour of the measurement whereas the `hour` variable does not? 



<div class="learncheck">

</div>

### Linegraphs via geom_line {#geomline}

We plot a linegraph of hourly temperature using `geom_line()`:


```r
ggplot(data = early_january_weather, 
       mapping = aes(x = time_hour, y = temp)) +
  geom_line()
```

<img src="figure/hourlytemp-1.png" title="Hourly Temperature in Newark for January 1-15, 2013" alt="Hourly Temperature in Newark for January 1-15, 2013" width="\textwidth" />

Much as with the `ggplot()` call in Chapter \@ref(geompoint), we describe the components of the Grammar of Graphics:

* Within the `ggplot()` function call, we specify two of the components of the grammar:
    1. The `data` frame to be `early_january_weather` by setting `data = early_january_weather`
    1. The `aes`thetic mapping by setting `aes(x = time_hour, y = temp)`. Specifically
        * `time_hour` (i.e. the time variable) maps to the `x` position
        * `temp` maps to the `y` position
* We add a layer to the `ggplot()` function call using the `+` sign
* The layer in question specifies the third component of the grammar:  the `geom`etric object in question. In this case the geometric object is a `line`, set by specifying `geom_line()`. 


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.11)**  Why should linegraphs be avoided when there is not a clear ordering of the horizontal axis?

**(LC3.12)** Why are linegraphs frequently used when time is the explanatory variable?

**(LC3.13)** Plot a time series of a variable other than `temp` for Newark Airport in the first 15 days of January 2013.





<div class="learncheck">

</div>

### Summary

Linegraphs, just like scatterplots, display the relationship between two numerical variables. However, the variable on the x-axis (i.e. the explanatory variable) should have a natural ordering, like some notion of time.  We can mislead our audience if that isn't the case.

## 5NG#3: Histograms {#histograms}

* Let's consider the `temp` variable in the `weather` data frame once again, but now unlike with the linegraphs, * let's say we don't care about the relationship of temperature to time, but rather we care about the *(statistical) distribution* of temperatures. 
* We could produce points where each of the different values appear on something similar to a number line:

<img src="figure/unnamed-chunk-16-1.png" title="Plot of Hourly Temperature Recordings from NYC in 2013" alt="Plot of Hourly Temperature Recordings from NYC in 2013" width="\textwidth" />

* This gives us a general idea of how the values of `temp` differ.  
* We see that temperatures vary from around 11 up to 100 degrees Fahrenheit.  
* The area between 40 and 60 degrees appears to have more points plotted than outside that range.

### Histograms via geom_histogram {#geomhistogram}

What is commonly produced instead of the above plot is a plot known as a *histogram*.  The histogram shows how many elements of a single numerical variable fall in specified *bins*.  In this case, these bins may correspond to between 0-10&deg;F, 10-20&deg;F, etc. We produce a histogram of the hour temperatures at all three NYC airports in 2013:


```r
ggplot(data = weather, mapping = aes(x = temp)) +
  geom_histogram()
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

```
## Warning: Removed 1 rows containing non-finite values (stat_bin).
```

<img src="figure/unnamed-chunk-17-1.png" title="Histogram of Hourly Temperature Recordings from NYC in 2013" alt="Histogram of Hourly Temperature Recordings from NYC in 2013" width="\textwidth" />

Note here:

* There is only one variable being mapped in `aes()`: the single numerical variable `temp`. You don't need to compute the y-aesthetic: it gets computed automatically.
* We set the `geom`etric object to be `geom_histogram()`
* We got a warning message of `1 rows containing non-finite values` being removed. This is due to one of the values of temperature being missing. R is alerting us that this happened.  
* Another warning corresponds to an urge to specify the number of bins you'd like to create.

### Adjusting the bins {#adjustbins}

We can adjust characteristics of the bins in one of *two* ways:

1. By adjusting the number of bins via the `bins` argument
1. By adjusting the width of the bins via the `binwidth` argument

First, we have the power to specify how many bins we would like to put the data into as an argument in the `geom_histogram()` function.  By default, this is chosen to be 30 somewhat arbitrarily; we have received a warning above our plot that this was done.


```r
ggplot(data = weather, mapping = aes(x = temp)) +
  geom_histogram(bins = 60, color = "white")
```

<img src="figure/unnamed-chunk-18-1.png" title="Histogram of Hourly Temperature Recordings from NYC in 2013 - 60 Bins" alt="Histogram of Hourly Temperature Recordings from NYC in 2013 - 60 Bins" width="\textwidth" />

Note the addition of the `color` argument.  If you'd like to be able to more easily differentiate each of the bins, you can specify the color of the outline as done above. You can also adjust the color of the bars by setting the `fill` argument. Type `colors()` in your console to see all 657 available colors.
  

```r
ggplot(data = weather, mapping = aes(x = temp)) +
  geom_histogram(bins = 60, color = "white", fill = "steelblue")
```

<img src="figure/unnamed-chunk-19-1.png" title="Histogram of Hourly Temperature Recordings from NYC in 2013 - 60 Colored Bins" alt="Histogram of Hourly Temperature Recordings from NYC in 2013 - 60 Colored Bins" width="\textwidth" />

Second, instead of specifying the number of bins, we can also specify the width of the bins by using the `binwidth` argument in the `geom_histogram` function. 


```r
ggplot(data = weather, mapping = aes(x = temp)) +
  geom_histogram(binwidth = 10, color = "white")
```

<img src="figure/unnamed-chunk-20-1.png" title="Histogram of Hourly Temperature Recordings from NYC in 2013 - Binwidth = 10" alt="Histogram of Hourly Temperature Recordings from NYC in 2013 - Binwidth = 10" width="\textwidth" />

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.14)** What does changing the number of bins from 30 to 60 tell us about the distribution of temperatures?

**(LC3.15)** Would you classify the distribution of temperatures as symmetric or skewed?

**(LC3.16)** What would you guess is the "center" value in this distribution?  Why did you make that choice?

**(LC3.17)** Is this data spread out greatly from the center or is it close?  Why?








<div class="learncheck">

</div>

### Summary

Histograms, unlike scatterplots and linegraphs, present information on only a single numerical variable. In particular they are visualizations of the (statistical) distribution of values.

## Facets {#facets}

Before continuing the 5NG, we briefly introduce a new concept called *faceting*.  Faceting is used when we'd like to create small multiples of the same plot over a different categorical variable.  By default, all of the small multiples will have the same vertical axis. 

For example, suppose we were interested in looking at how the temperature histograms we saw in Chapter \@ref(histograms) varied by month.  This is what is meant by "the distribution of a variable over another variable": `temp` is one variable and `month` is the other variable. In order to look at histograms of `temp` for each month, we add a layer `facet_wrap(~ month)`.  You can also specify how many rows you'd like the small multiple plots to be in using `nrow` or how many columns using `ncol` inside of `facet_wrap`.


```r
ggplot(data = weather, mapping = aes(x = temp)) +
  geom_histogram(binwidth = 5, color = "white") +
  facet_wrap(~ month, nrow = 4)
```

<img src="figure/facethistogram-1.png" title="Faceted histogram" alt="Faceted histogram" width="\textwidth" />

Note the use of the `~` before `month` in `facet_wrap`.  The tilde (`~`) is required and you'll receive the error `Error in as.quoted(facets) : object 'month' not found` if you don't include it before `month` here.

As we might expect, the temperature tends to increase as summer approaches and then decrease as winter approaches.


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.18)** What other things do you notice about the faceted plot above?  How does a faceted plot help us see relationships between two variables?

**(LC3.19)** What do the numbers 1-12 correspond to in the plot above?  What about 25, 50, 75, 100?

**(LC3.20)** For which types of data-sets would these types of faceted plots not work well in comparing relationships between variables? Give an example describing the nature of these variables and other important characteristics.

**(LC3.21)** Does the `temp` variable in the `weather` data-set have a lot of variability?  Why do you say that?



<div class="learncheck">

</div>


## 5NG#4: Boxplots {#boxplots}

* faceted histograms can provide a way to compare distributions of a numerical variable split by groups of a categorical variable as in Section \@ref(facets), an alternative plot called a *boxplot* (also called a *side-by-side boxplot*) achieves the same task and is frequently preferred.  
* The *boxplot* uses the information provided in the *five-number summary*.  It gives a way to compare this summary information across the different levels of a categorical variable.  

### Boxplots via geom_boxplot {#geomboxplot}

Let's create a boxplot to compare the monthly temperatures as we did above with the faceted histograms.


```r
ggplot(data = weather, mapping = aes(x = month, y = temp)) +
  geom_boxplot()
```

<img src="figure/badbox-1.png" title="Invalid boxplot specification" alt="Invalid boxplot specification" width="\textwidth" />

```
Warning messages:
1: Continuous x aesthetic -- did you forget aes(group=...)? 
2: Removed 1 rows containing non-finite values (stat_boxplot). 
```

* Note the set of warnings that is given here. 
* The second warning corresponds to missing values in the data frame and it is turned off on subsequent plots. 
* Let's focus on the first warning. 

* Observe that this plot does not look like what we were expecting.  
* We were expecting to see the distribution of temperatures for each month (so 12 different boxplots).  
* The first warning is letting us know that we are plotting a numerical, and not categorical variable, on the x-axis. This gives us the overall boxplot without any other groupings.  
* We can get around this by introducing a new function for our `x` variable:


```r
ggplot(data = weather, mapping = aes(x = factor(month), y = temp)) +
  geom_boxplot()
```

<img src="figure/monthtempbox-1.png" title="Month by temp boxplot" alt="Month by temp boxplot" width="\textwidth" />

* We have introduced a new function called `factor()` which converts a numerical variable to a categorical one. 
* This is necessary as `geom_boxplot` requires the `x` variable to be a categorical variable, which the variable `month` is not. So after applying `factor(month)`, month goes from having numerical values 1, 2, ..., 12 to having labels "1", "2", ..., "12." The resulting Figure \@ref(fig:monthtempbox) shows 12 separate "box and whiskers" plots with the following features:

* The "box" portions of this plot represent the 25^th^ percentile AKA the 1^st^ quartile, the median AKA the 50^th^ percentile AKA the 2^nd^ quartile, and the 75^th^ percentile AKA the 3^rd^ quartile.
* The height of each box, i.e. the value of the 3^rd^ quartile minus the value of the 1^st^ quartile, is called the *interquartile range* ($IQR$). It is a measure of spread of the middle 50% of values, with longer boxes indicating more variability.
* The "whisker" portions of these plots extend out from the bottoms and tops of the boxes and represent points less than the 25^th^ percentile and greater than the 75^th^ percentiles respectively. They're set to extend out no more than $1.5 \times IQR$ units away from either end of the boxes. We say "no more than" because the ends of the whiskers represent the first observed values of `temp` to be within the range of the whiskers. The length of these whiskers show how the data outside the middle 50% of values vary, with longer whiskers indicating more variability.
* The dots representing values falling outside the whiskers are called *outliers*. It is important to keep in mind that the definition of an outlier is somewhat arbitrary and not absolute. In this case, they are defined by the length of the whiskers, which are no more than $1.5 \times IQR$ units long.

Looking at this plot we can see, as expected, that summer months (6 through 8) have higher median temperatures as evidenced by the higher solid lines in the middle of the boxes. We can easily compare temperatures across months by drawing imaginary horizontal lines across the plot. Furthermore, the height of the 12 boxes as quantified by the interquartile ranges are informative too; they tell us about variability, or spread, of temperatures recorded in a given month. 

![boxplot image](images/box_plot.jpg)

#### skip forward to barplots or go on to next lesson
---

But to really bring home what boxplots show, let's focus only on the month of November's 2141 temperature recordings.

<img src="figure/monthtempbox2-1.png" title="November boxplot" alt="November boxplot" width="\textwidth" />

Now let's plot all 2141 temperature recordings for November on top of the boxplot in Figure \@ref(fig:monthtempbox3). 

<img src="figure/monthtempbox3-1.png" title="November boxplot with points" alt="November boxplot with points" width="\textwidth" />

What the boxplot does is summarize the 2141 points for you, in particular:
  
1. 25% of points (about 534 observations) fall below the bottom edge of the box which is the first quartile of 35.96 degrees Fahrenheit (2.2 degrees Celsius). In other words 25% of observations were colder than 35.96 degrees Fahrenheit.
1. 25% of points fall between the bottom edge of the box and the solid middle line which is the median of 44.96 degrees Fahrenheit (7.8 degrees Celsius). In other words 25% of observations were between 35.96 and 44.96 degrees Fahrenheit.
1. 25% of points fall between the solid middle line and the top edge of the box which is the third quartile of 51.98 degrees Fahrenheit (11.1 degrees Celsius). In other words 25% of observations were between 44.96 and 51.98 degrees Fahrenheit.
1. 25% of points fall over the top edge of the box. In other words 25% of observations were warmer than 51.98 degrees Fahrenheit.
1. The middle 50% of points lie within the interquartile range 16.02 degrees Fahrenheit.

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.22)** What does the dot at the bottom of the plot for May correspond to?  Explain what might have occurred in May to produce this point.

**(LC3.23)** Which months have the highest variability in temperature?  What reasons do you think this is?

**(LC3.24)** We looked at the distribution of a numerical variable over a categorical variable here with this boxplot.  Why can't we look at the distribution of one numerical variable over the distribution of another numerical variable?  Say, temperature across pressure, for example?

**(LC3.25)** Boxplots provide a simple way to identify outliers.  Why may outliers be easier to identify when looking at a boxplot instead of a faceted histogram?














<div class="learncheck">

</div>

### Summary

Boxplots provide a way to compare and contrast the distribution of one quantitative variable across multiple levels of one categorical variable. One can see where the median falls across the different groups by looking at the center line in the box. To see how spread out the variable is across the different groups, look at both the width of the box and also how far the lines stretch vertically from the box. (If the lines stretch far from the box but the box has a small width, the variability of the values closer to the center is much smaller than the variability of the outer ends of the variable.) Outliers are even more easily identified when looking at a boxplot than when looking at a histogram.

## 5NG#5: Barplots {#geombar}

### Barplots via geom_bar/geom_col

Let's generate barplots using these two different representations of the same basket of fruit: 3 apples and 2 oranges. Using the not pre-counted data `fruits` from Table \@ref(tab:fruits):

Stating the above differently:

* When the categorical variable you want to plot is not pre-counted in your data frame you need to use `geom_bar()`.
* When the categorical variable is pre-counted (in the above `fruits_counted` example in the variable `number`), you need to use `geom_col()` with the `y` aesthetic explicitly mapped.

Please note that understanding this difference is one of `ggplot2`'s trickier aspects that causes the most confusion, and fortunately this is as complicated as our use of `ggplot2` is going to get. Let's consider a different distribution: the distribution of airlines that flew out of New York City in 2013.  Here we explore the number of flights from each airline/`carrier`.  This can be plotted by invoking the `geom_bar` function in `ggplot2`:

(ref:geombar) Number of flights departing NYC in 2013 by airline using geom_bar


```r
ggplot(data = flights, mapping = aes(x = carrier)) +
  geom_bar()
```

<img src="figure/flightsbar-1.png" title="(ref:geombar)" alt="(ref:geombar)" width="\textwidth" />

To get an understanding of what the names of these airlines are corresponding to these `carrier` codes, we can look at the `airlines` data frame in the `nycflights13` package.  


```r
airlines
```
<table class="table" style="font-size: 16px; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> carrier </th>
   <th style="text-align:left;"> name </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 9E </td>
   <td style="text-align:left;"> Endeavor Air Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> AA </td>
   <td style="text-align:left;"> American Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> AS </td>
   <td style="text-align:left;"> Alaska Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B6 </td>
   <td style="text-align:left;"> JetBlue Airways </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DL </td>
   <td style="text-align:left;"> Delta Air Lines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> EV </td>
   <td style="text-align:left;"> ExpressJet Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> F9 </td>
   <td style="text-align:left;"> Frontier Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> FL </td>
   <td style="text-align:left;"> AirTran Airways Corporation </td>
  </tr>
  <tr>
   <td style="text-align:left;"> HA </td>
   <td style="text-align:left;"> Hawaiian Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> MQ </td>
   <td style="text-align:left;"> Envoy Air </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OO </td>
   <td style="text-align:left;"> SkyWest Airlines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UA </td>
   <td style="text-align:left;"> United Air Lines Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> US </td>
   <td style="text-align:left;"> US Airways Inc. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> VX </td>
   <td style="text-align:left;"> Virgin America </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WN </td>
   <td style="text-align:left;"> Southwest Airlines Co. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> YV </td>
   <td style="text-align:left;"> Mesa Airlines Inc. </td>
  </tr>
</tbody>
</table>

Going back to our barplot, we see that United Air Lines, JetBlue Airways, and ExpressJet Airlines had the most flights depart New York City in 2013.  To get the actual number of flights by each airline we can use the `group_by()`, `summarize()`, and `n()` functions in the `dplyr` package on the `carrier` variable in `flights`, which we will introduce formally in Chapter \@ref(wrangling).


```r
flights_table <- flights %>% 
  group_by(carrier) %>% 
  summarize(number = n())
flights_table
```
<table class="table" style="font-size: 16px; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> carrier </th>
   <th style="text-align:right;"> number </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 9E </td>
   <td style="text-align:right;"> 18460 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> AA </td>
   <td style="text-align:right;"> 32729 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> AS </td>
   <td style="text-align:right;"> 714 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B6 </td>
   <td style="text-align:right;"> 54635 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> DL </td>
   <td style="text-align:right;"> 48110 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> EV </td>
   <td style="text-align:right;"> 54173 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> F9 </td>
   <td style="text-align:right;"> 685 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> FL </td>
   <td style="text-align:right;"> 3260 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> HA </td>
   <td style="text-align:right;"> 342 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> MQ </td>
   <td style="text-align:right;"> 26397 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OO </td>
   <td style="text-align:right;"> 32 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UA </td>
   <td style="text-align:right;"> 58665 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> US </td>
   <td style="text-align:right;"> 20536 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> VX </td>
   <td style="text-align:right;"> 5162 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WN </td>
   <td style="text-align:right;"> 12275 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> YV </td>
   <td style="text-align:right;"> 601 </td>
  </tr>
</tbody>
</table>

In this table, the counts of the carriers are pre-counted. To create a barplot using the data frame `flights_table`, we

* use `geom_col()` instead of `geom_bar()`
* map the `y` aesthetic to the variable `number`.

Compare this barplot using `geom_col` in Figure \@ref(fig:flightscol) with the earlier barplot using `geom_bar` in Figure \@ref(fig:flightsbar). They are identical. However the input data we used for these are different. 

(ref:geomcol) Number of flights departing NYC in 2013 by airline using geom_col


```r
ggplot(data = flights_table, mapping = aes(x = carrier, y = number)) +
  geom_col()
```

<img src="figure/flightscol-1.png" title="(ref:geomcol)" alt="(ref:geomcol)" width="\textwidth" />


<!--
**Technical note**: Refer to the use of `::` in both lines of code above.  This is another way of ensuring the correct function is called.  A `count` exists in a couple different packages and sometimes you'll receive strange errors when a different instance of a function is used.  This is a great way of telling R that "I want this one!".  You specify the name of the package directly before the `::` and then the name of the function immediately after `::`.
-->

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.26)** Why are histograms inappropriate for visualizing categorical variables?

**(LC3.27)** What is the difference between histograms and barplots?

**(LC3.28)** How many Envoy Air flights departed NYC in 2013?

**(LC3.29)** What was the seventh highest airline in terms of departed flights from NYC in 2013? How could we better present the table to get this answer quickly?




<div class="learncheck">

</div>

### Must avoid pie charts!

Unfortunately, one of the most common plots seen today for categorical data is the pie chart.  While they may seem harmless enough, they actually present a problem in that humans are unable to judge angles well.  As Naomi Robbins describes in her book "Creating More Effective Graphs" [@robbins2013], we overestimate angles greater than 90 degrees and we underestimate angles less than 90 degrees.  In other words, it is difficult for us to determine relative size of one piece of the pie compared to another.  

Let's examine our previous barplot example on the number of flights departing NYC by airline.  This time we will use a pie chart.  As you review this chart, try to identify 

- how much larger the portion of the pie is for ExpressJet Airlines (`EV`)  compared to US Airways (`US`), 
- what the third largest carrier is in terms of departing flights, and
- how many carriers have fewer flights than United Airlines (`UA`)?


<img src="figure/carrierpie-1.png" title="The dreaded pie chart" alt="The dreaded pie chart" width="\textwidth" />

While it is quite easy to look back at the barplot to get the answer to these questions, it's quite difficult to get the answers correct when looking at the pie graph.  Barplots can always present the information in a way that is easier for the eye to determine relative position.  There may be one exception from Nathan Yau at [FlowingData.com][fd] but we will leave this for the reader to decide:

[fd]: https://flowingdata.com/2008/09/19/pie-i-have-eaten-and-pie-i-have-not-eaten/  "Pie I Have Eaten and Pie I Have Not Eaten"

<img src="images/Pie-I-have-Eaten.jpg" title="The only good pie chart" alt="The only good pie chart" width="\textwidth" style="display: block; margin: auto;" />


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.30)** Why should pie charts be avoided and replaced by barplots?

**(LC3.31)** What is your opinion as to why pie charts continue to be used?



<div class="learncheck">

</div>

### Using barplots to compare two categorical variables

Barplots are the go-to way to visualize the frequency of different categories of a categorical variable. They make it easy to order the counts and to compare the frequencies of one group to another. Another use of barplots (unfortunately, sometimes inappropriately and confusingly) is to compare two categorical variables together.  Let's examine the distribution of outgoing flights from NYC by `carrier` and `airport`.

We begin by getting the names of the airports in NYC that were included in the `flights` data-set. Here, we preview the `inner_join()` function from Chapter \@ref(wrangling). This function will join the data frame `flights` with the data frame `airports` by matching rows that have the same airport code. However, in `flights` the airport code is included in the `origin` variable whereas in `airports` the airport code is included in the `faa` variable. We will revisit such examples in Section \@ref(joins) on joining data-sets.



```r
flights_namedports <- flights %>% 
  inner_join(airports, by = c("origin" = "faa"))
```

After running `View(flights_namedports)`, we see that `name` now corresponds to the name of the airport as referenced by the `origin` variable.  We will now plot `carrier` as the horizontal variable.  When we specify `geom_bar`, it will specify `count` as being the vertical variable.  A new addition here is `fill = name`.  Look over what was produced from the plot to get an idea of what this argument gives.


```r
ggplot(data = flights_namedports, 
       mapping = aes(x = carrier, fill = name)) +
  geom_bar()
```

<img src="figure/unnamed-chunk-39-1.png" title="Stacked barplot comparing the number of flights by carrier and airport" alt="Stacked barplot comparing the number of flights by carrier and airport" width="\textwidth" />

This plot is what is known as a *stacked barplot*.  While simple to make, it often leads to many problems. For example in this plot, it is difficult to compare the heights of the different colors (corresponding to the number of flights from each airport) between the bars (corresponding to the different carriers).

Note that `fill` is an `aes`thetic just like `x` is an `aes`thetic, and thus must be included within the parentheses of the `aes()` mapping. The following code, where the `fill` `aes`thetic is specified on the outside will yield an error. This is a fairly common error that new `ggplot` users make:


```r
ggplot(data = flights_namedports, 
       mapping = aes(x = carrier), fill = name) +
  geom_bar()
```

<div class="learncheck">
**_Learning check_**
</div>

**(LC3.32)** What kinds of questions are not easily answered by looking at the above figure?

**(LC3.33)** What can you say, if anything, about the relationship between airline and airport in NYC in 2013 in regards to the number of departing flights?



<div class="learncheck">

</div>

Another variation on the stacked barplot is the *side-by-side barplot* also called a *dodged barplot*.


```r
ggplot(data = flights_namedports, 
       mapping = aes(x = carrier, fill = name)) +
  geom_bar(position = "dodge")
```

<img src="figure/unnamed-chunk-42-1.png" title="Side-by-side AKA dodged barplot comparing the number of flights by carrier and airport" alt="Side-by-side AKA dodged barplot comparing the number of flights by carrier and airport" width="\textwidth" />


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.34)** Why might the side-by-side (AKA dodged) barplot be preferable to a stacked barplot in this case?

**(LC3.35)** What are the disadvantages of using a side-by-side (AKA dodged) barplot, in general?


<div class="learncheck">

</div>

Lastly, an often preferred type of barplot is the *faceted barplot*.  We already saw this concept of faceting and small multiples in Section \@ref(facets).  This gives us a nicer way to compare the distributions across both `carrier` and airport/`name`.


```r
ggplot(data = flights_namedports, 
       mapping = aes(x = carrier, fill = name)) +
  geom_bar() +
  facet_wrap(~ name, ncol = 1)
```

<img src="figure/facet-bar-vert-1.png" title="Faceted barplot comparing the number of flights by carrier and airport" alt="Faceted barplot comparing the number of flights by carrier and airport" width="\textwidth" />


<div class="learncheck">
**_Learning check_**
</div>

**(LC3.36)** Why is the faceted barplot preferred to the side-by-side and stacked barplots in this case?

**(LC3.37)** What information about the different carriers at different airports is more easily seen in the faceted barplot?



<div class="learncheck">

</div>



### Summary

Barplots are the preferred way of displaying categorical variables.  They are easy-to-understand and make it easy to compare across groups of a categorical variable.  When dealing with more than one categorical variable, faceted barplots are frequently preferred over side-by-side or stacked barplots.  Stacked barplots are sometimes nice to look at, but it is quite difficult to compare across the levels since the sizes of the bars are all of different sizes.  Side-by-side barplots can provide an improvement on this, but the issue about comparing across groups still must be dealt with.




## Conclusion

### Putting it all together

Let's recap all five of the Five Named Graphs (5NG) in Table \@ref(tab:viz-summary-table) summarizing their differences. Using these 5NG, you'll be able to visualize the distributions and relationships of variables contained in a wide array of datasets. This will be even more the case as we start to map more variables to more of each `geom`etric object's `aes`thetic attribute options, further unlocking the awesome power of the `ggplot2` package.

<table>
<caption>Summary of 5NG</caption>
 <thead>
  <tr>
   <th style="text-align:right;">   </th>
   <th style="text-align:left;"> Named graph </th>
   <th style="text-align:left;"> Shows </th>
   <th style="text-align:left;"> Geometric object </th>
   <th style="text-align:left;"> Notes </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> Scatterplot </td>
   <td style="text-align:left;"> Relationship between 2 numerical variables </td>
   <td style="text-align:left;"> `geom_point()` </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> Linegraph </td>
   <td style="text-align:left;"> Relationship between 2 numerical variables </td>
   <td style="text-align:left;"> `geom_line()` </td>
   <td style="text-align:left;"> Used when there is a sequential order to x-variable e.g. time </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> Histogram </td>
   <td style="text-align:left;"> Distribution of 1 numerical variable </td>
   <td style="text-align:left;"> `geom_histogram()` </td>
   <td style="text-align:left;"> Facetted histogram shows distribution of 1 numerical variable split by 1 categorical variable </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> Boxplot </td>
   <td style="text-align:left;"> Distribution of 1 numerical variable split by 1 categorical variable </td>
   <td style="text-align:left;"> `geom_boxplot()` </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> Barplot </td>
   <td style="text-align:left;"> Distribution of 1 categorical variable </td>
   <td style="text-align:left;"> `geom_bar()` when counts are not pre-counted </td>
   <td style="text-align:left;"> Stacked &amp; dodged barplots show distribution of 2 categorical variables </td>
  </tr>
  <tr>
   <td style="text-align:right;">  </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> `geom_col()` when counts are pre-counted </td>
   <td style="text-align:left;">  </td>
  </tr>
</tbody>
</table>

### Review questions

Review questions have been designed using the [`fivethirtyeight` R package](https://rudeboybert.github.io/fivethirtyeight/) [@R-fivethirtyeight] with links to the corresponding FiveThirtyEight.com articles in our free DataCamp course **Effective Data Storytelling using the `tidyverse`**.  The material in this chapter is covered in the chapters of the DataCamp course available below:

* [Scatterplots & Linegraphs](https://campus.datacamp.com/courses/effective-data-storytelling-using-the-tidyverse-free/17581?ex=1)
* [Histograms & Boxplots](https://campus.datacamp.com/courses/effective-data-storytelling-using-the-tidyverse-free/17582?ex=1)
* [Barplots](https://campus.datacamp.com/courses/effective-data-storytelling-using-the-tidyverse-free/17583?ex=1)
* [ggplot2 Review](https://campus.datacamp.com/courses/effective-data-storytelling-using-the-tidyverse-free/17584?ex=1)

### What's to come?

In Chapter \@ref(tidy), we'll introduce the concept of "tidy data" and how it is used as a key data format for all the packages we use in this textbook.  You'll see that the concept appears to be simple, but actually can be a little challenging to decipher without careful practice. We'll also investigate how to import CSV (comma-separated value) files into R using the `readr` package.  

### Resources

An excellent resource as you begin to create plots using the `ggplot2` package is a cheatsheet that RStudio has put together entitled "Data Visualization with ggplot2" available 

* by clicking [here](https://www.rstudio.com/wp-content/uploads/2016/11/ggplot2-cheatsheet-2.1.pdf) or
* by clicking the RStudio Menu Bar -> Help -> Cheatsheets -> "Data Visualization with `ggplot2`"

This cheatsheet covers more than what we've discussed in this chapter but provides nice visual descriptions of what each function produces.


<!--
Fix this later
-->

<!--
In addition, we've created a mind map to help you remember which types of plots are most appropriate in a given situation by identifying the types of variables involved in the problem.  It is available [here](https://coggle.it/diagram/V_G2gzukTDoQ-aZt-) and below.
-->







### Script of R code

An R script file of all R code used in this chapter is available [here](scripts/03-visualization.R).





