---
layout: post
title:  "Managing Python Versions with Pyenv"
date:   2022-09-26
author: Joseph Cieslewicz
description: A quick and dirty guide to getting Pyenv up and running ASAP.
image: /assets/images/Pyenv_Header.JPG
---

## Introduction: Why should I use Pyenv?

Anyone who has used git understands the power of version control software for collaboration and deployment. Code versions from multiple developers can be easily merged and managed, avoiding collaboration bottlenecks and (worse) deployment disruptions. 

![Git Meme](https://github.com/jcieslewicz/stat386-projects/raw/main/assets/images/Git%20branch%20meme.jpg)

Another related issue that arises for python developers in collaboration and deployment is python version control. Here are some common examples:
* You need to fork a project that someone else on your team originally built several months or years ago. It should be an easy task, but when you try to run their scripts, you realize that the project was built in an old version of python that your local machine won’t support.
* You need to deploy a project to the cloud, but it depends on a multitude of python packages and Pip is re-initialized every time you spin up a new container.
* You have multiple projects going on at once, and you don’t want the overhead of hundreds of python packages floating around in your system all the time.

Pyenv is a python version manager that allows you to solve these and other related problems. It’s a relatively simple tool to become comfortable with (much simpler than Git, for example), but it’s hard to overstate how important version management is when collaborating in Python.

**If you’re already familiar with Pyenv and want to download it or see complete documentation, follow this link:**
[https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv)

## Basic Overview and Setup
It's helpful to have at least a simple understanding of what Pyenv is doing. Anytime you run a command like ‘python3’ or ‘pip’ from your command line, your operating system searches through a set of directories to find an executable with that name. These directories are stored in the PATH environment variable. 

Pyenv sets up a series of “fake” paths called shims, that the operating system searches through before the other paths. These shims will change based on the directory you’re in. Say, for example, you run the ‘python3’ command to execute a script in a directory with Pyenv enabled. Normally, your operating system would search PATH to find the python3.exe executable and run it. However, with Pyenv, the operating system will find the python3 shim before it finds python3.exe. It will pass the python3 command to Pyenv, which will then run the version of python that you’ve set for the current directory.

![Shim Diagram](https://github.com/jcieslewicz/stat386-projects/raw/main/assets/images/Shim_Diagram.JPG)



For a more complete description of how Pyenv works and how to install it, follow this link: [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv).

**In addition to the installation instructions on this page, I also had to add the following lines to the end of my ~/.bashrc file:**

```
# setting for pyenv
export PYENV_ROOT="${HOME}/.pyenv"
if [ -d "${PYENV_ROOT}" ]; then
    export PATH=${PYENV_ROOT}/bin:${PYENV_ROOT}/shims:${PATH}
    eval "$(pyenv init -)"
fi
# setting for pyenv-virtualenv
if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
```
If you run into issues with the installation process, here’s another useful page: [https://realpython.com/intro-to-pyenv/](https://realpython.com/intro-to-pyenv/)

## Using Pyenv: Quick Reference Guide

**If you already have Pyenv installed and want to start using it quickly, skip to here. I’ll include here the commands that I use most commonly, with some basic instructions:**

### Configuration
Keep in mind, Pyenv is separate from the system version of python. You’ll likely need to install python again within Pyenv, even if you’ve already installed python on your machine.

* To see which versions of python you’ve already installed: `pyenv versions`
* To see which versions are available to install: `pyenv install --list`
* To install a version of python: `pyenv install <version>` (For example: `pyenv install 3.9.6`)
* After you install a new version of python, run this line to rehash all shims: `pyenv rehash`

### Using Pyenv in a local directory
* To manage python version and packages in a specific directory, you’ll need to create and set a virtual environment: `pyenv virtualenv <python_version> <environment_name>` (For example, `pyenv virtualenv 3.9.6 my_project`)
* Then, you’ll need to set that virtual environment to apply to the current directory: `pyenv local <version or virtualenv name>` (For example, `pyenv local my_environment`)
* Running this command will create a .python-version file that stores the name of the version or virtual environment. Pyenv will reference that file to decide which version of python to run, anytime python is used in that directory.
* If you ever want to undo this association (and revert to the system version of python), use this command: `pyenv local --unset`

### Managing Packages
* Pip is the package manager for python. With Pyenv, each virtual environment has its own version of pip, which means it will have a unique set of packages installed. The basic command to install a package in pip is: `pip install <package name>`
* To see which packages are currently installed in the current environment, use: `pip list`

Using Pyenv with Pip is enormously convenient for collaboration and deployment. Say I have a project with a long list of dependencies, and I want to make it easy for others on my team to make sure they have those. Or, say I’m deploying to a Docker container, and I need to be able to download all the dependency packages when the container is spun up. First, run this command to create a list of all packages and their current versions: `pip freeze > <filename>`. Then, when your teammates pull this project onto their machines, all they need to do is run `pip install -r <filename>`. Now, the exact version of python and all its packages from your system will be installed on your teammate’s machine, but only within the Pyenv virtual environment that she/he has set up.

## Conclusion
I’ve met many students who know how to write great code, but who have no context for how to collaborate with others. Learning to use Pyenv and other collaborative tools is a low-effort, high-leverage way to make yourself more marketable and impactful as a data scientist. It may take a little time to get used to, but someday your boss will thank you for the investment!

*Image sources: [https://steemit.com/meme/@nerdymemes/git-branch-original-meme](https://steemit.com/meme/@nerdymemes/git-branch-original-meme) [https://digitalvarys.com/how-to-manage-multiple-python-versions-with-pyenv/](https://digitalvarys.com/how-to-manage-multiple-python-versions-with-pyenv/)*
