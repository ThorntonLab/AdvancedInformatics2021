# Work flows

## The problem

Any research project is a work flow or pipeline.
Starting from inputs, a series of intermediate steps result in a final output.

In a lab setting, if an intermediate step, like RNA-seq library preparation, has to be redone, then so does the library QC and sequencing steps that are "downstream" in the pipeline.

The same concepts apply to computational pipelines.
If an input file changes, then the whole pipeline needs to be re-run.
If a script somewhere in the middle changes, then every step depending on the output needs to be re-run.
How do we manage this?

## The world of "make"

This is the same problem faced when building software.
When a source file changes, how do you rebuild the program using the minimal number of steps?

In the software world, various "build systems" work roughly like this:

* They are aware of a final product--the compiled program.
* They know when that product was last built.
* They know what source files are needed for the product.
* They are able to discover which source files depend on which others.
  Like if you change file `A`, the inner workings of `B` change, so you have to recompile both files.
  (This is the clever bit!)
* If the time stamp of a source file is newer than the product, then the build/make command will recompile those files plus any dependencies.

The same logic applies to bioinformatics work.
If your `fastq` or `bam` file is newer than your `VCF` file, then all analyses from the latter are suspect.

The same logic applies to your figures.
If the summary file from the differential gene expression tool is newer than the figure, then we don't trust the figure, do we?

### snakemake

The Python package `snakemake` helps you organize your work flows.
We will be playing with this tool in lab this week.
It works like how I describe above, but it has all sorts of bells and whistles that help computational biology.
Keep in mind that this will be very useful for automating your work in Tony's part of the class!

The docs are [here](https://snakemake.readthedocs.io/en/v3.10.2/index.html).
*Read the tutorial for next time!*

It will also be useful to (try to...) install it for next time.
For Unix-like users who already have `bioconda` set up as a channel from way back in week 1:

```sh
conda install snakemake
```

For Windows users, let's give this a whirl:

```sh
pip install snakemake
```

Again, remember to use the correct version of the "Anaconda power shell", or whatever we've decided to call that thing!

### For next time

* Read the `snakemake` tutorial
* Learn how to execute shell scripts on your system.
  (The previous class had you do this.)
* Learn how to execute R code without having to use R Studio nor the interactive shell.
