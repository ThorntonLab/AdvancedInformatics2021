# snakemake in practice

## The goal

Recreate the graphic from the RStudio exercise from the week 2 lab.
Save the output in a file called `mtcars.png`.

## How to get there

1. Write an R script that generates an `sqlite3` database containing the `mtcars` data.
2. Write a Python script that reads in the data using `pandas` and makes the plot using `seaborn`

## The details

1. This "pipeline" will be written and carried out using `snakemake`.
2. The pipeline must be robust to change.
   In other words, if you `touch` any of the inputs, then the work flow should restart from that point and regenerate the necessary outputs.
   `touch` is a Unix command.
   If you are not familiar with it, Google it.

### Some details

1. You'll need to read the `snakemake` docs.
2. You'll have to figure out how to organize the "rules".
3. A correct work flow will generate the final output file starting from a directory containing nothing other than the `Snakefile` and the one `R` and the one `Python` script.

A correct work flow will only execute the necessary steps when a script/input file is "touched".
In other words:

1. If you `touch` your R script, the sqlite3 database and figure will be regenerated.
2. If you `touch` your sqlite3 database or the Python script, then the figure will be regenerated, but not the database.

#### Hints

Most of the steps you need are in the material from previous weeks.
You need to discover how to save a `seaborn` plot to a file, though!

### Question to answer

1. How do you delete all output from a `snakemake` work flow?
2. How do you delete output from a single `snakemake` rule?
3. What is the citation for `snakemake`?

## What to turn in

In a new repository:

1. The `Snakefile`
2. The R and Python script.
3. `mtcars.png`
4. The `README.md` for the repo should display the image.
5. The `README.md` should contain the answers to the questions listed above.
6. The `README.md` should contain evidence that `touch`ing the various files does the right thing.
   You can copy-paste the shell output that happens after you `touch` and rerun the jobs.
   Place the output in a code fence in the README:
   ``````
   ```
   Output
   ```
   ``````
   
   Do **not** use screen shots.
 
The other mechanics are the same.
Link to the new work from your homework web page, etc..
