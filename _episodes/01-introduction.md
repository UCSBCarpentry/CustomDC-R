---
title: "Before we Start"
teaching: 25
exercises: 15
questions:
- "How to find your way around RStudio?"
- "How to interact with R?"
- "How to manage your environment?"
- "How to install packages?"
objectives:
- "Install latest version of R."
- "Install latest version of RStudio."
- "Navigate the RStudio GUI."
- "Install additional packages using the packages tab."
- "Install additional packages using R code."
keypoints:
- "Use RStudio to write and run R programs."
- "Use `install.packages()` to install packages (libraries)."
---
## What is R? What is RStudio?

The term "`R`" is used to refer to both the programming language and the
software that interprets the scripts written using it.

[RStudio](https://rstudio.com) is currently a very popular way to not only write
your R scripts but also to interact with the R software. To function correctly,
RStudio needs R and therefore both need to be installed on your computer.

To make it easier to interact with R, we will use RStudio. RStudio is the most
popular IDE (Integrated Development Environment) for R. An IDE is a piece of
software that provides
tools to make programming easier.

## Why learn R?

### R does not involve lots of pointing and clicking, and that's a good thing

The learning curve might be steeper than with other software, but with R, the
results of your analysis do not rely on remembering a succession of pointing
and clicking, but instead on a series of written commands, and that's a good
thing! So, if you want to redo your analysis because you collected more data,
you don't have to remember which button you clicked in which order to obtain
your results; you just have to run your script again.

Working with scripts makes the steps you used in your analysis clear, and the
code you write can be inspected by someone else who can give you feedback and
spot mistakes.

Working with scripts forces you to have a deeper understanding of what you are
doing, and facilitates your learning and comprehension of the methods you use.

![An illustrated cartoon graph with “How much I think I know about R” on the y-axis, with axis labels at “I know nothing” and “I know lots”, versus “time” on the x-axis. The line varies widely between the two. Above the line are emoji-like faces, showing uncertainty and hope early on. At a second peak is the label “join R community on twitter”, with a “mind-blown” emoji face. The line quickly descends, but with a happy looking emoji character sliding down it.](../fig/r_rollercoaster.png)

<a href="https://www.allisonhorst.com/">Artwork by @allison_horst</a>.

### R code is great for reproducibility

Reproducibility is when someone else (including your future self) can obtain the
same results from the same dataset when using the same analysis.

R integrates with other tools to generate manuscripts from your code. If you
collect more data, or fix a mistake in your dataset, the figures and the
statistical tests in your manuscript are updated automatically.

An increasing number of journals and funding agencies expect analyses to be
reproducible, so knowing R will give you an edge with these requirements.

### R is interdisciplinary and extensible

With 10,000+ packages that can be installed to extend its capabilities, R
provides a framework that allows you to combine statistical approaches from many
scientific disciplines to best suit the analytical framework you need to analyze
your data. For instance, R has packages for image analysis, GIS, time series,
population genetics, and a lot more.

### R works on data of all shapes and sizes

The skills you learn with R scale easily with the size of your dataset. Whether
your dataset has hundreds or millions of lines, it won't make much difference to
you.

R is designed for data analysis. It comes with special data structures and data
types that make handling of missing data and statistical factors convenient.

R can connect to spreadsheets, databases, and many other data formats, on your
computer or on the web.

### R produces high-quality graphics

The plotting functionalities in R are endless, and allow you to adjust any
aspect of your graph to convey most effectively the message from your data.

### R has a large and welcoming community

Thousands of people use R daily. Many of them are willing to help you through
mailing lists and websites such as [Stack Overflow](https://stackoverflow.com/),
or on the [RStudio community](https://community.rstudio.com/). Questions which
are backed up with [short, reproducible code
snippets](https://www.tidyverse.org/help/) are more likely to attract

### Not only is R free, but it is also open-source and cross-platform

Anyone can inspect the source code to see how R works. Because of this
transparency, there is less chance for mistakes, and if you (or someone else)
find some, you can report and fix bugs.

Because R is open source and is supported by a large community of developers and
users, there is a very large selection of third-party add-on packages which are
freely available to extend R's native capabilities.

![Cute looking cartoon of a paint-covered artist holding paint brushes, and is wearing a shirt labeled RStudio](../fig/rstudio-artist.jpg)

RStudio extends what R can do, and makes it easier to write R code and interact
with R. <a href="https://www.allisonhorst.com/">Artwork by @allison_horst</a>.

{% include links.md %}
