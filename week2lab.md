# Week 2 lab

## Deadline

Everything is due by **5PM next Tuesday**.

## What you will turn in:

### Part "A"

1. The PR to the class website described below.

### Part "B"

1. Create a new repository for your R markdown work.
2. Take the HTML output from the R markdown "knit" step and put it in that repo.
3. Also put the original "Rmd" file there.
4. The README for your new repo will provide answers to the specific questions asked below.
5. Add a link from your "homework website" to your new repository.
   This link should be in a new subsection (two hashes!) on your website's main page.
6. Notify me via a DM on Discord when this is done.
   Not email.
   Not Canvas.
   Do **not** add me as a collaborator.
   (I get scores of GitHub notifications per day due to the organizations that I'm a part of.
   I'll simply never see the notification adding me to collaborate.)

## Some advice

Do not wait until the last minute!
Many of you are guaranteed to run into technical issues!!

## Goals

* Make sure you are set up to use R studio to generate reports in the R Markdown format.
* Demonstrate the amazing ability to do the following:
    * Execute both R and Python code in a common environment
    * Exchange data between the 2 languages!
    * Generate a plot using Python code from data that originated on the R side.
* You will learn, by osmosis, about several R, R Markdown, and Python features.

## Add a link to your assignment web page to the course web page.

You will submit a pull request to the [class website repository](https://github.com/ThorntonLab/AdvancedInformatics2021).

This PR will:

* Add a file called `name.md` where `name` is your first name.
  For example, mine would be called `kevin.md`.
* The contents of this file will be a hyperlink to the web page that you created last week.
* The link text will be `FirstName LastName`.  
  For example, mine would read `Kevin Thornton`.

## Fun with R Studio and conda!

So, you were asked to have both of these installed by today (Thursday).
If you haven't done this, then do so now.
Remember to get your "channels" set up so that you can use `bioconda`!

Install the following package(s) into R Studio (using the "install packages" bit of the interface):

1. reticulate
2. nycflights13
3. ggplot2

Install the following package(s) via `conda`:

1. pandas
2. seaborn

Now, in a new `R Markdown` document in R Studio, enter the following code:

``````
```{r}
library(nycflights13)
data(flights)
```

```{python}
import pandas
print(type(r.flights))
print(r.flights)
```
``````

### Hints

At the top of a new `R Markdown`, there's a "setup" block that often looks like:

``````
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
``````

In this block, you'll want to state that you are using `reticulate`.
Some of you may need to also tell the system where your Python executable is:

1. What function does that?
2. What shell command do you use to find your Python path?
3. What does `include = FALSE` do?

### Possible road blocks.

#### Windows users.

You'll have to figure out how to point your Rstudio to the Python binary on your Ubuntu system.
Let us know how to do that! ;)

## Moving on

Press the `knit` button.
Fingers crossed, you should see a window pop up with an HTML rendering generated from your markdown.

## Level up!

With that working (famous last words), add the following markdown code to your document:

``````
```{r}
data(mtcars)
```

```{python}
print(r.mtcars.head())
```

```{python}
import seaborn as sns
import matplotlib.pyplot as plt
g = sns.FacetGrid(r.mtcars, col="cyl")
g.map(sns.scatterplot,"wt","mpg");
plt.show()
```
``````

1. What happens if you remove the semicolon (';') from the second to last line of the last code block?
