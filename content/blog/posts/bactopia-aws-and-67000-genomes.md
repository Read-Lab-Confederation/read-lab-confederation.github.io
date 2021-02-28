---
title: "Using AWS Batch to process 67,000 genomes with Bactopia"
subtitle: "*How we spent $12K of Jeff Bezos' cash*"
description: ""
author: ["Robert Petit"]
date: '2020-12-07'
featured_image: "/images/posts/bactopia-aws-and-67000-genomes/final-nf-tower.png"
draft: false
---



In 5 days, I processed 67,000 *Staphylococcus aureus* genomes using [Bactopia](https://bactopia.github.io/) and Amazon Web Services (AWS). What would have taken 6 months using our local resources, was done in less than a week's time. (*magical*)
 
More importantly, in October 2019 I received an AWS Cloud Credits for Research award that expired on October 31, 2020. I had plenty of time, plenty. Well... yeah... out of nowhere October 2020 shows up and is all like "*OH HIIII!*". So, the genomes had to be processed ASAP, otherwise the credits went *POOF*.

This post outlines my recent experience.

I'll show that [Bactopia](https://doi.org/10.1128/msystems.00190-20) can use AWS to process thousands of genomes reliably and cost effectively. I'll give a run down of how I went from testing a few jobs locally to submitting 67,000 jobs to AWS Batch. This involved getting down into some of the details of how AWS works with Nextflow pipelines and containers along with a lot of optimization. I will also give a breakdown of the costs associated with using AWS.

If you are comfortable processing bacterial genomes and creating your own workflows, but are unsure of how the cloud might work for you, I think this post presents a good *use-case* while also highlighting the costs associated in doing so.

So, let's get started!

## Getting Started

### Acquiring *S. aureus* Genome List
A great thing about Bactopia is you can feed it Sequence Read Archive Experiment (e.g. SRX0000001) as an input. Bactopia will take that accession, download the FASTQs and start processing them. Even better you can use `bactopia search` to [generate the list of accessions](https://bactopia.github.io/usage-basic/#generating-accession-list). So in order to get a list of *S. aureus* publicly available genomes, I used the following command:

```bash
bactopia search "Staphylococcus aureus" --prefix saureus
```

From this command 3 files are created, one of those was `saureus-accessions.txt` which contained the full list of SRA Experiment accessions that were labeled as *S. aureus*. This accessions file could then be used by Bactopia, like this:

```bash
bactopia --accessions saureus-accessions.txt
```

### Getting Setup for AWS Batch
Bactopia is built using Nextflow, which natively supports many different [job executors](https://www.nextflow.io/docs/latest/executor.html). With a simple parameter change (e.g. `-profile slurm`) Nextflow will handle submitting jobs to the specified executor. One of the supported executors is [AWS Batch](https://aws.amazon.com/batch/) which allows you to submit jobs directly to AWS without having to manage a cluster.

In order to get started using AWS Batch with Nextflow, I followed Dr. Anthony Underwood's *[Running Nextflow on AWS Batch](https://antunderwood.gitlab.io/bioinformant-blog/posts/running_nextflow_on_aws_batch/)* and Tobias Neumann's *[Pipelines on AWS](https://t-neumann.github.io/pipelines/AWS-pipeline/)*. After going through these posts, there really isn't much I can add to the setup process that they don't already thoroughly cover.

## The Testing Phase

### Phase 1: Does Bactopia work on AWS Batch out of the box?
Short answer: *nope!* 

While Nextflow works with AWS Batch, Bactopia needed some adjustments to work with Batch.

My testing of Bactopia had mostly been done on a Linux machine using Conda or a SLURM cluster with Singularity images. I had never actually tested Bactopia on AWS Batch but I already had all the Docker containers available. So, I gave it a go and quickly learned a few things. 

1. AWS Batch did not like the extensive use of symbolic links for output files
2. Sometimes a process would start before all inputs were staged
3. Pulling in Docker containers was a time sink

#1 was easy enough to solve, in cases where I was passing a symbolic link I added a [check to choose to copy or create a link](https://github.com/bactopia/bactopia/blob/master/templates/estimate_genome_size.sh#L87-L105). 

#2 was a [known issue](https://github.com/nextflow-io/nextflow/issues/1107) that would occur when an EC2 instance made too many S3 requests. This required me to write a [script](https://github.com/bactopia/bactopia/blob/master/bin/check_staging.py) that would verify all input files were available and if not, exit, then retry the process.

#3 was an issue because Bactopia makes use of 12 different Docker containers. For each container it was taking 1-4 minutes to pull the container from DockerHub to AWS. Bactopia has 29 different processes, so that is 29 container pulls or 29-116 minutes per genome of just pulling containers. *Not cool!*

I solved the first two issues, and set the Docker issue to the side. I was able to process genomes (yay!), but only slowly. For a random set of 100 genomes, it was taking 3-4 hours to complete and failed jobs still occurred because eventually the staged file check would reach maximum retries.

This led me back to the drawing board.

### Phase 2: Could I use SLURM on AWS?
AWS Batch was nice, but the speed was not the best and the file staging was causing too many failures. Given I knew Bactopia worked on SLURM, a quick Google search for *["SLURM on AWS"](https://www.google.com/search?q=SLURM+on+AWS&oq=SLURM+on+AWS)* brought me to [AWS Parallel Cluster](https://aws.amazon.com/hpc/parallelcluster/), a tool that builds a SLURM cluster on AWS for you. It also automated Network File Shares (NFS) across nodes using AWS Elastic Block Store (EBS) volumes and allowed me to bypass the whole S3 unstaged file situation (a big win!).

After getting Parallel Cluster going, I set up all the datasets, imported Singularity images, and was off testing again. I ran a single genome through and it worked great! It was quick and smooth. I then launched another job, this time with 100 genomes. It failed due to:

*no more spot-instances available in your availability zone* 

Ok? That's weird? My AWS accounts limits are pretty high, so it that shouldn't be an issue. My first thought was to change availability zones. Maybe some one else is trying to spend all their credits before Halloween?

In order to change availability zones, this required shutting down the cluster and creating a new one. Even better, Parallel Cluster does not allow you to choose a zone it just *"randomly"* picks one. So guess what? 

You know it! I got the same availability zone on the next cluster! Haha I played the *random* game a few times, finally got a different zone, and restarted the setup process again.

All set up, new zone, let's go! ... 

*no more spot-instances available* 

Well, that's annoying, let's try a different instance type. 

*no more spot-instances available* 

Hmmm, well I guess another zone? 

I eventually edited the Parallel Cluster launch code to force an availability zone, and tried all of the us-east-1 zones. In every zone, no matter the instance type I tried, I eventually got the *no more spot-instances available* error. Again, and unfortunately, this error was not due to limits on my AWS account. Otherwise it would have been an easy fix (*"Hey AWS, please, can I have some more?"*).

I was really rooting for Parallel Cluster, but at the end of the day it just didn't work out. So it was back to AWS Batch.

### Phase 3 (Back to AWS Batch): How can I pull Docker containers faster?
Now that I was back on AWS Batch, I decided it was time to solve the whole *"pulling Docker containers taking forever"* issue. Because waiting 30-120 minutes for containers, per-genome, is an obscene amount of wasted time (and money!). 

I had a few options in mind, put the containers on the AWS Elastic Container Registry (ECR) or create an Amazon Machine Image (AMI) to *"pre-pull"* them. I thought about using ECR, but it required authentication to pull images (unless I missed something) so I went the custom AMI route.

This process was a lot of trial and error. To make matters a little more tedious, AMIs cannot be changed on existing AWS Batch compute environments. For every AMI I created, I had create a new compute environment for AWS Batch. I'm sure there's some automated method, but not for this amateur!

Well, I tried pre-building Conda environments:
```bash
# Problematic frame:
# C  [libc.so.6+0x14b35f][thread 3959 also had an error]
  __memmove_avx_unaligned_erms+0x4f
```

Never mind, I then tried Singularity in Docker:
```bash
env: 'singularity': No such file or directory (Fixed: added to container)
ERROR  : Failed to unshare root file system: Operation not permitted (tried --priveledged)
```

I tried Docker in Docker:
```bash
.command.run: line 259: docker: command not found (Fixed: installed docker on container)
docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. \
        Is the docker daemon running?. (Fixed: Mounted /var/run/docker)
/bin/bash: .command.run: No such file or directory
```

Don't judge! It was late and I was trying to make it work! There were other attempts, but those were lost to the 

Eventually, I circled back to the Conda error. It should have worked, it was the simplest and most straight-forward method. None of that containers within containers bit. After some digging... 

*sigh* 

It was a simple fix, the issue was related to building the Conda environments on an Intel-based instance and then trying to run them on an AMD-based instance. Some of the tools took advantage of instructions available to the Intel instances and not the AMD ones.

My solution? Don't mix Intel and AMD, and since there were more Intel instance options I only used Intel-based instances. 

After [creating the custom AMI](https://gist.github.com/rpetit3/01f27f52273104bbf609ed5acc9b63a9) and an [AWS Batch Docker container](https://github.com/bactopia/bactopia/blob/master/.awsbatch.Dockerfile), it took less than 30 seconds to pull in the Docker container. Problem #3 solved! Back to testing.

Things were much faster! But soon enough, the unstaged file issue would eventually pop up. It really became an issue when I was trying to run tests with 1,000 genomes. I tried reducing the maximum number of simultaneous transfers, and increasing the maximum number of retries per transfer. But eventually, the issue would pop up.

Back to problem #2.

### Phase 4: How can I reduce S3 transfers and save time (*and money*)?
Bactopia has lots of files being moved between processes, so for AWS Batch there are always numerous files being uploaded and downloaded. At this point, I fell back on my experience processing [40k genomes for Staphopia](https://dx.doi.org/10.7717%2Fpeerj.5261). 

For Staphopia, I used a Python wrapper around the Nextflow pipeline to run everything on a single instance and create a final tarball of the results. 

Except this time for Bactopia, I created a [Nextflow workflow around Bactopia](https://gist.github.com/rpetit3/fe1f5428be135852ec90bfb63aa32c93), a *Nexflowception* if you will! 

This was the solution! So, much smoother than that whole container within container debacle.

With this wrapper, a genome was processed from start to finish on a single instance and only the final tarball of the results was uploaded to S3. This meant all the intermediate files were never uploaded to S3 significantly reducing the chance of the *unstaged files* error.

A consequence of this approach was I couldn't take advantage of Nextflow's queue manager. I had to limit each genome to 4 cores and 16GB of memory. Otherwise, AWS Batch would end up setting aside a lot of resources that weren't being used efficiently.

During all this testing, I probably tested ~5,000 genomes (only ~2,000 unique) and got a really good idea of the cost per genome and expected runtime/compute requirements. It was time to start processing all the genomes. 

Every one of them.

## The Launch Phase
Honestly there isn't much to say here, a lot happened but at the same time not much happened. I had planned to submit genomes in batches of 5,000 or so, but decided:

![Final Tower Stats](/images/posts/bactopia-aws-and-67000-genomes/submit-all.png)

I went for it, and submitted all of the remaining 65,000 genomes at once. I also used [Nextflow Tower](https://tower.nf/) to track the progress, which made it more fun and easy to track.

For the next 5 days, genomes were processed and it all went as smooth as I could hope for.

*And, then it was done.*

Since I used Nextflow Tower I was able to get some pretty nice stats associated with jobs. 

Basically, I used a crap ton of resources over those 5 days!

![Final Tower Stats](/images/posts/bactopia-aws-and-67000-genomes/final-nf-tower.png)

Nearly all jobs took around 55 minutes to complete using only 4-cores and 16GB of memory. This is actually (at least I think so!) pretty cool, because it shows that you don't necessarily need a beefy server to run Bactopia. In most cases, a basic desktop is enough, although it might take longer. 

![Job Duration](/images/posts/bactopia-aws-and-67000-genomes/job-duration.png)


## How much did it cost?

Total: *$12,408 (USD)*

This cost included testing and processing all 67,000 genomes. When I add up the testing and final processing I probably processed close to 70,000 genomes, which comes out to ~$0.18 (USD) to process a *S. aureus* genome using Bactopia and AWS Batch. Here is the breakdown of costs.


| AWS Charge                  | Usage Estimate    | Cost USD (%) | Cost Per Genome |
|-----------------------------|-------------------|--------------|:---------------:|
| Elastic Compute Cloud (EC2) | 258,670 CPU hours | $7,552 (61%) | $0.11           |
| Data Transfer               | 42 TB             | $3,566 (29%) | $0.05           |
| Simple Storage Service (S3) | 84 TB             | $1,286 (10%) | $0.02           |
| Cloud Watch                 | 9 GB              | $4 (0%)      | $0.00           |

The bulk of the cost was compute costs, which was about $0.11 per genome. The remaining costs, $0.07, were associated with the storage and transfer of data. I do think there is room to reduce these costs per genome. 

In 2017, I was able to process *S. aureus* genomes for $0.05 per genome using Staphopia on the [Cancer Genomics Cloud (CGC)](https://www.cancergenomicscloud.org/). Staphopia and Bactopia have very similar runtimes and compute requirements (~50 minutes, 4-cores, 16GB memory). I think optimizing EC2 spot-instance types, such as using only smaller ones, could potentially cut the compute costs in half. 

You might have noticed, my S3 storage (84TB) is double my data transfer (42 TB). This is because I told Nextflow to output the results. If I had told Nextflow to link the result instead of copying, I could have easily cut the S3 storage in half, saving ~$640. 

Unfortunately, there is very little room to reduce data transfer costs. In my case I was already only downloading a gzipped (`--best`) tarball of all the results per sample. I could have reduced the tarball size by removing some of the result files (FASTQs or BAMs). But, to regenerate these files again would have taken a considerable amount of time. So, for me at least, it made more sense to not delete any of the results and keep them all in the final tarball. 

In the end, with a few tweaks, I could have potentially reduced the costs from $0.18/genome down to $0.11/genome, with roughly half of the $0.11 being just data transfer costs. 

Although, the cost savings would have come with a hit on total processing time. It's likely by using only small instance types, the total processing time would have taken multiple weeks. Given I was on a deadline to spend those credits, I think completing everything in less than a week was a fair trade off.

Fortunately, all the costs, except $60, were covered by an [AWS Cloud Credits for Research](https://aws.amazon.com/research-credits/) award.

## So, what's next?
The plan for these data is to become the basis for Staphopia V2. At the time of writing this, I am still in the midst of transferring data between servers (gotta have those backups!), so we haven't actually gotten the chance to start digging into the data. So, stand by! It's coming soon!

## Conclusions (TL;DR)

- Nextflow works with AWS Batch, but Bactopia needed adjustments
- [AWS Parallel Cluster](https://aws.amazon.com/hpc/parallelcluster/) did not work for this case
- Trial and error: Conda, Docker, Singularity, and S3 file staging issues
- *Nextflow-ception* (Bactopia in a Nextflow wrapper) worked best
- Bactopia can be processed small machines (4-core, 16GB memory)
- Bactopia cost ~$0.18 per genome using AWS 
