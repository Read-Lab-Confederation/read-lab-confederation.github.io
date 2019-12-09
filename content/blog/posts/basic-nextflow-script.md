---
title: "Getting to grips with Nextflow"
description: ""
author: ["Tim Read", "Robert Petit"]
date: 2019-12-09
featured_image: "/images/nextflow.png"
draft: false
---

## What is Nextflow and why do I need it?
A recent [blog post](http://www.opiniomics.org/the-three-technologies-bioinformaticians-need-to-be-using-right-now/) identified three components of modern bioinformatics that elevate a user from an early stage code hacker to an efficient power user.  These are containers, cloud computing and workflow software. The other two components will be discussed in other posts but the focus here is on workflows, which allows you to string together programs in reproducible, optimized pipelines that ultimately save time (and money if you want to scale up analysis to the cloud one day).

[Nextflow](https://www.nextflow.io/) is one of the principal free and well-supported workflow software tools written specifically for bioinformatics.  We use it because we like some of the syntax and scalability features but other alternatives, such as snakemake, are also very good.  Nextflow was the basis of the [Staphopia](https://github.com/staphopia/staphopia-ap) and new [Bactopia](https://github.com/bactopia/bactopia) bacterial genome processing pipelines developed by Robert.  We feel that current Nextflow documentation is skewed toward sophisticated programmers wanting to perform large analysis, so here is gentle walkthrough. This post assumes familiarity with the UNIX command line, experience with ```conda``` and next-gen sequence data.

## Installation
*Spoiler alert*: It’s really easy!
```
conda create -n nextflow -c conda-forge -c bioconda nextflow conda
conda activate nextflow

# Add channels to the environment’s conda install
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

Alternatively, you can install without using Conda. You will need a Linux or OS X system with Java 8.  The Nextflow site (https://www.nextflow.io) gives the details on how to install.  You should download Nextflow to a directory customarily used for programs and make sure to add it to your PATH (e.g. .profile, .bash_profile, etc…).

A simple example
There are several tutorials that go over the basics of Nextflow and I don't want to repeat them here.  Instead, I’ll introduce a script I created called *[basic_breseq.nf](https://gist.github.com/rpetit3/15a75bed014cd0be9146fb352166337b)*, partly to teach myself (Tim) and try to give a sense of the overall structure without going into details of all the syntax

The idea is to automate *[breseq](https://barricklab.org/twiki/pub/Lab/ToolsBacterialGenomeResequencing/documentation/index.html)*, a useful program for finding SNPs, indels and other genetic changes between a reference bacterial genome and a mutant that has been sequenced using paired-end Illumina technology.  Normally you run breseq with one reference and FASTQ files from one mutant.  If you have several mutants to run against the same reference you can use the associated `gdtools` program to combine the results into a matrix of variants found at the same position.

## The full script in all its glory
```
#!/usr/bin/env nextflow

// Input parameters
params.genbank = "./NRS384.gbk"
params.fastq = "fastq_files"
params.pattern = "N*P{1,2}_trim.fq.gz"
params.outdir = "breseq_analysis_results"

// Reference and paired read channels
fastq_file_path = "$params.fastq/${params.pattern}"
Channel
    .fromFilePairs(fastq_file_path)
    .ifEmpty { error "Cannot find any reads matching: ${fastq_file_path}" }  
    .set { read_pairs }

// Workflow steps
process run_breseq {
	conda 'breseq'
	
	publishDir "${params.outdir}/runs", mode: 'copy'

	// ‘each’ is used to use the reference genbank file multiple times,
	// otherwise only the first read set would have been processed
	input:
	set pair_id, file(reads) from read_pairs
	each file(genbank_file) from Channel.fromPath(params.genbank)

	output:
	file("${pair_id}/*")
	file("${pair_id}/output/output.gd") into gd_outputs
	
	script:
	"""
	breseq -r $genbank_file -n $pair_id -o $pair_id $reads -j 48
	"""
}

process collect_gd {
	conda 'breseq'
	
	publishDir "${params.outdir}/variantmatrix", mode: 'copy'
	
	// '.collect()' outputs all of outputs in the GD_OUTPUTS channel at once, 
	// instead of one at a time
	// https://www.nextflow.io/docs/latest/operator.html#collect
	input:
	file '*.gd' from gd_outputs.collect()
	file(genbank_file) from Channel.fromPath(params.genbank)

	output:
	file("output.html")
	file("output.tsv")
	
	script:
	"""
	gdtools ANNOTATE -r $genbank_file *.gd
	gdtools ANNOTATE -r $genbank_file -f TSV *.gd
	"""
}
```

### The first part of the script
```
#!/usr/bin/env nextflow

// Input parameters
params.genbank = "./NRS384.gbk"
params.fastq = "fastq_files"
params.pattern = "N*P{1,2}_trim.fq.gz"
params.outdir = "breseq_analysis_results"
```

The first line (shebang) tells your system where to find the `nextflow` program.  

The next lines, `params.*` variables, are input parameters for the script.  The values for these variables can be submitted on the command line (example: `--genbank ref.gb`), through a separate config file, or within the main script. The values in the main script will be used as the default values. 

We have 4 parameters:

    params.genbank (--genbank GENBANK_FILE) the reference file used by breseq
    params.fastq (--fastq FASTQ_DIR) path to FASTQ files to process
    params.pattern (--pattern GLOB_PATTERN) pattern to match read pairs
    params.outdir (--outdir OUTPUT_DIR) directory to output the results to

### Second part
```
// Paired read channels
fastq_file_path = "$params.fastq/${params.pattern}"
Channel
    .fromFilePairs(fastq_file_path)
    .ifEmpty { error "Cannot find any reads matching: ${fastq_file_path}" }  
    .set { read_pairs }
```

This part creates Nextflow channels based on the input file paths.  Channels are sort of like pipes that data flows through that Nextflow uses to queue workflow steps.

A channel named `read_pairs`, is created that will consist of all FASTQ pairs to be processed by breseq. First (`fastq_file_path = …`), the path the glob pattern to match our FASTQ pairs is concatenated to the location of the FASTQ files. Then using Nextflow’s `fromFilePairs()` channel factory, read sets are easily found and grouped into a channel. This is extremely useful when working with Illumina sequences.  

### Third part, where all the action happens
```
// Workflow steps
process run_breseq {
    conda 'breseq'

    publishDir "${params.outdir}/runs", mode: 'copy'

    // ‘each’ is used to use the reference genbank file multiple times,
    // otherwise only the first read set would have been processed
    input:
    set pair_id, file(reads) from read_pairs
    each file(genbank_file) from Channel.fromPath(params.genbank)

    output:
    file("${pair_id}/*")
    file("${pair_id}/output/output.gd") into gd_outputs

    script:
    """
    breseq -r $genbank_file -n $pair_id -o $pair_id $reads
    """
}

process collect_gd {
    conda 'breseq'

    publishDir "${params.outdir}/variantmatrix", mode: 'copy'

    // '.collect()' outputs all of outputs in the GD_OUTPUTS channel at 
    // once, instead of one at a time
    // https://www.nextflow.io/docs/latest/operator.html#collect
    input:
    file '*.gd' from gd_outputs.collect()

    output:
    file("output.html")
    file("output.tsv")

    script:
    """
	gdtools ANNOTATE -r $genbank_file *.gd
	gdtools ANNOTATE -r $genbank_file -f TSV *.gd
    """
}


```
The third part contains two processes, which are the action part of Nextflow.  These are defined as the code between the curly braces ({ }). 

The first process ```run_breseq``` starts with a call to conda to create an environment containing the breseq program.  This means that you don’t have to have breseq installed to run this script! (but you do have to have [conda installed]( https://read-lab-confederation.github.io/blog/posts/conda-intro/)). The first time this script is run, there will be a delay while conda creates the breseq environment but on subsequent runs Nextflow will detect the breseq environment and use the program immediately. Each process sets the input channels and creates channels for output data.  The code between pairs of the three quotes (`”””`) is the script that runs the actual commands based on input and output channels.  

So the `run_breseq` process will run breseq on all the pairs of fastq files (naming the output files by the root part of the filename) using the supplied reference (input genbank file). The results from this process is saved to the “runs” directory. 

The second process, `collect_gd`, then collects all files with the *.gd* extension and processes them using gdtools ANNOTATE. From this a matrix in tabular format is output to the `variantmatrix` directory.

Lines starting with ```//``` are comments and not executed as code.

## Running the script
The script can be run using a command as simple as
```
nextflow basic_breseq.nf
```
Running a Nextflow script (if it doesn’t fail) will produce a directory called “work” as well as the directory with the output files.

One of the most powerful features of Nextflow is the ability to resume without redoing already  executed processes using the ```-resume``` command.  
```
nextflow basic_breseq.nf -resume
```
If you later add or change the input data (e.g new fastq files or additional reference) only new jobs will be run instead of starting from scratch.  Similarly, if processes are added to the script, only the new jobs will be run. This is because -resume uses the data stored in the work directory.  If this directory is deleted, the script must start again from the beginning.

There are a ton of options for running Nextflow - read the online manual for more information. 
The workflow directory can get pretty large with excess directories if the script is run repeated with -resume but can be cleaned up by the ```nextflow clean``` command.

## Writing and debugging Nextflow scripts
I (Tim) have found that writing new scripts is more difficult than R and Python.  This is partly because I am not familiar with the underlying Groovy language that Nextflow is based on.  There are no tools for developing and debugging that I am aware of, so you are forced into writing print statements to understand the values that variables have been assigned (the .println() function is useful).

When developing new scripts I suggest finding a minimal data set consisting of the smallest possible files that produce a known result.  Develop the script one process at a time.

The work directory can be helpful in finding out why function calls are failing.  Each call is assigned its own directory, which contains a list of symbolic links to the data files as well as a shell script containing the function call.  An example of one subdirectory is shown below.

```
ls -lha work/b0/b52f9d163109d54e31d7abd02653b3/
drwxr-xr-x  13 timothyread  admin   442B Aug 23 10:31 .
drwxr-xr-x   3 timothyread  admin   102B Aug 23 10:31 ..
-rw-r--r--   1 timothyread  admin     0B Aug 23 10:31 .command.begin
-rw-r--r--   1 timothyread  admin   1.8K Aug 23 10:31 .command.err
-rw-r--r--   1 timothyread  admin   2.1K Aug 23 10:31 .command.log
-rw-r--r--   1 timothyread  admin   312B Aug 23 10:31 .command.out
-rw-r--r--   1 timothyread  admin   2.5K Aug 23 10:31 .command.run
-rw-r--r--   1 timothyread  admin   133B Aug 23 10:31 .command.sh
-rw-r--r--   1 timothyread  admin     1B Aug 23 10:31 .exitcode
drwxrwxr-x   5 timothyread  admin   170B Aug 23 10:31 N384-1_S1_L001
lrwxr-xr-x   1 timothyread  admin   104B Aug 23 10:31 N384-1_S1_L001_P1_trim.fq.gz -> /test_data/N384-1_S1_L001_P1_trim.fq.gz
lrwxr-xr-x   1 timothyread  admin   104B Aug 23 10:31 N384-1_S1_L001_P2_trim.fq.gz ->/test_data/N384-1_S1_L001_P2_trim.fq.gz
lrwxr-xr-x   1 timothyread  admin    90B Aug 23 10:31 RS384_p2.gbk ->/RS384_p2.gbk
```

You can rerun the particular command to check it is working as advertised by using 

```
cd work/b0/b52f9d163109d54e31d7abd02653b3/
bash .command.sh
```

## Is it worth it?
There are easier alternatives to running Nextflow.  You can run each command one by one manually, or put them in a simple Bash or Python script, or use a graphical workflow manager like [Galaxy](https://galaxyproject.org).  Nextflow has a lot of powerful features (most of which we haven’t touched on in this blog) that really become useful when you have a large number of processes and files. It creates what can sometimes be huge work directories that can be a burden if disk space is limiting (or you are paying for it on the cloud).  Nextflow can sometimes feel like using a Ferrari to pop down the street to pick up a pint of milk.  The arguments for putting in the upfront effort to script Nextflow workflows are:

- Reproducibility - the Nexflow script and work directory provide an explicit record of the way analysis was performed to produce a result.  This is useful for publication and for understanding how results were produced long after the work was performed.

- Extendability - the same script used to process a small number of samples can easily be scaled  because Nextflow is designed to be compatible with AWS, and other multi-core processing environments.  Porting is as easy as changing a configuration file.  You may not feel that you are ever going to need to scale up this way but things happen and if your Nextflow script is designed to process 2 genomes it can process 10,000 as well.

- Modularity - the Nextflow processes are quite isolated and it is fairly easy to cut and paste them into another pipeline and just edit the inputs and outputs. Similarly, if you can easily cut and paste processes from scripts you find on the web, saving you some time and effort.  

