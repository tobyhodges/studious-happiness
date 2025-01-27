---
title: Setup
permalink: /setup/
---

## Installing Python Using Anaconda

[Python][python] is a popular language for scientific computing, and great for
general-purpose programming as well. Installing all of its scientific packages
individually can be a bit difficult, however, so we recommend the all-in-one
installer [Anaconda][anaconda].

Regardless of how you choose to install it, please make sure you install Python
version 3.x (e.g., 3.4 is fine). Also, please set up your python environment at
least a day in advance of the workshop.  If you encounter problems with the
installation procedure, ask your workshop organizers via e-mail for assistance so
you are ready to go as soon as the workshop begins.

### Windows - [Video tutorial][video-windows]

1. Open [http://continuum.io/downloads][continuum-windows]
  with your web browser.

2. Download the Python 3 installer for Windows.

3. Double-click the executable and install Python 3 using *MOST* of the
  default settings. The only exception is to check the
  **Make Anaconda the default Python** option.

### Mac OS X - [Video tutorial][video-mac]

1. Open [http://continuum.io/downloads][continuum-mac]
  with your web browser.

2. Download the Python 3 installer for OS X.

3. Install Python 3 using all of the defaults for installation.

### Linux

Note that the following installation steps require you to work from the shell.
If you run into any difficulties, please request help before the workshop begins.

1. Open [http://continuum.io/downloads][continuum-linux] with your web browser.

2. Download the Python 3 installer for Linux.

3. Install Python 3 using all of the defaults for installation.
  
  a.  Open a terminal window.
  
  b.  Navigate to the folder where you downloaded the installer
  
  c.  Type
  
  ```bash
  $ bash Anaconda3-
  ```
  
  and press tab.  The name of the file you just downloaded should appear.
  
  d.  Press enter.
  
  e.  Follow the text-only prompts.  When the license agreement appears (a colon
  will be present at the bottom of the screen) hold the down arrow until the
  bottom of the text. Type `yes` and press enter to approve the license. Press
  enter again to approve the default location for the files. Type `yes` and
  press enter to prepend Anaconda to your `PATH` (this makes the Anaconda
  distribution the default Python).

## Getting the Data

The data we will be using is taken from the [gapminder] dataset.
To obtain it, download and unzip the file
[python-novice-gapminder-data.zip](files/python-novice-gapminder-data.zip).
In order to follow the presented material, you should launch a Jupyter
notebook in the root directory (see [Starting Python](#Starting-Python)).

## Starting Python

We will teach Python using the [Jupyter lab][jupyter], a
programming environment that runs in a web browser. Jupyter requires a reasonably
up-to-date browser, preferably a current version of Chrome, Safari, or Firefox
(note that Internet Explorer version 9 and below are *not* supported). If you
installed Python using Anaconda, Jupyter should already be on your system. If
you did not use Anaconda, use the Python package manager pip
(see the [Jupyter website][jupyter-install] for details.)

To start the notebook, open a terminal or git bash and type the command:

```bash
$ jupyter lab
```

To start the Python interpreter without the notebook, open a terminal
or Git Bash and type the command:

```bash
$ python
```

[python]: https://python.org
[anaconda]: https://www.continuum.io/anaconda
[video-windows]: https://www.youtube.com/watch?v=xxQ0mzZ8UvA
[continuum-windows]: https://continuum.io/downloads#_windows
[video-mac]: https://www.youtube.com/watch?v=TcSAln46u9U
[continuum-mac]: https://continuum.io/downloads#_macosx
[continuum-linux]: https://continuum.io/downloads#_unix
[gapminder]: https://gapminder.org
[jupyter]: https://jupyter.org/
[jupyter-install]: https://jupyter.readthedocs.io/en/latest/install.html#optional-for-experienced-python-developers-installing-jupyter-with-pip



