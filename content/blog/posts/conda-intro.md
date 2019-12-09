---
title: "Introduction to Bioconda on MacOS / Linux"
description: ""
author: ["Tim Read"]
date: 2019-10-23
featured_image: "/images/bioconda.png"
draft: false
---

# Introduction to Bioconda on MacOS / Linux

By Tim Read, October 2019


## What?
Over the past few years, [conda](https://docs.conda.io/en/latest/), an open-source package manager, has seen rapid growth in the number of available programs it distributes (via [Anaconda Cloud](https://anaconda.org/)). A useful feature of conda is it allows programs to be separated into channels. [Bioconda](https://bioconda.github.io/) is one of these channels specializing in maintaining bioinformatic software.

## Why?

Installing open source software from source (ie the files containing the programs) is hard and time-consuming and often futile.  Bioconda is not perfect but it’s much better than solving the complex riddle of software installation on your own.  It’s a one-stop-shop - with more than 6,000 packages (May 2019) developed and ready to deploy.  It makes your projects more reproducible and easier to update.  Because you don't usually need admin permission to load software onto your own area, it is perfect for use on shared servers.


## Installing Miniconda

Most Bioconda installs use a stripped-down version of conda called Miniconda.  Installation information is [here](https://conda.io/projects/conda/en/latest/user-guide/install/index.html).  I performed the following steps on MacOS 10.15 but they should work for other versions (and Linux will also be pretty similar).  First download the Python 3 version of Miniconda with the bash (.sh) installer using either `wget` or `curl`.

```
wget -O ~/miniconda.sh \
    https://repo.anaconda.com/miniconda/Miniconda4-latest-MacOSX-x86_64.sh 
```

Or..

```
curl -o ~/miniconda.sh \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh
```

Then run the bash installer script.

```
bash miniconda.sh 
```

Accept the licenses and follow the instructions.  I just accepted all the default install options.

I opened a new terminal and confirmed install by checking version.

```
conda --version

conda 4.7.10
```

### For some users: installation in Zshell
On mac OSX 10.15 zsh (Zshell) is the new default shell in the terminal application.  Here are the [gnarly details](https://support.apple.com/en-us/HT208050).  It may also be the default on some Linux systems.  You can check which shell you are in by typing,

```
echo “$SHELL”
```

If you want to make things easy you can change to the bash shell by this command.

```
chsh -s /bin/bash
```

If you use zsh, you will have to set the $PATH environment variable for conda manually. This is done by adding the following line to the end of your zsh profile (typically `~/.zshrc`).

```
export PATH="/Users/YOUR_USER_NAME/miniconda3/bin:$PATH"
```

## Using Bioconda

Bioconda is actually a specific channel within the greater conda project ([https://bioconda.github.io](https://bioconda.github.io)).  To install the Bioconda channel, I followed the [Bioconda's recommended channel ordering](https://bioconda.github.io/user/install.html#set-up-channels) using the commands below in the exact order:

```
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

To explore the tools available on Bioconda and get their recipe names, use the search dialog available at the Bioconda site [https://bioconda.github.io/](https://bioconda.github.io/)

## Environments
Environments are a useful feature of conda.  These are directories with particular subsets of software.  By building environments for a particular purpose you can reduce the confusion created when, for instance, different tools specify different versions of the same software. The default environment is called *base*. For more information about environments an [in-depth discussion is available](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html?highlight=environment).


### Example Environment Creation 
For this demonstration, I want to create an environment called *Blaster* and have contain the programs: [NCBI blast]([http://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs](http://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs)), [muscle aligner]([http://www.drive5.com/muscle/](http://www.drive5.com/muscle/)), and [ncbi-genome-download ]([https://github.com/kblin/ncbi-genome-download/](https://github.com/kblin/ncbi-genome-download/)). This can be done with `conda create`:

```
conda create -n Blaster blast muscle ncbi-genome-download
```

The command above will find the packages and install any dependencies into the newly-created *Blaster* environment.  

### Activating and Deactivating Environments

To check the available environments:

```
conda info --envs
# conda environments:
#
base                  *  /Users/timothyread/miniconda3
Blaster                  /Users/timothyread/miniconda3/envs/Blaster
```

This reports the environments and their paths and notes which environment is active (the asterisk*).  At the moment *base* is the active environment. Because the programs just downloaded are available in the *Blaster* environment, you can’t run them from base.  

To activate *Blaster*, use `conda activate`:

```
conda activate Blaster
```

This switches the conda environment from *base* to *Blaster*. This switch is reflected in `conda info`:
```
conda info --envs
# conda environments:
#
base                     /Users/timothyread/miniconda3
Blaster               *  /Users/timothyread/miniconda3/envs/Blaster
```

This shows *Blaster* is now the active environment.  In this environment, BLAST, Muscle and NCBI Genome Download are now available.  You can confirm this by typing, for example,

```
blastn -help
```

To deactivate the environment.
```
conda deactivate
```

Now the environment is switched back to *base*. BLAST, Muscle and NCBI Genome Download will no longer be available, unless of course they were already in your PATH. 

## Getting more information
To see a list of tools available in your current environment, you can use `conda list`:  

```
conda list
# packages in environment at /Users/timothyread/miniconda3:
#
# Name                    Version                   Build  Channel
asn1crypto                0.24.0                   py37_0  
bzip2                     1.0.8                h1de35cc_0  
ca-certificates           2019.5.15                     0  
certifi                   2019.6.16                py37_0  
cffi                      1.12.3           py37hb5b8e2f_0  
chardet                   3.0.4                    py37_1  
conda                     4.7.10                   py37_0  
conda-package-handling    1.3.11                   py37_0  
… etc , etc
```


## Updating Conda and Tools

To update the version of conda or a specific tool, use `conda update`:

```
conda update conda
# OR 
conda update TOOL_NAME
```


## Strategies for using environments

It’s best to download all packages in the environment at once (ie at creation and update time).  This is a more efficient way to resolve program dependencies than iterative accumulation, which can lead to very long *solving environment* wait times.  

Keep separate environments for different projects/workflows.  Don’t worry about possible redundant installation of the same package in different environments - these days, programs in general take up insignificant hard drive space compared to data.


## Further information

Obviously there is much more to conda and Bioconda than detailed in this brief intro.  To find out more, start with the links in this document or poke around with Google.  Have fun!
