---
title: “A step by step guide to Bactopia on AWS”
description: ""
author: ["Tim Read"]
date: 2021-05-15
featured_image: "/images/aws-bactopia-image3.png"
draft: false
---

# Bactopia on AWS: step by step guide 


## Intro to Bactopia

[Bactopia](https://bactopia.github.io/) is a software pipeline for complete analysis of bacterial genomes<sup><a href="https://doi.org/10.1128/mSystems.00190-20">1</a></sup>, developed by [Robert Petit](https://www.robertpetit.com/).  Bactopia is based on the [Nextflow](https://www.nextflow.io/) bioinformatic workflow software. One of the many virtues of Nextflow is its adaptability to different compute environments, so I thought I would play around a bit with [Amazon Web Services](https://aws.amazon.com/) (AWS).


## AWS Strategies 

AWS offers the advantages of paying only for what you ask for/use and having potentially vast scalability.  This means that if you don't want to build and maintain a large-scale bioinformatics server, you still have the ability to run ambitious projects for relatively modest cash outlay (if you do it right). The disadvantages of AWS stem from its complex ecosystem and the fact that there is always a nagging feeling that screwing up will result in a huge bill somewhere down the road.

There are infinite strategies for setting up AWS that differ in parameters such as cost, time, complexity and convenience.  Here, I used two types of AWS storage: [S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) (Simple Storage Service) and [EBS](https://aws.amazon.com/ebs/?ebs-whats-new.sort-by=item.additionalFields.postDateTime&ebs-whats-new.sort-order=desc) (Elastic Block Storage). I think of S3 as sort of a huge hard drive in the cloud, while EBS is like a flash drive attached to an EC2 instance (AWS speak for a rentable machine with processing capability).  S3 is cheap and very well backed-up (your data won't get lost).  It is very adaptable - it can work with many AWS components and can be accessed through URL or even [mounted on your own server.](https://cloud.netapp.com/blog/amazon-s3-as-a-file-system)  The EBS advantages are that it is convenient (attached to your EC2 instance) and can be used to save your configurations in the form of snapshots and images.


## Getting started - creating an S3 bucket for my FASTQ inputs

First thing was to log in AWS and create a new S3 bucket (sort of equivalent to an “uber”-directory name) called “tdr-staph-fastqs”. This bucket was set up as private, meaning only myself and people I authorize can access.  Then I uploaded some FASTQ files using the [AWS CLI](https://aws.amazon.com/cli/) (command line interface), but there are many other ways to interface with S3.  My dataset was 5 randomly chosen paired end Illumina runs of _S. aureus_ from this paper<sup><a href="https://doi.org/10.1186/s12866-018-1336-z">2</a></sup>. The total size was 1.8 Gbytes.  As AWS charges me $0.023 per GByte per month, I could afford to keep it there while I played around with EC2 compute strategies.

![S3 set up](/images/aws-bactopia-image1.png)

## Running all genomes on one machine

The simplest approach to start with is to rent a single instance with a big enough hard drive to stage all my data and run everything there.  It is not necessarily the cheapest or fastest way but, especially, for small projects like this, the cost differential between strategies is a few cents.

I selected a <span style="text-decoration:underline;">m5a.4xlarge</span> instance to launch. This is a fairly powerful machine with 16 CPU and 64 GBytes RAM, which is the ratio we have learned from experience works well with Bactopia jobs on _S. aureus_.  For the machine image I chose the default - ECS-optimized Amazon Linux 2 AMI. I configured the instance with 200 GBytes core EBS storage. This cost $0.69 per hour and at rough estimate, the EBS added another $0.014 per hour.

Following [this protocol](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-access-s3-bucket/), I also created an IAM (Identity and Access Management) policy and profile to allow full S3 access and attached it to the instance.  This means I can use the [AWS CLI](https://aws.amazon.com/cli/) on my EC2 instance.

Once I logged onto the machine I followed a [gist](https://gist.github.com/rpetit3/01f27f52273104bbf609ed5acc9b63a9) made by Robert for setting up Bactopia, with a few modifications.

First I downloaded and set up [Conda](https://docs.conda.io/projects/conda/en/latest/index.html) and some other useful utilities.  I also installed `mamba`, which is a faster version of conda.


```
$ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
$ bash Miniconda3-latest-Linux-x86_64.sh -b -f
$ ~/miniconda3/bin/conda init bash
$ source ~/.bashrc
$ rm Miniconda3-latest-Linux-x86_64.sh 
$ conda install mamba -n base -c conda-forge

$ sudo yum install -y bzip2 wget git emacs
mamba create -n utils -y -c conda-forge -c bioconda procps-ng sed tar wget which coreutils pigz
```


Then created and activated the Bactopia environment using mamba and conda


```
$ mamba create -n bactopia -y -c conda-forge -c bioconda bactopia
$ conda activate bactopia
$ bactopia --version
bactopia 1.7.0
```


Then I took the steps to make the species-specific Bactopia datasets, incorporating the curated _S. aureus _github repo.


```
$ mkdir datasets
$ git clone https://github.com/bactopia-datasets/staphylococcus-aureus.git
$ cp -r staphylococcus-aureus/species-specific/ datasets/
$ rm -rf ../bactopia-datasets/ # clean up
$ bactopia datasets --ariba "card" --species "Staphylococcus aureus" --cpus 4 # takes a few min
```


Next I copied the FASTQ data from S3 and made a file of file names (FOFN), using the _bactopia prepare_ sub-command (as detailed in the [bactopia tutorial)](https://bactopia.github.io/usage-basic/#the-fofn-format).


```
$ aws s3 cp s3://tdr-staph-fastqs fastqs/ --recursive
$ bactopia prepare fastqs/ > fastqs.txt
$ more fastqs.txt
sample	runtype	r1	r2	extra
David-10_AGGCAGAA-CTCTCTAT_L001	paired-end	/home/ec2-user/fastqs/David-10_AGGCAGAA-CTCTCTAT_L001_R1.fastq.gz	/home/ec2-user/fastqs/David-10_A
GGCAGAA-CTCTCTAT_L001_R2.fastq.gz	
David-11_AGGCAGAA-TATCCTCT_L001	paired-end	/home/ec2-user/fastqs/David-11_AGGCAGAA-TATCCTCT_L001_R1.fastq.gz	/home/ec2-user/fastqs/David-11_A
GGCAGAA-TATCCTCT_L001_R2.fastq.gz	
David-12_AGGCAGAA-AGAGTAGA_L001	paired-end	/home/ec2-user/fastqs/David-12_AGGCAGAA-AGAGTAGA_L001_R1.fastq.gz	/home/ec2-user/fastqs/David-12_A
GGCAGAA-AGAGTAGA_L001_R2.fastq.gz	
David-13_AGGCAGAA-GTAAGGAG_L001	paired-end	/home/ec2-user/fastqs/David-13_AGGCAGAA-GTAAGGAG_L001_R1.fastq.gz	/home/ec2-user/fastqs/David-13_A
GGCAGAA-GTAAGGAG_L001_R2.fastq.gz	
David-1_CGTACTAG-GCGTAAGA_L001	paired-end	/home/ec2-user/fastqs/David-1_CGTACTAG-GCGTAAGA_L001_R1.fastq.gz	/home/ec2-user/fastqs/David-1_CG
TACTAG-GCGTAAGA_L001_R2.fastq.gz	
```


Finally, I set up access to [Nextflow Tower](https://tower.nf/) so that I could monitor the workflow in real time.  You need to create an account and [create a secret token](https://www.nextflow.io/blog/2021/nextflow-developer-environment.html).


```
$ export TOWER_ACCESS_TOKEN=<secret_token>
```


All the steps above took less than an hour. Now, to launch the analysis of all 5 genomes I used the command below. 


```
$ bactopia --fastqs fastqs.txt \
         --datasets ./datasets/ \
         --species "Staphylococcus aureus" \
         --coverage 100 \
         --genome_size median \
         --cpus 4 \
         --outdir out_bactopia \
         -with-tower
```


Most of it is fairly obvious and the [Bactopia tutorial ](https://bactopia.github.io/tutorial/)is an outstanding resource to help understand the parameters.  I chose `-cpus 4` to limit the size of one job as a guess that this would increase [the efficiency of the run](https://bactopia.github.io/usage-basic/#-cpus).

The first time bactopia is run on a machine it builds 12 conda environments for the different part so the pipeline, so that takes a few minutes ….

The script generates a URL to Nextflow Tower, so you can watch it all happening in real time.  (_Beware: Nextflow Tower is hypnotizing and wonderful, and a bit of a productivity killer._)  The run was all over in an hour and a half.

![Tower Stats](/images/aws-bactopia-image2.png)

Nextflow produces a lot of nice statistics that the Tower visualizes and it is clear to see that the _blast_proteins_ is a bit of a culprit in slowing down the run. This is because of the large number of proteins in the custom S. aureus database used here. In later versions of the software, we may make some tweaks to this process so that it doesn’t slow down the rest of the pipeline too  much.

![Process Stats](/images/aws-bactopia-image3.png)

So all the data is now in the output directory, which can be moved to S3, or downloaded directly from EBS using scp or rsync.  These directories can be mined for assemblies, blast results etc. I I also used these to run a couple of [bactopia-tools](https://bactopia.github.io/bactopia-tools/) to further summarize and query the data.

The most basic tool is summary.


```
$ bactopia tools summary --bactopia out_bactopia
```


This creates a `bactopia-tools `folder with summaries of data quality, MLST and antimicrobial resistance hits for each strain. For example,


```
$ more bactopia-tools/summary/bactopia/amrfinder/amrfinder-gene-summary.txt
sample_name	AMINOGLYCOSIDE	BETA-LACTAM	FOSFOMYCIN	MACROLIDE	STREPTOTHRICIN	TETRACYCLINE
David-10_AGGCAGAA-CTCTCTAT_L001	True	True	True	True	True	True
David-11_AGGCAGAA-TATCCTCT_L001	True	True	True	True	True	True
David-13_AGGCAGAA-GTAAGGAG_L001	True	True	True	True	True	False
David-1_CGTACTAG-GCGTAAGA_L001	True	True	True	True	True	True
```


Another tool is `staph-typer` a set of (currently) three typing tools specific for S. aureus.  


```
$ bactopia tools staph-typer --bactopia out_bactopia
```


The results from this run are put in the `bactopia-tools` folder under a subfolder for the name of the tool. For example, AgrVATE results show all strains are group 1 _agr_ and probably don’t have frameshifts.


```
$ more bactopia-tools/staph-typer/staph-typer/agrvate-results.txt 
#filename	agr_group	match_score	canonical_agrD	multiple_agr	frameshifts
David-10_AGGCAGAA-CTCTCTAT_L001	gp1	13	1	s	0
David-11_AGGCAGAA-TATCCTCT_L001	gp1	13	1	s	u
David-12_AGGCAGAA-AGAGTAGA_L001	gp1	7	1	s	u
David-13_AGGCAGAA-GTAAGGAG_L001	gp1	8	1	s	u
David-1_CGTACTAG-GCGTAAGA_L001	gp1	13	1	s	0
```


After that is done, the instance can be closed , or if there is no immediate need to run more bactopia analysis, terminated.

(Note - I was charged $1.56 for the 2.5 hour session described here)


## References


1.	[Petit RA, Read TD. Bactopia: a Flexible Pipeline for Complete Analysis of Bacterial Genomes. mSystems 2020;5.: https://doi.org/10.1128/mSystems.00190-20.](https://doi.org/10.1128/mSystems.00190-20)


2.	[Read TD, Petit RA 3rd, Yin Z, Montgomery T, McNulty MC, David MZ. USA300 Staphylococcus aureus persists on multiple body sites following an infection. BMC Microbiol 2018;18:206. https://doi.org/10.1186/s12866-018-1336-z.](https://doi.org/10.1186/s12866-018-1336-z)

