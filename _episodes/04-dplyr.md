---
title: "Manipulating, analyzing and exporting data with tidyverse"
teaching: 50
exercises: 30
questions:
- "What are dplyr and tidyr?"
- "How can I select columns and filter rows?"
- "How can I use commands together"
- "How to export data?"
objectives:
- Describe the purpose of the dplyr and tidyr packages.
- Select certain columns in a data frame with the dplyr function select.
- Extract certain rows in a data frame according to logical (boolean) conditions with the dplyr function filter .
- Link the output of one dplyr function to the input of another function with the ‘pipe’ operator %>%.
- Add new columns to a data frame that are functions of existing columns with mutate.
- Use the split-apply-combine concept for data analysis.
- Use summarize, group_by, and count to split a data frame into groups of observations, apply summary statistics for each group, and then combine the results.
- Describe the concept of a wide and a long table format and for which purpose those formats are useful.
- Describe what key-value pairs are.
- Reshape a data frame from long to wide format and back with the pivot_wider and pivot_longer commands from the tidyr package.
- Export a data frame to a .csv file.
keypoints:
- Use the dplyr package to manipulate dataframes.
- Use select() to choose variables from a dataframe.
- Use filter() to choose data based on values.
- Use group_by() and summarize() to work with subsets of data.
- Use mutate() to create new variables.
- Use the tidyr package to change the layout of dataframes.
- Use pivot_wider() to go from long to wide format.
- Use pivot_longer() to go from wide to long format.
---

## Data manipulation using dplyr and tidyr

Bracket subsetting is handy, but it can be cumbersome and difficult to read, especially for complicated operations. Enter `dplyr`. `dplyr` is a package for helping with tabular data manipulation. It pairs nicely with `tidyr` which enables you to swiftly convert between different data formats for plotting and analysis.

The `tidyverse` package is an “umbrella-package” that installs `tidyr`, `dplyr`, and several other useful packages for data analysis, such as `ggplot2`, `tibble`, etc.

The `tidyverse` package tries to address 3 common issues that arise when doing data analysis in R:

1. The results from a base R function sometimes depend on the type of data.
2. R expressions are used in a non standard way, which can be confusing for new learners.
3. The existence of hidden arguments having default operations that new learners are not aware of.

You should already have installed and loaded the `tidyverse` package. If you haven’t already done so, you can type `install.packages("tidyverse")` straight into the console. Then, type `library(tidyverse)` to load the package.

~~~
library(tidyverse)
~~~
{: .language-r}

## What are dplyr and tidyr?

The package `dplyr` provides helper tools for the most common data manipulation tasks. It is built to work directly with data frames, with many common tasks optimized by being written in a compiled language (C++). An additional feature is the ability to work directly with data stored in an external database. The benefits of doing this are that the data can be managed natively in a relational database, queries can be conducted on that database, and only the results of the query are returned.

This addresses a common problem with R in that all operations are conducted in-memory and thus the amount of data you can work with is limited by available memory. The database connections essentially remove that limitation in that you can connect to a database of many hundreds of GB, conduct queries on it directly, and pull back into R only what you need for analysis.

The package `tidyr` addresses the common problem of wanting to reshape your data for plotting and usage by different R functions. For example, sometimes we want data sets where we have one row per measurement. Other times we want a data frame where each measurement type has its own column, and rows are instead more aggregated groups (e.g., a time period, an experimental unit like a plot or a batch number). Moving back and forth between these formats is non-trivial, and tidyr gives you tools for this and more sophisticated data manipulation.

To learn more about `dplyr` and `tidyr` after the workshop, you may want to check out this [handy data transformation with dplyr cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf) and this one about [tidyr](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-import.pdf).

As before, we’ll load in our data package using the `library()` and data() functions.

~~~
library(palmerpenguins)
data(penguins)
~~~
{: .language-r}

~~~
## inspect the data
str(penguins)
~~~
{: .language-r}

~~~
## preview the data
View(penguins)
~~~
{: .language-r}

Next, we’re going to learn some of the most common `dplyr` functions:

- `select()`: subset columns
- `filter()`: subset rows on conditions
- `mutate()`: create new columns by using information from other columns
- `group_by()` and `summarize()`: create summary statistics on grouped data
- `arrange()`: sort results
- `count()`: count discrete values

## Selecting columns and filtering rows

To select columns of a data frame, use `select()`. The first argument to this function is the data frame (`penguins`), and the subsequent arguments are the columns to keep.

~~~
select(penguins, island, species, body_mass_g)
~~~
{: .language-r}

To select all columns except certain ones, put a “-” in front of the variable to exclude it.

~~~
select(penguins, -year, -species)
~~~
{: .language-r}

This will select all the variables in penguins except `year` and `species`.

To choose rows based on a specific criterion, use `filter()`:

~~~
filter(penguins, year == 2007)
~~~
{: .language-r}

## Pipes

What if you want to select and filter at the same time? There are three ways to do this: use intermediate steps, nested functions, or pipes.

With intermediate steps, you create a temporary data frame and use that as input to the next function, like this:

~~~
penguins2 <- filter(penguins, body_mass_g < 3000)
penguins_sml <- select(penguins2, species, sex, body_mass_g)
~~~
{: .language-r}

This is readable, but can clutter up your workspace with lots of objects that you have to name individually. With multiple steps, that can be hard to keep track of.

You can also nest functions (i.e. one function inside of another), like this:

~~~
penguins_sml <- select(filter(penguins, body_mass_g < 3000), species, sex, body_mass_g)

~~~
{: .language-r}

This is handy, but can be difficult to read if too many functions are nested, as R evaluates the expression from the inside out (in this case, filtering, then selecting).

The last option, pipes, are a recent addition to R. Pipes let you take the output of one function and send it directly to the next, which is useful when you need to do many things to the same dataset. Pipes in R look like `%>%` and are made available via the `magrittr` package, installed automatically with dplyr. If you use RStudio, you can type the pipe with `Ctrl` + `Shift` + `M` if you have a PC or `Cmd` + `Shift` + `M` if you have a Mac.

~~~
penguins %>%
  filter(body_mass_g < 3000) %>%
  select(species, sex, body_mass_g)
~~~
{: .language-r}

In the above code, we use the pipe to send the `penguins` dataset first through `filter()` to keep rows where `body_mass_g` is less than 5, then through `select()` to keep only the `species`, `sex`, and `body_mass_g` columns. Since `%>%` takes the object on its left and passes it as the first argument to the function on its right, we don’t need to explicitly include the data frame as an argument to the `filter()` and `select()` functions any more.

Some may find it helpful to read the pipe like the word “then.” For instance, in the example above, we took the data frame `penguins`, *then* we `filter`ed for rows with `body_mass_g < 5`, then we `select`ed columns `species`, `sex`, and `body_mass_g`. The `dplyr` functions by themselves are somewhat simple, but by combining them into linear workflows with the pipe we can accomplish more complex manipulations of data frames.

If we want to create a new object with this smaller version of the data, we can assign it a new name:

~~~
penguins_sml <- penguins %>%
  filter(body_mass_g < 3000) %>%
  select(species, sex, body_mass_g)

penguins_sml
~~~
{: .language-r}

Note that the final data frame is the leftmost part of this expression.

## Mutate

Frequently you’ll want to create new columns based on the values in existing columns, for example to do unit conversions, or to find the ratio of values in two columns. For this we’ll use `mutate()`.

To create a new column that reads body mass in in kilogram:

~~~
penguins %>%
  mutate(body_mass_kg = body_mass_g / 1000)
~~~
{: .language-r}

You can also create a second new column based on the first new column within the same call of `mutate()`:

~~~
penguins %>%
  mutate(body_mass_kg = body_mass_g / 1000, 
         body_mass_lb = body_mass_kg * 2.2)
~~~
{: .language-r}

If this runs off your screen and you just want to see the first few rows, you can use a pipe to view the `head()` of the data. (Pipes work with non-`dplyr` functions, too, as long as the `dplyr` or `magrittr` package is loaded).

~~~
penguins %>%
  mutate(body_mass_kg = body_mass_g / 1000, 
         body_mass_lb = body_mass_kg * 2.2) %>%
  head()
~~~
{: .language-r}

In the first few rows of the output we see some `NA`'s, so if we wanted to remove those we could insert a `filter()` in the chain:

~~~
penguins %>%
filter(!is.na(body_mass_g)) %>%
  mutate(body_mass_kg = body_mass_g / 1000, 
         body_mass_lb = body_mass_kg * 2.2) %>%
  head()
~~~
{: .language-r}

`is.na()` is a function that determines whether something is an `NA`. The `!` symbol negates the result, so we’re asking for every row where weight is *not* an `NA`.

> ## Challenge
> 
> Create a new data frame from the `penguins` data that meets the 
> following criteria: contains only the `species` column and a new 
> column called `flipper_length_cm` containing the length of the penguin flipper values 
> (currently in mm) converted to centimeters. In this `flipper_length_cm` 
> column, there are no NAs and all values are less than 20.
> Hint: think about how the commands should be ordered to produce this data frame!
> > ## Solution
> > ~~~
> > penguins_flipper_length_cm <- penguins %>%
> >     filter(!is.na(penguins_flipper_length_mm)) %>%
> >     mutate(flipper_length_cm = penguins_flipper_length_mm / 10) %>%
> >     filter(flipper_length_cm < 20) %>%
> >     select(species, flipper_length_cm)
> > ~~~
> > {: .language-r}
> {: .solution}
{: .challenge}

## Split-apply-combine data analysis and the `summarize()` function
Many data analysis tasks can be approached using the split-apply-combine paradigm: split the data into groups, apply some analysis to each group, and then combine the results. Key functions of dplyr for this workflow are `group_by()` and `summarize()`.

The `group_by()` and `summarize()` functions
`group_by()` is often used together with `summarize()`, which collapses each group into a single-row summary of that group. `group_by()` takes as arguments the column names that contain the categorical variables for which you want to calculate the summary statistics. So to compute the mean `body_mass_g` by sex:

~~~
penguins %>%
  group_by(sex) %>%
  summarize(mean_body_mass_g = mean(body_mass_g))
~~~
{: .language-r}

You can also group by multiple columns:

~~~
penguins %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g)) 
~~~
{: .language-r}

From both outputs, we see missing data. The value `NA` appears an additional, extraneous row.
~~~
# where are the NA's coming from?
penguins %>%
  filter(is.na(sex)) 
~~~
{: .language-r}

`is.na()` is a function that determines whether something is an `NA`. The `!` symbol negates the result, so we’re asking for every row where sex is *not* an `NA`. We can pipe this filter so that we calculate the mean body mass after missing values have been removed.

~~~
# filter out observations with missing body mass measurements and unidentified sex
penguins %>%
  filter(!is.na(body_mass_g), !is.na(sex)) %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g))

penguins %>%
  filter(!is.na(body_mass_g), !is.na(sex)) %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g),
            min_body_mass_g = min(body_mass_g))
~~~
{: .language-r}

When calculating summary values, we can also look at the amount of cases that went into the calculation. The `n()` function returns the number of observations in each group.

~~~
penguins %>%
  filter(!is.na(body_mass_g), !is.na(sex)) %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g),
            min_body_mass_g = min(body_mass_g),
            num_cases = n())
~~~
{: .language-r}

To keep only rows where no attribute values are missing, we can pipe `drop_na()` to the data so that the following piped commands are implemented on complete records.

~~~
# drop all rows with any NA's
penguins %>%
  drop_na() %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g),
            min_body_mass_g = min(body_mass_g))
~~~
{: .language-r}

It is sometimes useful to rearrange the result of a query to inspect the values. For instance, we can sort on `min_body_mass_g` to put the lighter species first:

~~~
penguins %>%
  drop_na() %>%
  group_by(sex, species) %>%
  summarize(mean_body_mass_g = mean(body_mass_g),
            min_body_mass_g = min(body_mass_g)) %>%
  arrange(min_body_mass_g)
~~~
{: .language-r}

### Counting

When working with data, we often want to know the number of observations found for each factor or combination of factors. For this task, `dplyr` provides `count()`. For example, if we wanted to count the number of rows of data for each sex, we would do:

~~~
penguins %>%
    count(sex)
~~~
{: .language-r}

The `count()` function is shorthand for something we’ve already seen: grouping by a variable, and summarizing it by counting the number of observations in that group. In other words, `penguins %>% count()` is equivalent to:

~~~
penguins %>%
    group_by(sex) %>%
    summarise(count = n())
~~~
{: .language-r}

For convenience, `count()` provides the sort argument:

~~~
penguins %>%
    count(sex, sort = TRUE)
~~~
{: .language-r}

Previous example shows the use of `count()` to count the number of rows/observations for one factor (i.e., `sex`). If we wanted to count combination of factors, such as `sex` and `species`, we would specify the first and the second factor as the arguments of `count()`:

~~~
penguins %>%
  count(sex, species)
~~~
{: .language-r}

With the above code, we can proceed with `arrange()` to sort the table according to a number of criteria so that we have a better comparison. For instance, we might want to arrange the table above in (i) an alphabetical order of the levels of the species and (ii) in descending order of the count:

~~~
penguins %>%
  count(sex, species) %>%
  arrange(species, desc(n))
~~~
{: .language-r}

From the table above, we may learn that, for instance, there are 6 observations of the Adelie species and 5 observations of the Gentoo species that are not specified for its sex (i.e. NA).

> ## Challenge
> 1. How many penguins are in each island surveyed?
> 
> 2. Use group_by() and summarize() to find the mean, min, and max 
> bill length for each species (using `species`). Also add the 
> number of observations (hint: use `n()`).
> 
> 3. What was the heaviest animal measured in each year? Return the 
> columns year, island, species, and body_mass_g.
> 
> > ## Solution
> > 
> > 1.
> > ~~~
> > penguins %>%
> >    count(island)
> > ~~~
> > {: .language-r}
> > 
> > 2.
> > ~~~
> > penguins %>%
> >     filter(!is.na(bill_length_mm)) %>%
> >     group_by(species) %>%
> >     summarize(
> >         mean_bill_length_mm = mean(bill_length_mm),
> >         min_bill_length_mm = min(bill_length_mm),
> >         max_bill_length_mm = max(bill_length_mm),
> >         num_cases = n()
> >     )
> > ~~~
> > {: .language-r}
> > 
> > 3.
> > ~~~
> > penguins %>%
> >     filter(!is.na(body_mass_g)) %>%
> >     group_by(year) %>%
> >     filter(body_mass_g == max(body_mass_g)) %>%
> >     select(year, island, species, body_mass_g) %>%
> >     arrange(year)
> > ~~~
> > {: .language-r}
> > 
> {: .solution}
{: .challenge}

## Reshaping with pivot_longer and pivot_wider

In the [spreadsheet lesson](https://datacarpentry.org/spreadsheet-ecology-lesson/01-format-data/), we discussed how to structure our data leading to the four rules defining a tidy dataset:

1. Each variable has its own column
2. Each observation has its own row
3. Each value must have its own cell
4. Each type of observational unit forms a table
Here we examine the fourth rule: Each type of observational unit forms a table.

In `penguins`, the rows of `penguins` contain the values of variables associated with each record (the unit), values such as the `body_mass_g` or `sex` of each animal associated with each record. What if instead of comparing records, we wanted to compare the different mean `body_mass_g` of each `sex` between `islands`? (Ignoring `island` for simplicity).

We’d need to create a new table where each row (the unit) is comprised of values of variables associated with each `island`. In practical terms this means the values in `sex` would become the names of column variables and the cells would contain the values of the mean `body_mass_g` observed on each `island`.

Having created a new table, it is therefore straightforward to explore the relationship between the `body_mass_g` of different penguins within, and between, the `islands`. The key point here is that we are still following a tidy data structure, but we have reshaped the data according to the observations of interest: average `sex` `body_mass_g` per `island` instead of recordings per date.

The opposite transformation would be to transform column names into values of a variable.

We can do both these of transformations with two `tidyr` functions, `pivot_wider()` and `pivot_longer()`.

These may sound like dramatically different data layouts, but there are some tools that make transitions between these layouts more straightforward than you might think! The gif below shows how these two formats relate to each other, and gives you an idea of how we can use R to shift from one format to the other.

![](https://datacarpentry.org/R-ecology-lesson/img/tidyr-pivot_wider_longer.gif)

#### Pivoting from long to wide format
`pivot_wider()` takes three principal arguments:

1. the data
2. the names_from column variable whose values will become new column names.
3. the values_from column variable whose values will fill the new column variables.
Further arguments include `values_fill` which, if set, fills in missing values with the value provided.

Let’s use `pivot_wider()` to transform penguins to find the mean body_mass_g of each sex in each island over the entire survey period. We use `filter()`, `group_by()` and `summarise()` to filter our observations and variables of interest, and create a new variable for the `mean_body_mass_g`.

~~~
penguins_gw <- penguins %>%
  filter(!is.na(body_mass_g)) %>%
  group_by(island, sex) %>%
  summarize(mean_body_mass_g = mean(body_mass_g))
~~~
{: .language-r}

~~~
str(penguins_gw)
~~~
{: .language-r}

This yields `penguins_gw` where the observations for each island are distributed across multiple rows, 9 observations of 3 variables (`island`, `sex`, and `mean_body_mass_g`). Using `pivot_wider()` with the names from `sex` and with values from `mean_body_mass_g` this becomes 3 observations of 2 variables, one row for each `island` and one column for each `sex`).

~~~
penguins_wide <- penguins_gw %>%
  pivot_wider(names_from = sex, values_from = mean_body_mass_g)

str(penguins_wide)
~~~
{: .language-r}

~~~
penguins_wide
~~~
{: .language-r}

We could now compare between the `mean_body_mass_g` values between penguin `sex` and different `islands`. Note that since we did not filter `NA` values from sex, we have a third column. We can use the `rename()` function to provide this column a more descriptive name.

~~~
# fix that funny name
penguins_wide <- penguins_gw %>%
  pivot_wider(names_from = sex, 
              values_from = mean_body_mass_g) %>%
  rename(unknown = `NA`) # "unknown" is the new column name

penguins_wide
~~~
{: .language-r}

#### Pivoting from wide to long format
The opposing situation could occur if we had been provided with data in the form of `penguins_wide`, where the `sex` names are column names, but we wish to treat them as values of a `sex` variable instead.

In this situation we are reshaping the column names and turning them into a pair of new variables. One variable represents the column names as values, and the other variable contains the values previously associated with the column names.

`pivot_longer()` takes four principal arguments:

1. the data
2. the names_to column variable we wish to create from column names.
3. the values_to column variable we wish to create and fill with values.
4. cols are the name of the columns we use to make this pivot (or to drop).

To recreate `penguins_gw` from `penguins_wide` we would create a names variable called `sex` and value variable called `mean_body_mass_g`.

In pivoting longer, we also need to specify what columns to reshape. If the columns are directly adjacent as they are here, we don’t even need to list the all out: we can just use the `:` operator!

~~~
penguins_long <- penguins_wide %>%
  pivot_longer(names_to = "sex", 
               values_to = "mean_body_mass_g", 
               cols = female:unknown) #includes columns "female", "male", and "unknown"

penguins_long
~~~
{: .language-r}

Pivoting wider and then longer can be a useful way to balance out a dataset so that every replicate has the same composition.

We could also have used a specification for what columns to exclude. In this next example, we will use all columns except `island` for the names variable. By using the minus sign in the `cols` argument, we omit `island` from being reshaped

~~~
penguins_wide %>%
  pivot_longer(names_to = "sex", values_to = "mean_body_mass_g", cols = -island) %>%
  head()
~~~
{: .language-r}

> ## Challenge
> 1. Reshape the `penguins` data frame with `year` as columns, `island` as 
> rows, and the number of `species` per `island` as the values. You will need to 
> summarize before reshaping, and use the function `n_distinct()` to get 
> the number of unique species within a particular chunk of data. It’s a 
> powerful function! See `?n_distinct` for more.
> 
> 2. Now take that data frame and `pivot_longer()` it, so each row is a 
> unique `island` by year combination.
> 
> 3. The penguins data set has two measurement columns: `bill_length_mm` 
> and `body_mass_g`. This makes it difficult to do things like look at the 
> relationship between mean values of each measurement per year in 
> different `island` types. Let’s walk through a common solution for this 
> type of problem. First, use `pivot_longer()` to create a dataset where we 
> have a names column called measurement and a value column that takes on 
> the value of either `bill_length_mm` or `body_mass_g`. Hint: You’ll need to 
> specify which columns will be part of the reshape.
> 
> 4. With this new data set, calculate the average of each measurement in 
> each year for each different island. Then `pivot_wider()` them into a 
> data set with a column for `bill_length_mm` and `body_mass_g`. Hint: You only 
> need to specify the names and values columns for `pivot_wider()`.
> 
> > ## Solution
> > 1.
> > ~~~
> > penguins_wide_species <- penguins %>%
> >    group_by(island, year) %>%
> >    summarize(n_species = n_distinct(species)) %>%
> >    pivot_wider(names_from = year, values_from = n_species)
> > 
> >    head(penguins_wide_species)
> > ~~~
> > {: .language-r}
> > 
> > 2.
> > ~~~
> > penguins_wide_species %>%
> >     pivot_longer(names_to = "year", values_to = "n_species", cols = -island)
> > ~~~
> > {: .language-r}
> > 
> > 3.
> > ~~~
> > penguins_long <- penguins %>%
> >     pivot_longer(names_to = "measurement", values_to = "value", cols = c(bill_length_mm, body_mass_g))
> > ~~~
> > {: .language-r}
> > 
> > 4.
> > ~~~
> > penguins_long %>%
> >   group_by(year, measurement, island) %>%
> >   summarize(mean_value = mean(value, na.rm=TRUE)) %>%
> >   pivot_wider(names_from = measurement, values_from = mean_value)
> >   ~~~
> > {: .language-r}
> > 
> {: .solution}
{: .challenge}

## Exporting data

Now that you have learned how to use `dplyr` to extract information from or summarize your raw data, you may want to export these new data sets to share them with your collaborators or for archival.

Similar to the `read_csv()` function used for reading CSV files into R, there is a `write_csv()` function that generates CSV files from data frames.

Before using `write_csv()`, we are going to create a new folder, `data`, in our working directory that will store this generated dataset. We don’t want to write generated datasets in the same directory as our raw data. It’s good practice to keep them separate. The `data_raw` folder should only contain the raw, unaltered data, and should be left alone to make sure we don’t delete or modify it. In contrast, our script will generate the contents of the data directory, so even if the files it contains are deleted, we can always re-generate them.

In preparation for our next lesson on plotting, we are going to prepare a cleaned up version of the data set that doesn’t include any missing data.

Let’s start by removing observations of penguins for which there is an attribute that is missing or has not been determined:

~~~
# only use complete records
penguins_complete <- penguins %>%
  drop_na()

penguins_raw_complete <- penguins_raw %>%
  drop_na()
~~~
{: .language-r}

To make sure that everyone has the same data set, check that `penguins_complete` and `penguins_raw_complete` has rows and columns by using `dim()`.

Now that our data set is ready, we can save it as a CSV file in our `data` folder. We should create a data folder in our RStudio `Files` panel before exporting these.

~~~
write_csv(penguins_complete, file = "data/penguins_complete.csv")

write_csv(penguins_raw_complete, file = "data/penguins_raw_complete.csv")
~~~
{: .language-r}

In our data folder, we now have two csv's. A csv can be read into R as a dataframe using the `read_csv()` function.
We can also use `glimpse()` to see all data columns.

~~~
pcom <- read_csv("penguins_complete.csv")
glimpse(pcom)
glimpse(penguins_complete)
~~~
{: .language-r}

{% include links.md %}
