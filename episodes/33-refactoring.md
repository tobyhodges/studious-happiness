---
title: Refactoring
teaching: 10
exercises: 10
---

::::::::::::::::::::::::::::::::::::::: objectives

- Understand the value of refactoring code and use of functions.
- Practice determining where code can be divided into smaller functions.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- When should I reorganize my code so it is more clear and readable for others?
- How can I organize my code so that it is useable in other places?
- Why do I almost always want to write my code as though it will be used somewhere else?

::::::::::::::::::::::::::::::::::::::::::::::::::

This code works nicely for generating plots of multiple data sets, but there is
now a lot of code to digest in our script. Picture yourself looking at this code for
the first time. Would it be immediately clear to you where are arguments are handled
and where plots are generated?

It would be nice to break this work into clear chunks of code. This can be
accomplished by making the argument checking section and the body of the for
loop their own functions. This requires surprisingly few changes to the code,
but makes it much more clear. This process is called **refactoring**.

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise: make a refactoring plan

Given the guidance above, talk with your neighbors about which parts of the script should be
moved into functions. Try to think of ways to make the functions the most reusable on their own.

:::::::::::::::  solution

## Solution

A possible solution:

1. A function that parses the arguments
2. A function that makes the plots
3. A function that calls the other functions

This isn't the only "right" solution, but a reasonable way to split things up

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Let's refactor our script

### Create a Branch

Because we're making a major change, let's make a new branch to work in.

```bash
$ git checkout -b refactor
```

Let's break the code into 4 functions:

- `parse_arguments()` - gets the input from argv[], returns a list of file names
- `create_plot()` - takes one file name as input, creates one plot and writes it to the fig folder
- `create_plots()` - takes a list of files as input, calls `create_plot()` for each element in the list
- `main()` - calls `parse_arguments()` and `create_plots()`

Below is a template that will help you write these functions. The `"""` syntax indicates a multi-line comment.
If these comments are the first thing in a function, they are known as a `Docstring`.

```python
import sys
import glob
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt


def parse_arguments(argv):
    """
    Parse the argument list passed from the command line
    (after the program filename is removed) and return a list
    of filenames.

    Input:
    ------
        argument list (normally sys.argv[1:])

    Returns:
    --------
        filenames: list of strings, list of files to plot
    """


def create_plot(filename):
    """
    Creates a plot for the specified
    data file.

    Input:
    ------
        filename: string, path to file to plot

    Returns:
    --------
        none
    """


def create_plots(filenames):
    """
    Takes in a list of filenames to plot
    and creates a plot for each file.

    Input:
    ------
        filenames: list of strings, list of files to plot

    Returns:
    --------
        none
    """


def main():
    """
    main function - does all the work
    """



# call main
main()
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Adding Docstrings

In an effort to create human-readable code. It is common to include a
"docstring" or "Python Documentation String", at the top of each function that clearly lay
out three things: the purpose or objective of the function, a description
of the inputs and their datatypes, and a description of what is returned
and their data types. By including these things, debugging later on is
much more efficient because now the developer knows the starting point
(inputs), endpoints (returns), and what the intended change is (purpose).


::::::::::::::::::::::::::::::::::::::::::::::::::

Let's move the code into the functions now:

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise: refactor the code

Now that we have a plan for refactoring and a template to work from, create a new script called
`refactored_gdp_plot.py`. Paste the template from above into the new script. Then copy and paste
the code from `gdp_plot.py` script into the corresponding functions.

:::::::::::::::  solution

## Solution

```python
import sys
import glob
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt


def parse_arguments(argv):
    """
    Parse the argument list passed from the command line
    (after the program filename is removed) and return a list
    of filenames.

    Input:
    ------
        argument list (normally sys.argv[1:])

    Returns:
    --------
        filenames: list of strings, list of files to plot
    """
# make sure additional arguments or flags have
    # been provided by the user
    if argv == []:
        # why the program will not continue
        print("Not enough arguments have been provided")
        # how this can be corrected
        print("Usage: python gdp_plots.py <filenames>")
        print("Options:")
        print("-a : plot all gdp data sets in current directory")

    # check for -a flag in arguments
    if "-a" in argv:
        filenames = glob.glob("*gdp*.csv")
    else:
        filenames = argv

    return filenames

def create_plot(filename):
    """
    Creates a plot for the specified
    data file.

    Input:
    ------
        filename: string, path to file to plot

    Returns:
    --------
        none
    """

    # load data and transpose so that country names are
    # the columns and their gdp data becomes the rows
    data = pandas.read_csv(filename, index_col = 'country').T

    # create a plot of the transposed data
    ax = data.plot(title = filename)

    # set some plot attributes
    ax.set_xlabel("Year")
    ax.set_ylabel("GDP Per Capita")
    # set the x locations and labels
    ax.set_xticks(range(len(data.index)))
    ax.set_xticklabels(data.index, rotation = 45)

    # save the plot with a unique file name
    split_name1 = filename.split('.')[0] #data/gapminder_gdp_XXX
    split_name2 = filename.split('/')[1]
    save_name = 'figs/'+split_name2 + '.png'
    plt.savefig(save_name)

def create_plots(filenames):
    """
    Takes in a list of filenames to plot
    and creates a plot for each file.

    Input:
    ------
       filenames: list of strings, list of files to plot

   Returns:
   --------
       none
   """

    for filename in filenames:
        create_plot(filename)


def main():
    """
    main function - does all the work
    """

    # parse arguments
    files_to_plot = parse_arguments(sys.argv[1:])

    #generate plots
    create_plots(files_to_plot)

# call main
main()
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

The behavior of the program hasn't changed, but it has been made more modular by
separating the into different functions with their own purpose.

Why the extra function? Our function main has two primary components to it

1. parsing arguments handed to the program
2. generating the desired plots

But we've defined three functions above the `main` function: `parse_arguments`, `create_plot`, and `create_plots`.

If the functions in our file directly reflected the components in `main` we would only have the `parse_arguments` and `create_plots`
functions. If we think about using these functions independently, however, a function which always takes in a list of
filenames isn't very convenient to use on its own. By defining the `create_plot` function, we have placed most of the
plot generation work there, while allowing for a very simple definition of the `create_plots` function.

The importance of this design decision will be made clear in the next lesson.

Before we commit we need to change our `refactored_gdp_plot.py` script to `gdp_plot.py`
since we don't want to keep two copies of this script around in our repo.
We only made this as a separate script to make it easier to copy-paste.
Once you've tested it, you can either rename `refactored_gdp_plot.py` script to `gdp_plot.py`
or copy the contents of `refactored_gdp_plot.py` script to `gdp_plot.py` and delete `refactored_gdp_plot.py`.

#### Update the Repository

We haven't changed the behavior of our program, but our *code* has changed, so
let's update the repository.

```bash
$ git add gdp_plots.py
$ git commit -m "Refactoring code."
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Branching and Refactoring

To demonstrate that the behavior of our program hasn't changed,
try running it a few different ways using both the `master` and
`refactor` branches. Remember that the command for checking out
a branch is `git checkout <branch_name>`.

::::::::::::::::::::::::::::::::::::::::::::::::::

Now that we're satisfied with our refactor. We can merge this branch into our master branch.

```bash
$ git checkout master
$ git merge refactor
```

:::::::::::::::::::::::::::::::::::::::: keypoints

- Refactoring makes code more modular, easier to read, and easier to understand.
- Refactoring requires one to consdier future implications and generally enables others to use your code more easily.

::::::::::::::::::::::::::::::::::::::::::::::::::


