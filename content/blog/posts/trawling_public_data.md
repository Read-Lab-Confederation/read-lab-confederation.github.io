---
title: "The joys and perils of trawling through public genome data"
description: ""
author: ["Tim Read"]
date: "2021-12-07”
featured_image: "/images/animation.gif”
draft: false
---

## The joys and perils of trawling through public bacterial genome data

For many bacterial species there are now thousands of genomes in public databases such as the NCBI Short Read Archive (SRA).  These huge datasets offer the possibility of large-scale mining to reveal species-wide patterns of genetic variability in a manner analogous to the way that vast biobanks of human genomic data are powering deep GWAS studies and GISAID has opened the door for SARS-CoV2 genomic analysis.  Our group developed the Staphopia database of 43,000+ public _Staphylococcus aureus_ genomes in 2017 [(Petit and Read 2018)](https://paperpile.com/c/dY3Arn/417n) and the Bactopia pipelines for large scale analysis in 2020 [(Petit and Read 2020)](https://paperpile.com/c/dY3Arn/qwho).  These pipelines pull down FASTQ files deposited in the SRA database and perform quality checks, normalize coverage (usually to 100x) and perform several downstream analyses, including _de novo_ assembly.  Several issues came up when we set about using the processed genomes for large-scale exploratory analysis. The naive approach is to use every genome sequence you can get your hands on (“the Big Trawl”), and that might be OK in some circumstances but there are a number of considerations that might lead you to be more selective in choosing how to interpret results for these types of genomic expeditions.

![The accelerating growth of bacterial projects in SRA.  There were > 1.5 M submitted projects by the end of 2021.](/images/animation.gif)


## Much of the data in the SRA database is not linked to a publication in PubMed

Some funding bodies mandate accession of genome data into public databases before publication. There is a process for manual and automated association of data to subsequently published articles but it is not 100% efficient. We found in surveying the _S. aureus_ data (and manually curating the literature to uncover new linkages) that only 28% of the 43,000 genomes could be linked to a publication [(Petit and Read 2018)](https://paperpile.com/c/dY3Arn/417n).  There are quite complex ethical issues to consider when using unpublished genomes. On one hand, the exact reason that many funders demand pre-publication submission is to increase the value of the data they have paid for by allowing scientists across the globe to access and look at it. On the other hand, publishing the final results of someone else’s study before they have had the chance themselves, takes from their scientific effort and expertise in collecting and accessioning the data and discourages future sharing. An ideal solution would be to contact each data submitter and get their permission to go forward but that can be impractical when you are performing a study using data from hundreds of individual projects. My feeling is that it is **fair use** to publish general statistics that cover genomes in unpublished datasets. For example, the Staphopia paper published the percentage of all genomes that were MRSA versus MSSA and their ST genotypes, including both published and unpublished data. However, focusing on a discovery such as a novel gene using someone else’s unpublished data without their permission is clearly not a fair use of shared data.

There are many blurred lines in the issue of fair use and each project needs to be thoughtfully navigated.  For example, we noticed that there is already unpublished data that is more than 10 years old in SRA.  Often, it is impossible to get any response from the original submitter when contacting through the email addresses on file.  There should be a time limit on the original submitters' claim to priority before another group can fairly use the data but I don’t remember ever seeing one formally proposed anywhere.  


## Many public genomes have inadequate associated metadata 

Basic information about the provenance of a genome sequence is important for reconstructing the history of genetic changes and horizontally transferred genes.  Unfortunately, large numbers of public bacterial genome sequences (probably the majority) have either no attached metadata or metadata that is hard to use.  This situation occurs because it is up to the submitter to deposit metadata and data entry is often skipped through a combination of never collecting the relevant information to begin with, lack of time to complete forms, and the confusing titles of metadata field names.  In a recent preprint [(Raghuram et al. 2021)](https://paperpile.com/c/dY3Arn/lpSO) we tried to streamline the _S. aureus_ metadata by reading papers and supplemental tables to incorporate extra information stored by the SRA.  Focusing on key fields: collection date, geographic location, host species, host body site and “host status” (which we interpret to mean “type of infection”) we aggregated and renamed terms.  For example there are numerous terms for bacteria isolated from blood, e.g “bacteremic”, “blood”, “bloodstream infection” etc scattered over several different metadata fields,  that we aggregate to simply “blood” in the “host body site” field.  

These efforts were extremely time-consuming but necessary to obtain a set of consistently labelled genomes for downstream analysis. In the future, as genomes accumulate, I believe that genomes with missing and inconsistent metadata will increasingly be left out of analyses, making the efforts taken to submit and store them in the public domain a bit pointless.


## Many data sets were generated using deprecated technologies; also, some accessions are mislabelled and misformatted

Over the years, a number of different sequencing technologies have been tried on bacterial genomes, the most important being 454, Ion Torrent, Illumina, Pacific Biosciences and Oxford Nanopore. The data produced by the different machines have unique features and may need specialized software and parameter settings to process separately.  Illumina technology has been the dominant driving force over the past 10 or so years and continues to be the most popular today.  For the Staphopia project, we restricted analysis to just Illumina technology as it was used in > 98% of public accessions. Even limiting analysis to one technology, you still need to confront the changes that have occurred over time.  The first Illumina runs at the end of the 2000’s generated sequences only 36 bp long with lower per base quality than we see today.  We introduced filters to exclude some of the early data as it produced notably poorer quality assemblies than contemporary Illumina experiments.

Although not a major factor in terms of raw numbers, we have seen that a few SRA accessions are misformatted in a way that should lead them to be discarded.  One of these oddities are shotgun assembled genomes converted to FASTQ and submitted to SRA. We have also seen paired end files with different number of reads in the forward and reverse files, and in one case, a “paired end” run with only one FASTQ file. In each case, these data files either cause downstream software to fail or produce weird results.  We have written filters to allow Bactopia to recognize them and exclude them.  Other database problems come from submitters misunderstanding the data entry process and doing things such as giving all genomes from one project the same name (BioSample ID).  Once again, mistakes like this need to carefully screened to recognize them and remove them.  


## Some of the samples are not what you expect them to be: dealing with different types of taxonomic inaccuracy and contamination

After downloading and processing the data, it becomes evident that some of the samples are not what they are claimed to be.  These samples have usually either been misnamed or misidentified when they were originally isolated or they have been contaminated during lab processing. Misidentified/contaminated samples fall under three levels of difficulty to detect - let’s call them “easy”, “quite hard” and “hard”.

The “easy” scenario is usually where a sample is misidentified as a different species.  We have found that this is quite common in NCBI submissions. There are many efficient ways to screen out these samples, such as Mash [(Ondov et al. 2016)](https://paperpile.com/c/dY3Arn/UDj9) and CheckM [(Parks et al. 2015)](https://paperpile.com/c/dY3Arn/FpqW). These samples usually stand apart when any type of downstream analysis (alignment, phylogeny, pan-genome) is run on the processed data and so they are quite easy to filter out.

The “quite hard” scenario is when the strain of interest is contaminated with a strain or strains of another species.  These mixtures can happen through sloppy lab practise.  It can also come about as a consequence of experimental design.  A small proportion of studies are not aimed at sequencing individual colonies from pure culture but are pools made by scraping together multiple colonies isolated directly from a clinical specimen on an agar plate.  The reason for doing this is to capture genetic diversity within a sample.  However, to my knowledge, there is no way to label this experiment as different from others in SRA (technically this a metagenomic experiment rather than true bacterial genome sequencing).  CheckM seems to do a good job of flagging samples that contain a minority of reads from another species.  Other tells are an unusually large number of contigs in the shotgun assembly with a tail of short low-coverage assemblies.  If these samples are annotated then you may see an unusual number of genes with no orthologs in the species.

The “hard” situation is when the sample is an unlabelled pool of two or more strains of the same species.  The tell here is again the presence of an unusual number of low coverage contigs in the shotgun assembly but the closer the strains are genetically the smaller the signal.  Genome based MLST may fail if the strains are from different sequence types.  If the strains are from the same ST (nightmare scenario) then it may be really hard to spot.  As a future project, we are thinking of developing a tool based on an unusual number of minor alleles when the reads are mapped to a reference but haven't implemented this yet.


## Certain types of projects produce many, almost identical, genome sequences

In most cases, the point of comparative genomes is to track genetic variation from strains isolated “in the wild”, outside the laboratory.  However, not all of the genomes in SRA derive directly from clinical or environmental samples.  There are many studies where a common laboratory strain has been evolved in the presence of antibiotics or during animal model infections.  Genomes are also sequenced and deposited as a validation step to confirm their genetic identity.  The upshot of these experiments is that there are many almost exact copies of a small number of strains commonly used in genetic experiments.  Furthermore, the genetic variations found in these samples have been produced in artificial laboratory conditions.  It may be best to exclude all samples from these projects to avoid contamination of evolutionary signals.

Related to this type of oversampling, “within-host” experimental designs seek to sequence multiple clones from the same host over a relatively short time period in order to understand microevolutionary pressures related to competition, immune evasion, antibiotics etc and infer demographics.  In these projects variation is not happening under laboratory selection but there is usually substantial overrepresentation of near-identical genome sequences (although usually not the typical lab strains seen in directed evolution studies).


## There is sometimes extreme sampling bias in the strains sequenced and submitted to public databases

As hinted at by the previous section,  the publicly accessed genome databases are not close to an even representation of species genetic diversity.  Certain heavily researched strains and subspecies have massive numbers of very similar genomes, while strains considered less important have very few accessions. By analogy, consider the currently 1 million plus SARS-CoV2 sequences, differing by at maximum, a few dozen mutations versus the handful of coronavirus sequences from other mammals.  In the Staphopia project, we found that while there were more than 1000 known S. aureus STs, only 10 STs represented >60% of the SRA accessions. 

For this reason, and several others outlined above, it is not wise to present raw statistics of bacterial species as represented by their submitted genomes. For example: the statement _“x% of S. aureus carry the resistance gene y” _needs to be qualified by the method used to select the population of genomes that make up the denominator. 


## Solutions

It seems inevitable that to go forward with whole-species genome analysis we will need to use filtered and curated subsets of the entirety of the data for that organism in SRA.  The need for these curated sets may get more pressing if the capacity for generating and submitting data exceeds reasonable cost and time measures for reprocessing the data (using a pipeline like Bactopia) and storing the results.

In the original Staphopia analysis we created a NRD (non-redundant diversity) set of genomes with high quality genomic data and reasonable metadata, selecting one at random from each ST.  In this way, we reduced 43,000 genomes to a representative 390 for more efficient analysis.  This may be too extreme for some questions. Programs like popPUNK [(Lees et al. 2019)](https://paperpile.com/c/dY3Arn/4un3) have been designed to try and get representative genomic sets with minimal genetic loss.  It may be important to select based on geographic location, time period or other metadata criteria.  In these cases, oversampling within certain genetic groups may even be desirable as it would portray the composition of strains in the population.  We also recently implemented a simple dereplication strategy based on hash distance to reduce 25,511 closely related input _S. aureus_ genomes to 3,312 individual representative genome sequences. The take-home message is that there is no one down-sampling strategy. The important thing is that methodology for creating these sets needs to be rigorously documented using reproducible research protocols.  

Finally, despite all that has been said here, there are arguments for the Big Trawl in certain circumstances (although it may become less and less technically feasible in the future as the database grows). One scenario could be that you are screening for a particular rare mutation or sequence within the species and you don’t want to risk that a strain containing them is placed in the excluded portion.  It may also just be easiest to process and analyze everything rather than setting up time-consuming filters.  We just have to be careful of the traps that having the wonderful resource of abundant free data may pose.

## TL;DR



1. It is tempting to trawl all public genome data when doing cross-species genomic analysis, and in many cases this approach will give an acceptable result (we have done this ourselves several times)
2. There are several types of biases in the public data that can each be addressed using custom-designed filters to produce a carefully cleaned subset of the data
3. There is no one way to subset the genomes, it will depend on the questions being asked and the resources available.


## References
    
[Lees, John A., Simon R. Harris, Gerry Tonkin-Hill, Rebecca A. Gladstone, Stephanie W. Lo, Jeffrey N. Weiser, Jukka Corander, Stephen D. Bentley, and Nicholas J. Croucher. 2019. “Fast and Flexible Bacterial Genomic Epidemiology with PopPUNK.” Genome Research, January, 360917. https://doi.org/10.1101/gr.241455.118.](http://paperpile.com/b/dY3Arn/4un3)


[Ondov, Brian D., Todd J. Treangen, Páll Melsted, Adam B. Mallonee, Nicholas H. Bergman, Sergey Koren, and Adam M. Phillippy. 2016. “Mash: Fast Genome and Metagenome Distance Estimation Using MinHash.” Genome Biology 17 (1): 132. https://doi.org/10.1186/s13059-016-0997-x.](http://paperpile.com/b/dY3Arn/UDj9)


[Parks, Donovan H., Michael Imelfort, Connor T. Skennerton, Philip Hugenholtz, and Gene W. Tyson. 2015. “CheckM: Assessing the Quality of Microbial Genomes Recovered from Isolates, Single Cells, and Metagenomes.” Genome Research, May. https://doi.org/10.1101/gr.186072.114.](http://paperpile.com/b/dY3Arn/FpqW)


[Petit, Robert A., 3rd, and Timothy D. Read. 2018. “Staphylococcus Aureus Viewed from the Perspective of 40,000+ Genomes.” PeerJ 6 (July): e5261. https://doi.org/10.7717/peerj.5261.](http://paperpile.com/b/dY3Arn/417n)


[Petit, Robert A., and Timothy D. Read. 2020. “Bactopia: A Flexible Pipeline for Complete Analysis of Bacterial Genomes.” mSystems 5 (4). https://doi.org/10.1128/mSystems.00190-20.](http://paperpile.com/b/dY3Arn/qwho)


[Raghuram, Vishnu, Ashley M. Alexander, Hui Qi Loo, Robert A. Petit, Joanna B. Goldberg, and Timothy D. Read. 2021. “Species-Wide Phylogenomics of the Staphylococcus Aureus Agr Operon Reveals Convergent Evolution of Frameshift Mutations.” bioRxiv. https://doi.org/10.1101/2021.07.29.454156.](http://paperpile.com/b/dY3Arn/lpSO)
