The glory of dplyr!
===================

The fancy printing of `help` files, etc., in this document is handled by
`printr`:

    library(printr)

    ## Registered S3 method overwritten by 'printr':
    ##   method                from     
    ##   knit_print.data.frame rmarkdown

Today’s exercise requires some imagination on your part. We will not
give you a massive data set. Rather, we provide you the foundation to
efficiently handle some very large computations.

Some of you won’t “get it”. Not now at least. But, [a recent
paper](https://www.genetics.org/content/213/4/1513) of mine literally
would not have been possible without the methods outlined here.

The problem
-----------

1.  You have a very large amount of data. It cannot fit into RAM, so you
    cannot use regular `R/dplyr` or `pandas` methods to process it.
2.  There’s no pre-existing tool for your analysis. For example, this
    isn’t something that `samtools` or `bedtools` or some other
    domain-specific software already do.

Thinking about a solution
-------------------------

Let’s reason through a few things:

1.  The data can’t fit in RAM, so they need to go in a file.
2.  So, we need a file format.
3.  We also need code to process it.

Rectangular data and split/apply/combine
----------------------------------------

Let’s fire up the venerable `mtcars` data set and take a look:

    data(mtcars)
    help(mtcars)

    ## Motor Trend Car Road Tests
    ## 
    ## Description:
    ## 
    ##      The data was extracted from the 1974 _Motor Trend_ US magazine,
    ##      and comprises fuel consumption and 10 aspects of automobile design
    ##      and performance for 32 automobiles (1973-74 models).
    ## 
    ## Usage:
    ## 
    ##      mtcars
    ##      
    ## Format:
    ## 
    ##      A data frame with 32 observations on 11 (numeric) variables.
    ## 
    ##        [, 1]  mpg   Miles/(US) gallon                        
    ##        [, 2]  cyl   Number of cylinders                      
    ##        [, 3]  disp  Displacement (cu.in.)                    
    ##        [, 4]  hp    Gross horsepower                         
    ##        [, 5]  drat  Rear axle ratio                          
    ##        [, 6]  wt    Weight (1000 lbs)                        
    ##        [, 7]  qsec  1/4 mile time                            
    ##        [, 8]  vs    Engine (0 = V-shaped, 1 = straight)      
    ##        [, 9]  am    Transmission (0 = automatic, 1 = manual) 
    ##        [,10]  gear  Number of forward gears                  
    ##        [,11]  carb  Number of carburetors                    
    ##       
    ## Note:
    ## 
    ##      Henderson and Velleman (1981) comment in a footnote to Table 1:
    ##      'Hocking [original transcriber]'s noncrucial coding of the Mazda's
    ##      rotary engine as a straight six-cylinder engine and the Porsche's
    ##      flat engine as a V engine, as well as the inclusion of the diesel
    ##      Mercedes 240D, have been retained to enable direct comparisons to
    ##      be made with previous analyses.'
    ## 
    ## Source:
    ## 
    ##      Henderson and Velleman (1981), Building multiple regression models
    ##      interactively.  _Biometrics_, *37*, 391-411.
    ## 
    ## Examples:
    ## 
    ##      require(graphics)
    ##      pairs(mtcars, main = "mtcars data", gap = 1/4)
    ##      coplot(mpg ~ disp | as.factor(cyl), data = mtcars,
    ##             panel = panel.smooth, rows = 1)
    ##      ## possibly more meaningful, e.g., for summary() or bivariate plots:
    ##      mtcars2 <- within(mtcars, {
    ##         vs <- factor(vs, labels = c("V", "S"))
    ##         am <- factor(am, labels = c("automatic", "manual"))
    ##         cyl  <- ordered(cyl)
    ##         gear <- ordered(gear)
    ##         carb <- ordered(carb)
    ##      })
    ##      summary(mtcars2)

The first several rows are:

    head(mtcars)

<table>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: right;">mpg</th>
<th style="text-align: right;">cyl</th>
<th style="text-align: right;">disp</th>
<th style="text-align: right;">hp</th>
<th style="text-align: right;">drat</th>
<th style="text-align: right;">wt</th>
<th style="text-align: right;">qsec</th>
<th style="text-align: right;">vs</th>
<th style="text-align: right;">am</th>
<th style="text-align: right;">gear</th>
<th style="text-align: right;">carb</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Mazda RX4</td>
<td style="text-align: right;">21.0</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">160</td>
<td style="text-align: right;">110</td>
<td style="text-align: right;">3.90</td>
<td style="text-align: right;">2.620</td>
<td style="text-align: right;">16.46</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="even">
<td style="text-align: left;">Mazda RX4 Wag</td>
<td style="text-align: right;">21.0</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">160</td>
<td style="text-align: right;">110</td>
<td style="text-align: right;">3.90</td>
<td style="text-align: right;">2.875</td>
<td style="text-align: right;">17.02</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">4</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Datsun 710</td>
<td style="text-align: right;">22.8</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">108</td>
<td style="text-align: right;">93</td>
<td style="text-align: right;">3.85</td>
<td style="text-align: right;">2.320</td>
<td style="text-align: right;">18.61</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">1</td>
</tr>
<tr class="even">
<td style="text-align: left;">Hornet 4 Drive</td>
<td style="text-align: right;">21.4</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">258</td>
<td style="text-align: right;">110</td>
<td style="text-align: right;">3.08</td>
<td style="text-align: right;">3.215</td>
<td style="text-align: right;">19.44</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">1</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Hornet Sportabout</td>
<td style="text-align: right;">18.7</td>
<td style="text-align: right;">8</td>
<td style="text-align: right;">360</td>
<td style="text-align: right;">175</td>
<td style="text-align: right;">3.15</td>
<td style="text-align: right;">3.440</td>
<td style="text-align: right;">17.02</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">2</td>
</tr>
<tr class="even">
<td style="text-align: left;">Valiant</td>
<td style="text-align: right;">18.1</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">225</td>
<td style="text-align: right;">105</td>
<td style="text-align: right;">2.76</td>
<td style="text-align: right;">3.460</td>
<td style="text-align: right;">20.22</td>
<td style="text-align: right;">1</td>
<td style="text-align: right;">0</td>
<td style="text-align: right;">3</td>
<td style="text-align: right;">1</td>
</tr>
</tbody>
</table>

The data are “rectangular”, meaning that they are set up like a
spreadsheet.

A typical analysis of such data involves:

1.  **Split** the data up into groups. For example, break the data set
    up according to number of cylinders or numb of gears. One could also
    split by unique `(cylinder, gear)` combos.
2.  **Apply** a function to each group. For example, get the average
    `mpg` for each group.
3.  **Combine** (“aggregate”) the results back into a new data frame.

In `base` `R`, we can do these analyses using `aggregate`:

    aggregate(mtcars, list(cyl=mtcars$cyl), mean)

<table style="width:100%;">
<colgroup>
<col style="width: 3%" />
<col style="width: 8%" />
<col style="width: 3%" />
<col style="width: 8%" />
<col style="width: 9%" />
<col style="width: 8%" />
<col style="width: 8%" />
<col style="width: 8%" />
<col style="width: 9%" />
<col style="width: 9%" />
<col style="width: 8%" />
<col style="width: 8%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: right;">cyl</th>
<th style="text-align: right;">mpg</th>
<th style="text-align: right;">cyl</th>
<th style="text-align: right;">disp</th>
<th style="text-align: right;">hp</th>
<th style="text-align: right;">drat</th>
<th style="text-align: right;">wt</th>
<th style="text-align: right;">qsec</th>
<th style="text-align: right;">vs</th>
<th style="text-align: right;">am</th>
<th style="text-align: right;">gear</th>
<th style="text-align: right;">carb</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: right;">4</td>
<td style="text-align: right;">26.66364</td>
<td style="text-align: right;">4</td>
<td style="text-align: right;">105.1364</td>
<td style="text-align: right;">82.63636</td>
<td style="text-align: right;">4.070909</td>
<td style="text-align: right;">2.285727</td>
<td style="text-align: right;">19.13727</td>
<td style="text-align: right;">0.9090909</td>
<td style="text-align: right;">0.7272727</td>
<td style="text-align: right;">4.090909</td>
<td style="text-align: right;">1.545454</td>
</tr>
<tr class="even">
<td style="text-align: right;">6</td>
<td style="text-align: right;">19.74286</td>
<td style="text-align: right;">6</td>
<td style="text-align: right;">183.3143</td>
<td style="text-align: right;">122.28571</td>
<td style="text-align: right;">3.585714</td>
<td style="text-align: right;">3.117143</td>
<td style="text-align: right;">17.97714</td>
<td style="text-align: right;">0.5714286</td>
<td style="text-align: right;">0.4285714</td>
<td style="text-align: right;">3.857143</td>
<td style="text-align: right;">3.428571</td>
</tr>
<tr class="odd">
<td style="text-align: right;">8</td>
<td style="text-align: right;">15.10000</td>
<td style="text-align: right;">8</td>
<td style="text-align: right;">353.1000</td>
<td style="text-align: right;">209.21429</td>
<td style="text-align: right;">3.229286</td>
<td style="text-align: right;">3.999214</td>
<td style="text-align: right;">16.77214</td>
<td style="text-align: right;">0.0000000</td>
<td style="text-align: right;">0.1428571</td>
<td style="text-align: right;">3.285714</td>
<td style="text-align: right;">3.500000</td>
</tr>
</tbody>
</table>

A solution for very large data sets
-----------------------------------

So, if our data can be arranged as one or more “tidy” spreadsheets and
our analysis will be “split/apply/combine”, then the solution to our
problem is:

1.  Store each data frame/“spreadsheet” as a *table* in a *relational
    database* software.
2.  Use [dplyr](https://dplyr.tidyverse.org/) for our analysis.
