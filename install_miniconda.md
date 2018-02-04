Installing miniconda
====================

This document assumes you are using a Mac. (If you are using a PC, perhaps first install [git for windows](https://git-scm.com/download/win) and then use the program Git Bash in place of Terminal references here.)

In case you are coming across this outside of the CAR conference, you might need a little setup for your computer.

Python programming best practices include building a little sandbox to hold all the Python dependencies you need. These are called virtual environments, and there are several ways to to handle it.

My preferred way is to us an environment manager called [conda](https://conda.io/docs/index.html).

## Install miniconda

We are using [miniconda](https://conda.io/miniconda.html) because it is faster to install than the full [Anaconda](https://conda.io/docs/user-guide/install/macos.html) package.

- Go to the [miniconda](https://conda.io/miniconda.html) install page.
- Choose the version 3.x version for your computer (and I'm assuming Mac here).
- This will download a file that is the script to install minconda
- Launch your Terminal.app application
- Type into the Terminal (but not the $):

`$ bash ~/Downloads/Miniconda3-latest-MacOSX-x86_64.sh`

As you are typing this, you *should* be able to use tab-complete to write out some of the files. so type "bash ~/Down" and then tab, which should complete to "cd ~/Downloads/", then "Mini" and hit tab, which should finish out the file name.

You'll be asked to answer a series of questions by the installer:
- review the License. Hit Return to do so.
- You don't have to page through the whole license. Hit the "q" key to exit.
- When asked to accept the terms of the license, type in "yes" and  hit return
- You'll then be asked to confirm the install. Hit Enter to say yes.
- I *think* there is one more question about path. Answer "yes".

Once you are done, Quit your Terminal app and re-launch it.

You've now installed Conda a command-line package manager for Python.

## Create a package

Hopefully that all went well, and you can do the following:

`$ conda create -n nicar csvkit`

When it asks if it ok, type `$ y`.

You've now created a reusable environment called "nicar" that includes the Python package called "csvkit".

## Get into the environment

`source activate nicar`

And if it works correctly, your terminal prompty line should now start with "(nicar)".

If that doesn't work, try `conda activate nicar`.

For PCs, it is just `activate nicar`, I think.

You've now launched the "nicar" environment, which is a special Python sandbox that won't mess up the rest of your computer.

Now you should be able to continue with [the lesson](README.md)