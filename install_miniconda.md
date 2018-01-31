Installing miniconda
====================

In case you are coming across this outside of the CAR conference, you might need a little setup for your computer.

This assumes you are using a Mac.

## Install miniconda

We are using [miniconda](https://conda.io/miniconda.html) because it is faster to install than the full [Anaconda](https://conda.io/docs/user-guide/install/macos.html) package.

- Go to the [miniconda](https://conda.io/miniconda.html) install page.
- Choose the version 3.x version for your computer.

## Create a package

Hopefully that all went well, and you can do the following:

`$ conda create -n nicar csvkit`

When it asks if it ok, type `$ y`.

## Get into the environment

`conda activate nicar`

If that doesn't work, try `source activate nicar`.

For PCs, it is just `activate nicar`, I think.

Now you should be able to continue with [the lesson](README.md)