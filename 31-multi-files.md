---
title: Trying Different Methods
teaching: 5
exercises: 25
---

::::::::::::::::::::::::::::::::::::::: objectives

- Read data from standard input in a program so that it can be used in a pipeline.
- Compare using different methods to accomplish the same task.
- Practice making branches and merging in a Git repository.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How do I plot multiple data sets using different methods?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Handling Multiple Files

Perhaps we would like a script that can operate on multiple data files at
once. This can actually be done in two different ways: (1) writing a
bash script that calls our already-existing Python script in a for-loop, or
(2) modifying our Python script to read multiple files in a for-loop.
We will try both methods and compare the two.

### Create New Branches

First we will create two new branches where we can develop each of these
two different methods. We will call these branches `python-multi-files` and
`bash-multi-files`

```bash
$ git branch python-multi-files
$ git branch bash-multi-files
```

We can check that these two branches were created with `$ git branch -a`.

## Handling Multiple Files with Python

First, we'll try using just Python to loop through mutliple files. Let's
switch to our Python branch.

```python
$ git checkout python-multi-files
```

To process each file separately, we'll need a loop that executes our
plotting statements for each file.

We want our program to process each file separately, and the easiest way to do
this is a loop that executes once for each of the filenames provided in
`sys.argv`.

But we need to be careful: `sys.argv[0]` will always be the name of our program,
rather than the name of a file.  We also need to handle an unknown number of
filenames, since our program could be run for any number of files.

A solution is to loop over the contents of `sys.argv[1:]`. The '1' tells
Python to start the slice at location 1, so the program's name isn't included.
Since we've left off the upper bound, the slice runs to the end of the list, and
includes all the filenames.

Here is our updated program.

<pre>
import sys
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt

<b>for filename in sys.argv[1:]:</b>

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

    # display the plot
    plt.show()
</pre>

Now when the program is given multiple filenames

```bash
$ python gdp_plots.py data/gapminder_gdp_oceania.csv data/gapminder_gdp_africa.csv
```

one plot for each filename is generated.

#### Update the Repository

```python
$ git add gdp_plots.py
$ git commit -m "Allowing plot generation for multiple files at once"
```

### Saving Figures

By using `plt.show()` with multiple files, the program stops each
time a figure is generated and the user must exit it to continue.
To avoid this slow down, we can replace this with `plt.savefig()` and
view all the figures after the script finishes. This function has one
required argument which is the filename to save the figure as. The filename
must have a valid image extension (eg. PNG, JPEG, etc.).

Let's replace our `plt.show()` with `plt.savefig('fig/gdp-plot.png')`. First, create the `fig` directory using `mkdir` Our new script should like like this:

<pre>
import sys
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt

for filename in sys.argv[1:]:

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

    # save the plot
    <b>plt.savefig('fig/gdp-plot.png')</b>
</pre>

If we look at the contents of our folder now, we should have a new
file called `gdp-plot.png`. But why is there only one when we supplied
multiple data files? This is because each time the plot is created,
it is being saved as the same file name and overwriting the previous
plot.

We can fix this by creating a unique file name each time. A simple
unique name can be used based on the original file name. We can use
Python's `split()` function to split `filename`, which is a string,
by any character. This returns a list like so:

```python
name = 'my-data.csv'
split_name = name.split('.')
print(split_name)
print(split_name[0])
```

```python
['my-data', 'csv']
'my-data'
```

We'll split the original file name and use the first part to rename
our plot. And then we will concatenate `.png` to the name to specify
our file type.

<pre>
import sys
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt

for filename in sys.argv[1:]:
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
    split_name2 = split_name1.split('/')[1]
    save_name = 'figs/'+split_name2 + '.png'
    plt.savefig(save_name)
</pre>

### Updating the repository

Yet another successful update to the code. Let's
commit our changes. If we do `git status` we see we also have image
files untracked. Let's ignore those files because they will likely
change as our data changes.

```bash
$ echo "*.png" >> .gitignore
$ git add .gitignore
$ git commit -m "ignoring generated images"
$ git add gdp_plots.py
$ git commit -m "Saves each figure as a separate file."
```

## Handling Multiple Files with Bash

Now that we've created a Python script to save multiple files at once,
let's try to do the same thing in Bash. We'll leave our current branch
alone and switch to our `bash-multi-files` branch.

```bash
$ git checkout bash-multi-files
```

If we look at our `gdp_plots.py` file, it is not in a for-loop format
because it is not up to date with other branch. Our script should look
like this:

```python
import sys
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt

filename = sys.argv[1]

# load data and transpose so that country names are
# the columns and their gdp data becomes the rows
data = pandas.read_csv(filename, index_col = 'country').T

# create a plot the transposed data
ax = data.plot(title = filename)

# set some plot attributes
ax.set_xlabel("Year")
ax.set_ylabel("GDP Per Capita")
# set the x locations and labels
ax.set_xticks(range(len(data.index)))
ax.set_xticklabels(data.index, rotation = 45)

# display the plot
plt.show()
```

Let's use bash to generate multiple plots by calling our Python script
in a bash for-loop. First, let's create a bash file for us to edit.

```bash
$ touch gdp_plots.sh
```

In this script, we'll write a for-loop will call our `gdp_plots.py` script
on multiple files. We can break up our long list of files by using a
backslash `\` and writing the rest on the next line.

```bash
for filename in data/gapminder_gdp_oceania.csv data/gapminder_gdp_africa.csv
do
   python gdp_plots.py $filename
done
```

We can run our script to see if it works:

```python
$ bash gdp_plots.sh
```

When we run this, we see that it stops to show us each plot like before.
Let's update our script to save the figure like before.

<pre>
import sys
import pandas
# we need to import part of matplotlib
# because we are no longer in a notebook
import matplotlib.pyplot as plt

filename = sys.argv[1]

# load data and transpose so that country names are
# the columns and their gdp data becomes the rows
data = pandas.read_csv(filename, index_col = 'country').T

# create a plot the transposed data
ax = data.plot(title = filename)

# set some plot attributes
ax.set_xlabel("Year")
ax.set_ylabel("GDP Per Capita")
# set the x locations and labels
ax.set_xticks(range(len(data.index)))
ax.set_xticklabels(data.index, rotation = 45)

<b># save the plot with a unique file name
split_name1 = filename.split('.')[0] #data/gapminder_gdp_XXX
split_name2 = split_name1.split('/')[1]
save_name = 'figs/'+split_name2 + '.png'
plt.savefig(save_name)</b>
</pre>

When we run the script again, we should have new image files generated.

### Updating the repository

Yet another successful update to the code. Let's
commit our changes. Since our Python and bash scripts had somewhat
unrelated changes, let's make two separate commits. We will also
ignore our images like before.

```bash
$ git add gdp_plots.sh
$ git commit -m "Wrote bash script to call python plotter."
$ git add gdp_plots.py
$ git commit -m "Saves figures with unique name."
$ echo "*.png" >> .gitignore
$ git add .gitignore
$ git commit -m "ignoring generated images"
```

## Comparing Methods

We have successfully developed two different methods for accomplishing
the same task. This is common to do in software development when there is not
a clear path forward. Let's compare our two methods and decide which to
merge into our `master` branch.

One comparison we might be interested in is how fast each is. We can
use bash's `time` function to get the time to run the script. Let's time
each script. To do this, we just add `time` before each command when we run a script
or command and it will give us timing information when it is completed.

While we are on our bash branch, we'll time that script first.

```bash
$ time bash gdp_plots.sh
```

```
real    0m6.031s
user    0m5.535s
sys     0m0.388s
```

We are most interested in the "real" time in the output, which is the
elapsed time we experience.

Let's checkout our python branch and time our script there.

```bash
$ git checkout python-multi-files
$ time python gdp_plots.py data/gapminder_gdp_oceania.csv data/gapminder_gdp_africa.csv
```

```
real    0m3.163s
user    0m3.002s
sys     0m0.132s
```

As we can see, our Python method ran faster than the bash method. For
this reason, we will merge our Python branch into master.

```bash
$ git checkout master
$ git merge python-multi-files
```

Another advantage to the Python method over the bash method is that
we do not need to change a file and commit it each time we want to
create plots from different data.

## More Practice with Multiple Files in Python

:::::::::::::::::::::::::::::::::::::::  challenge

## Finding Particular Files

Using the `glob` module,
write a simple version of `ls` that shows files in the current directory with a particular suffix.
A call to this script should look like this:

```python
$ python my_ls.py py
```

```output
left.py
right.py
zero.py
```

:::::::::::::::  solution

## Solution

```python
import sys
import glob

def main():
    '''prints names of all files with sys.argv as suffix'''
    assert len(sys.argv) >= 2, 'Argument list cannot be empty'
    suffix = sys.argv[1] # NB: behaviour is not as you'd expect if sys.argv[1] is *
    glob_input = '*.' + suffix # construct the input
    glob_output = sorted(glob.glob(glob_input)) # call the glob function
    for item in glob_output: # print the output
        print(item)
    return

main()
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Counting Lines

Write a program called `line_count.py` that works like the Unix `wc` command:

- If no filenames are given, it reports the number of lines in standard input.
- If one or more filenames are given, it reports the number of lines in each, followed by the total number of lines.

:::::::::::::::  solution

## Solution

```python
import sys

def main():
    '''print each input filename and the number of lines in it,
       and print the sum of the number of lines'''
    filenames = sys.argv[1:]
    sum_nlines = 0 #initialize counting variable

    if len(filenames) == 0: # no filenames, just stdin
        sum_nlines = count_file_like(sys.stdin)
        print('stdin: %d' % sum_nlines)
    else:
        for f in filenames:
            n = count_file(f)
            print('%s %d' % (f, n))
            sum_nlines += n
        print('total: %d' % sum_nlines)

def count_file(filename):
    '''count the number of lines in a file'''
    f = open(filename,'r')
    nlines = len(f.readlines())
    f.close()
    return(nlines)

def count_file_like(file_like):
    '''count the number of lines in a file-like object (eg stdin)'''
    n = 0
    for line in file_like:
        n = n+1
    return n

main()

```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Make different branches in a Git repository to try different methods.
- Use bash's `time` command to time scripts.

::::::::::::::::::::::::::::::::::::::::::::::::::


