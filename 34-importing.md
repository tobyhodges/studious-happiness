---
title: Running Scripts and Importing
teaching: 10
exercises: 10
---

::::::::::::::::::::::::::::::::::::::: objectives

- Learn how to import functions from one script into another.
- Understand the difference between using a file as a module and running it as a script or program.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I import some of my work even if it is part of a program?

::::::::::::::::::::::::::::::::::::::::::::::::::

In our last lesson we learned how refactoring code makes it easier to understand
organize into pices that each have their own purpose. The added modularity also makes
it easier to use in other places.

First, let's start a Jupyter notebook in our current directory
and see what happens if we try to import our file as it is.

```python
import gdp_plots
```

The result is an error related to how Python is attempting to interpret
the file. This is because Python is encountering our call to the function
`main` and isn't sure how to proceed.

:::::::::::::::::::::::::::::::::::::::::  callout

## Running Versus Importing

Running a Python script in bash is very similar to
importing that file in Python.
The biggest difference is that we don't expect anything
to happen when we import a file,
whereas when running a script, we expect to see some
output printed to the console.

In order for a Python script to work as expected
when imported or when run as a script,
we typically put the part of the script
that produces output in the following if statement:

```python
if __name__ == '__main__':
    main()  # Or whatever function produces output
```

When you import a Python file, `__name__` is the name
of that file (e.g., when importing `pandas_plots.py`,
`__name__` is `'pandas_plots'`). However, when running a
script in bash, `__name__` is always set to `'__main__'`
in that script so that you can determine if the file
is being imported or run as a script.


::::::::::::::::::::::::::::::::::::::::::::::::::

Let's add the main function in our script to a section which identifies the program
as being called from the command line.

<pre>
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
        print("Usage: python gdp_plots.py < filenames >")
        print("Options:")
        print("-a : plot all gdp data sets in current directory")

    # check for -a flag in arguments
    if "-a" in argv:
        filenames = glob.glob("data/*gdp*.csv")
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
<b>

if __name__ == "__main__":
    # call main
    main()</b>
</pre>

Now let's go back to the Jupyter notebook and try importing the file again.

```python
import gdp_plots
```

Success! You've just written your first Python module. Any of the functions in that module can now be accessed in our Jupyter notebook session.

```python
gdp_plots.create_plot("data/gapminder_gdp_oceania.csv")
```

### Update the repository

Back in the terminal, let's commit these changes to our repository.

```bash
$ git add gdp_plots.py
$ git commit -m "Moving call to the main function."
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Writing Modular Code

In our previous lesson we refactored our code for clarity and modularity.
As part of that process we created two functions `create_plot` and `create_plots`.
While the `create_plot` function wasn't used in our program, imagine importing our
module and finding that the only way to generate a plot for a single file
is to add that filename to a list before passing that list to `create_plots`.

This might seem very strange or confusing to someone importing our module
for the first time. It take time to develop an intuition for design decisions like
these, but here are a few questions to ask yourself as a guide when organizing code:

- Are my functions able to stand on their own? Do they accomplish simple tasks?
- Is it easy to write a clear function names in my module?
  A function with the name "create\_plots\_and\_export\_data" is likely better
  off being borken up into two functions "create\_plots" and "export\_data"
- Are my function names plural?
  In our example, it may have felt more natural to create only one function,
  "create\_plots". In these cases, it is almost always useful to create a
  function which does our operation once ("create\_plot") and another plural
  form of the function ("create\_plots") which contains a loop over the
  singular function.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- The `__name__` variable allows us to know whether the file is being imported or run as a script.

::::::::::::::::::::::::::::::::::::::::::::::::::


