# Advanced Informatics, Winter 2021

## "Textbook"

* [PDF version](compskills.pdf)
* [Source code](https://github.com/ThorntonLab/ComputerSkills4GradStudents) for the book.

## Course materials

* [Homework assignment turn in pages](hwpages)

### Hints

#### Nuke your conda!

It is useful to know how to entirely get rid of all things `conda`.
On a Unix-like system.

First remove the installation itself and all config/cache files:

```sh
rm -rf ~/miniconda3 ~/.conda*
```

Then, you will want to remove what it added to your login scripts to auto-activate your `base` environment when you log in.
On a Linux machine, that file is `~/.bashrc`.
On macOS, I believe is it `~/.bash_profile`.
In either case, there's a "dotfile" for your shell, the end of which has a chunk of shell code related to `conda`.
On my machine, it looks like this:

```sh
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/condauser/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/condauser/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/home/condauser/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/condauser/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

Use your favorite text editor to remove that. 
Restart your shell/terminal, and you are now free from `conda`!
You can re-install now.

### Week 2

* [Notes](week2notes) on conda and R Studio

## Lab exercises

### Week 1

For this week:

1. Set up a new repository.
   The repo name will be `AdvancedInformaticsExercises`.
2. The `README.md` file will be the landing point for a website.
3. Submit a pull request that adds some random text somewhere in the `README`.

### Week2

[Slides](NotebookSlides) for this week.
See [here](week2lab) for instructions.

### Week 3

* [Useful R/Python libraries for data science](keylibs)
* [lab](week3lab)

### Week 4

* [snakemake intro](week4)
* [lab](week4lab)

### Week 5

* [Plotting](week5)
