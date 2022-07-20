---
title: "Data Visualisation with ggplot2"
teaching: 80
exercises: 35
questions:
- What are the components of a ggplot?
- How do I create scatterplots, boxplots, histograms, and more?
- How can I change the aesthetics (ex. colour, transparency) of my plot?
- How can I create multiple plots at once?
objectives:
- Produce scatter plots, boxplots, and time series plots using ggplot.
- Set universal plot settings.
- Describe what faceting is and apply faceting in ggplot.
- Modify the aesthetics of an existing ggplot plot (including axis labels and color).
- Build complex and customized plots from data in a data frame.
keypoints:
- ggplot2 is a flexible and useful tool for creating plots in R.
- The data set and coordinate system can be defined using the ggplot function.
- Additional layers, including geoms, are added using the + operator.
- Boxplots are useful for visualizing the distribution of a continuous variable.
- Barplots are useful for visualizing categorical data.
- Faceting allows you to generate multiple plots based on a categorical variable.
---

We start by loading the required packages. `ggplot2` is included in the `tidyverse` package.

~~~
library(tidyverse)
library(palmerpenguins)
~~~
{: .language-r}

If not still in the workspace, load the data we saved in the previous lesson.

~~~
penguins <- read_csv(path_to_file("penguins.csv"))
~~~
{: .language-r}

## Plotting with ggplot2

`ggplot2` is a plotting package that provides helpful commands to create complex plots from data in a data frame. It provides a more programmatic interface for specifying what variables to plot, how they are displayed, and general visual properties. Therefore, we only need minimal changes if the underlying data change or if we decide to change from a bar plot to a scatterplot. This helps in creating publication quality plots with minimal amounts of adjustments and tweaking.

`ggplot2` refers to the name of the package itself. When using the package we use the function `ggplot()` to generate the plots, and so references to using the function will be referred to as `ggplot()` and the package as a whole as `ggplot2`

`ggplot2` plots work best with data in the ‘long’ format, i.e., a column for every variable, and a row for every observation. Well-structured data will save you lots of time when making figures with `ggplot2`

ggplot graphics are built layer by layer by adding new elements. Adding layers in this fashion allows for extensive flexibility and customization of plots.

To build a ggplot, we will use the following basic template that can be used for different types of plots:

~~~
# this will not run
ggplot(data = <DATA>, mapping = aes(<MAPPINGS>)) +  <GEOM_FUNCTION>()
~~~
{: .language-r}

* use the `ggplot()` function and bind the plot to a specific data frame using the data argument

~~~
ggplot(data = penguins)
~~~
{: .language-r}

* define an aesthetic mapping (using the aesthetic (aes) function), by selecting the variables to be plotted and specifying how to present them in the graph, e.g., as x/y positions or characteristics such as size, shape, color, etc.

~~~
ggplot(data = penguins, mapping = aes(x = body_mass_g, y = flipper_length_mm))
~~~
{: .language-r}

* add ‘geoms’ – graphical representations of the data in the plot (points, lines, bars). `ggplot2` offers many different geoms; we will use some common ones today, including:
  * `geom_point()` for scatter plots, dot plots, etc.
  * `geom_boxplot()` for, well, boxplots!
  * `geom_line()` for trend lines, time series, etc.

To add a geom to the plot use `+` operator. Because we have two continuous variables, let’s use `geom_point()` first:

~~~
ggplot(data = penguins, aes(x = body_mass_g, y = flipper_length_mm)) +
  geom_point()
~~~
{: .language-r}

The + in the `ggplot2` package is particularly useful because it allows you to modify existing ggplot objects. This means you can easily set up plot “templates” and conveniently explore different types of plots, so the above plot can also be generated with code like this:

~~~
# Assign plot to a variable
penguins_plot <- ggplot(data = penguins,
                       mapping = aes(x = body_mass_g, y = flipper_length_mm))

# Draw the plot
penguins_plot +
  geom_point()
~~~
{: .language-r}

**Notes**

* Anything you put in the `ggplot()` function can be seen by any geom layers that you add (i.e., these are universal plot settings). This includes the x- and y-axis you set up in aes().
* You can also specify aesthetics for a given geom independently of the aesthetics defined globally in the `ggplot()` function.
* The + sign used to add layers must be placed at the end of each line containing a layer. If, instead, the + sign is added in the line before the other layer, `ggplot2` will not add the new layer and will return an error message.
* You may notice that we sometimes reference ‘`ggplot2`’ and sometimes ‘ggplot’. To clarify, ‘`ggplot2`’ is the name of the most recent version of the package. However, any time we call the function itself, it’s just called ‘ggplot’.
* The previous version of the `ggplot2` package, called ggplot, which also contained the `ggplot()` function is now unsupported and has been removed from CRAN in order to reduce accidental installations and further confusion.

~~~
# This is the correct syntax for adding layers
penguins_plot +
  geom_point()

# This will not add the new layer and will return an error message
penguins_plot
+ geom_point()
~~~
{: .language-r}

> ## Challenge
> Scatter plots can be useful exploratory tools for small datasets. 
> For data sets with large numbers of observations, such as the surveys_complete data set, overplotting of points can be a limitation of scatter plots. 
> One strategy for handling such settings is to use hexagonal binning of observations. 
> The plot space is tessellated into hexagons. 
> Each hexagon is assigned a color based on the number of observations that fall within its boundaries. 
> To use hexagonal binning with `ggplot2`, first install the R package hexbin from CRAN:
> ~~~
> install.packages("hexbin")
> library(hexbin)
> ~~~
> {: .language-r}
> 
> Then use the `geom_hex()` function:
> ~~~
> penguins_plot +
>   geom_hex()
> ~~~
> {: .language-r}
> What are the relative strengths and weaknesses of a hexagonal bin plot compared to a scatter plot? 
> Examine the above scatter plot and compare it with the hexagonal bin plot that you created.
> 
{: .challenge}

## Building your plots iteratively

Building plots with `ggplot2` is typically an iterative process. We start by defining the dataset we’ll use, lay out the axes, and choose a geom:

~~~
ggplot(data = penguins, aes(x = body_mass_g, y = flipper_length_mm)) + geom_point()
~~~
{: .language-r}

Then, we start modifying this plot to extract more information from it. For instance, we can add transparency (alpha) to avoid overplotting:

~~~
ggplot(data = penguins, aes(x = body_mass_g, y = flipper_length_mm)) +
  geom_point(alpha = 0.1)
~~~
{: .language-r}

We can also add colors for all the points:

~~~
ggplot(data = penguins, mapping = aes(x = body_mass_g, y = flipper_length_mm)) +
  geom_point(alpha = 0.1, color = "blue")
~~~
{: .language-r}

Or to color each species in the plot differently, you could use a vector as an input to the argument color. `ggplot2` will provide a different color corresponding to different values in the vector. Here is an example where we color with species_id:

~~~
ggplot(data = penguins, mapping = aes(x = body_mass_g, y = flipper_length_mm)) +
  geom_point(alpha = 0.1, aes(color = species))
~~~
{: .language-r}

## Boxplot
We can use boxplots to visualize the distribution of weight within each species:

~~~
ggplot(data = penguins, mapping = aes(x = species, y = body_mass_g)) +
  geom_boxplot()
~~~
{: .language-r}

By adding points to the boxplot, we can have a better idea of the number of measurements and of their distribution:

~~~
ggplot(data = penguins, mapping = aes(x = species, y = body_mass_g)) +
  geom_boxplot(alpha = 0) +
  geom_jitter(alpha = 0.3, color = "tomato")
~~~
{: .language-r}

Notice how the boxplot layer is behind the jitter layer? What do you need to change in the code to put the boxplot in front of the points such that it’s not hidden?

> ## Challenges
> Boxplots are useful summaries, but hide the shape of the distribution.
> For example, if there is a bimodal distribution, it would not be observed with a boxplot. 
> An alternative to the boxplot is the violin plot (sometimes known as a beanplot), where the shape (of the density of points) is drawn.
> 
> Replace the box plot with a violin plot; see `geom_violin()`.
> In many types of data, it is important to consider the scale of the observations. 
> For example, it may be worth changing the scale of the axis to better distribute the observations in the space of the plot. 
> Changing the scale of the axes is done similarly to adding/modifying other components (i.e., by incrementally adding commands). 
> Try making these modifications:
> 
> - Represent weight on the log10 scale; see `scale_y_log10()`.
> So far, we’ve looked at the distribution of weight within species. 
> Try making a new plot to explore the distribution of another variable within each species.
> 
> - Create boxplot for hindfoot_length. 
> Overlay the boxplot layer on a jitter layer to show actual measurements.
> 
> - Add color to the data points on your boxplot according to the plot from which the sample was taken (plot_id).
> 
> Hint: Check the class for plot_id. 
> Consider changing the class of plot_id from integer to factor. 
> Why does this change how R makes the graph?
{: .challenge}

## Plotting time series data

Let’s calculate number of counts per day (in the month of November, 2008) for each species. First we need to group the data and count records within each group:

~~~
library(lubridate)
?penguins_raw
~~~
{: .language-r}

~~~
peng_raw_subset <- penguins_raw %>%
  select("Island", "Species", "Date Egg") %>%
  filter(year(penguins_raw[["Date Egg"]]) == 2008)
~~~
{: .language-r}

~~~
daily_counts <- peng_raw_subset %>%
  count(Daily, Species)

View(daily_counts)
~~~
{: .language-r}

Timelapse data can be visualized as a line plot with years on the x-axis and counts on the y-axis:

~~~
ggplot(data = daily_counts, aes(x = Daily, y = n)) +
  geom_line()
~~~
{: .language-r}

Unfortunately, this does not work because we plotted data for all the genera together. We need to tell ggplot to draw a line for each genus by modifying the aesthetic function to include group = genus:

~~~
ggplot(data = daily_counts, aes(x = Daily, y = n, group = Species)) +
  geom_line()
~~~
{: .language-r}

We will be able to distinguish genera in the plot if we add colors (using color also automatically groups the data):

~~~
ggplot(data = daily_counts, aes(x = Daily, y = n, color = Species)) +
  geom_line()
~~~
{: .language-r}

## Integrating the pipe operator with ggplot2
In the previous lesson, we saw how to use the pipe operator %>% to use different functions in a sequence and create a coherent workflow. We can also use the pipe operator to pass the data argument to the `ggplot()` function. The hard part is to remember that to build your ggplot, you need to use + and not %>%.

~~~
daily_counts %>%
  ggplot(mapping = aes(x = Daily, y = n, color = species)) +
  geom_line()
~~~
{: .language-r}

The pipe operator can also be used to link data manipulation with consequent data visualization.

~~~
daily_counts_graph <- peng_raw_subset %>%
  count(Daily, Species) %>%
  ggplot(mapping = aes(x = Daily, y = n, color = Species)) +
  geom_line()

daily_counts_graph
~~~
{: .language-r}

## Faceting
ggplot has a special technique called faceting that allows the user to split one plot into multiple plots based on a factor included in the dataset. We will use it to make a time series plot for each genus:

~~~
~~~
{: .language-r}

{% include links.md %}