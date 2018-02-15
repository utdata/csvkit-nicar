Installing miniconda
====================

This document assumes you are using a Mac. (If you are using a PC, see below.)

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

## Create an environment

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

## Installing miniconda on Windows

- I suggest installing [git for Windows](https://gitforwindows.org/) first, so that you can use the "git bash" program, which allows you to use linux like shell commands on Windows.
- Then install [Miniconda](https://conda.io/miniconda.html) for Windows. Use Python 3.6. If you have a newer computer, it's likely the 64-bit version, but use what you think is best.
- During the miniconda install, you'll get a prompt asking if you want to "Add Anaconda to my PATH environment variable". **YES, check this box**.

![Add to path](images/anaconda-prompt.png)

- If you do that, once installed, you should be able to launch a new "git bash" prompt and type `where python` and get a return like this:

```
$ where python
C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3\python.exe
```

### If that doesn't work

You might have to add Python to the path yourself.

- Open the Anaconda Prompt and type in `where python` and the `where conda`.

```
(base) C:\Users\CMcdonald>where python
C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3\python.exe

(base) C:\Users\CMcdonald>where conda
C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3\Library\bin\conda.bat
C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3\Scripts\conda.exe
```

You'll need the path before "python.exe" and the one before "conda.exe" in the next steop.

- Open a regular CMD prompt and enter this:

```
$ SETX PATH "%PATH%;C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3;C:\Users\CMcdonald\AppData\Local\Continuum\miniconda3\Scripts"
```
and hit return and you should get:

```
SUCCESS: Specified value was saved.
```

- Now, open a new Git Bash prompt and type in `where python`, and you should get a good response with the path.

Now you can go back up to the "Create an environment" section and go from there.

---

The above bit on Windows was drawn from [this article](https://medium.com/@GalarnykMichael/install-python-on-windows-anaconda-c63c7c3d1444) and some personal experience.
