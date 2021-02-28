---
title: "The strain of defining the bacterial strain"
description: ""
author: ["Tim Read"]
date: 2020-11-15
featured_image: ""
draft: false
---

## Introduction: A strain by any other name ...

Large-scale sequencing is giving us a clearer picture of what bacterial species are - clusters of core genomes with more similarities to each other than other species’ clusters.  Horizontal gene transfer can make the boundaries more or less fuzzy but using metrics such as average nucleotide identity <sup>1,2</sup>, in the majority of cases, we can clearly see when two genomes are not in the same species.  But what are the labels to be used within bacterial species to distinguish populations of varying diversity? How can these labels be applied so that they don’t have to change as we acquire more information. The problematic label most associated with subspecies groupings is *"strain"*, and that is my focus here.

Microbiologists have many words for below-species groupings.  Off the top of my head, a few I can think of are: *subtype, clone, clonal complex, genotype, lineage, sequence type, and isolate*.  The meaning of these terms have never been defined globally across all species and I, and others, have been guilty of promiscuous use of the same word in multiple contexts. If you asked 10 microbiologists what they thought the definition of a *"strain"* was, you would probably get 10 different answers.

A whirlwind tour of the literature shows the general lack of comfort in the precision of the meaning of the bacterial strain. The first use of *"strain"* for microbes was probably in the early 1900s. Its use was adapted from animal and plant breeding, where it has meant [*"race, stock, line"* since about 1600](https://www.etymonline.com/word/strain).

According to Wikipedia it is  *"... a low-level taxonomic rank used at the intraspecific level (within a species)"*.  _Bergey’s Manual of Determinative Microbiology_ first edition says a strain *"... is made up of the descendants of a single isolation in pure culture ..."*.  It has also been defined as *"an isolate or group of isolates that can be distinguished from other isolates of the same .. species by phenotypic characteristics of genotypic characteristics"*<sup>3</sup>.  The only literature reference I can find discussing the nomenclature issue is from Dijkshoorn, Ursing and Ursing (2000)<sup>4</sup>.  This thoughtful analysis is hampered though by its publication coming before the age of large-scale comparative genomics, when bacteria were mostly only distinguished by phenotypic and morphological characteristics.   

I am not presumptuous enough to even try at defining strain for bacteriologists in general, but I am going to point out three situations where *"strain"* is used in a way I feel is too generalized, and where it should be split into separate terms.  Yes, and I will make suggestions for what these alternative terms should be, at least for the purposes for setting this lab’s in-house writing style.


## Bacterial cultures versus bacterial genomes

I believe there are practical reasons for using a different vocabulary for bacterial cultures manipulated in the laboratory versus nomenclature for describing how the genome of the bacterium falls in the hierarchical taxonomy of the species. At the present time, it is common to call both *"strains"*.  The photo below (from Wikimedia Commons) is a plate that was obtained by spreading a liquid culture of a bacterial strain *"X"* (will come to what that word might mean later). Each individual colony on this plate could give rise to a pure culture line stored as its own in-format name (e.g A1, A2, A3). Each colony may have been founded by bacteria with identical genome sequences, or maybe one has a single mutation, or maybe one is a contaminant from a different species.  We won’t know until the genomes are sequenced.

![Figure 1](/images/Ecoli_colonies.png)

<sup>**Figure** - Is each one of these colonies a different *"strain"*?. I would call cultures from each single colony *"isolates"*. In terms of genome relationships, each isolate probably represents an individual "clone" of one bacterial strain. (Photo source Wikimedia Commons, User:MadPrime).</sup>



I like the word **"isolate"** for bacterial colonies and cultures.  This would be equivalent to what is commonly called a *"cell line"* in eukaryotic biology. This definition is essentially the same as the Bergey’s strain definition quoted above. Of course, true pure culture isolates can’t always be obtained for every species (e.g _Mycobacterium tuberculosis_, _Chlamydia trachomatis_) but the word symbolizes individual instances of a bacterial culture (such as a colony), with the minimal possible genomic differences between cells in the population.  **Isolate** allows for working with bacteria in the laboratory in the absence of knowledge of the genome sequence. So we could have multiple nearly isogenic isolates of the same strain that we want to keep separate if they have different culture histories.  Isolate is also a convenient term for initial processing of potentially mixed bacterial colonies from clinical samples and environmental sources.


## Genomes within the same cloud of diversity or intra-subject lineage

It could be argued that every bacterium with a non-identical genome sequence should be considered a separate strain.  However, there is no nomenclature that could possibly deal with the population sizes of bacterial species with their billions of individual cells.

There are often situations where we have non-identical **genomes** from multiple closely-related isolates from, say, a single patient (i.e this recent paper on Bacteroides from guts of healthy individuals<sup>5</sup>).  In these cases, the individual isolates derive from a recent expansion from an individual cell and have only a small number of distinct mutations. I like the term **"clone"** to describe these isolates collectively.  The central question here is at what point in time does one clone end and the next begin?  It's impossible to define for all bacterial species, but I think it should be linked to the natural transmission cycle - for instance, in pathogens, between the bottlenecks of infecting a host and then transmitting to a new host.  In a recent paper with Michael David<sup>6</sup>, we tried to coin the phrase *"inter species lineage"* (ISL) for a clone infecting an individual patient.  The term would also apply to bacteria with identical or near-identical genomes but with different complements of mobile elements (plasmids, phage etc).  In experimental evolution experiments, the progeny and parents would also be classed as clones.


## Strains and substrains as stable lineages within species 

From reading hundreds of bacterial comparative genomics papers in many species over the last decade, I frequently see genomes grouped into high level clusters that have been called *"lineages"*, *"clonal complexes"*, *"clades"*, and other terms (indeed, *"clones"*<sup>7</sup>).  Though controversial,  these are what I would reserve the term **"strain"** for because they are going to be stable subspecies features. Tools such as PopPUNK<sup>8</sup> can be used to define strains within a species of a species given comparative genomics data.  

Between *"strains"* and clouds of clonal diversity, there are of course multiple taxonomic levels of where I propose to judiciously use **"substrain"**.  Multi-locus sequence typing (MLST) systems use the unique combination of sequences 7-8 short alleles to form unique sequence types (STs), and these STs make a nice substrain definition.  Each individual ST can be organized as a substrain within a higher level *"clonal complex",* which would be the equivalent of a strain.  Still, there are many commonly used names that are substrains within STs.  For instance, the common MRSA strains USA300 and USA500 are considered substrains of _S. aureus_ ST8.  However, it turns out not all USA300 and USA500 are actually ST8 because of single base mutations within the core genes that define ST<sup>9</sup>.  This is one of the many cases where common usage of names within a scientific community provides challenges to any rational naming scheme you can propose but you have to be hopeful that over time a useful scheme will gain adoption.  

Here is an example of how I propose to use the terms. Suppose you sequence 100 bacterial **isolates**.  Each unique sequence I would term a **"genome"** when describing in a publication.  If some genomes were closely related (maybe setting a threshold of &lt; 40 SNPs) and from the same patient, I would term them **clones**.  Each of the genomes could be members of the *"USA300"* **substrain**, itself in the *"ST8"* substrain, which makes them all *"CC8"* **strains**. Phew.

I’m not going to speculate here about how to discern natural boundaries between these subspecies levels.  SNP or ANI (average nucleotide identity) cutoffs based on eyeballing phylogenies usually work OK.  The PopPUNK software uses networks to define clusters of closely related strains based on core and accessory genome composition<sup>10</sup>. Interestingly, a recent preprint by Sakoparnig et al<sup>11</sup> suggested there may very well be a *"natural"* intermediate definition within species.  Using a pairwise test to quantify recombination between genomes, they showed that there was an inflection in the clonal fraction as genomes diverged. The pattern held up for 6 commonly sampled bacterial species including _Escherichia coli_ and _Staphylococcus aureus_.  The way to interpret this seems to be that when two genomes have diverged recently from a common ancestor they share most of the same clonal history.  The pairwise divergence could be a quantifiable test for membership of the same strain, although thresholds will probably be different across species.  Past this threshold, intra-species differences accumulate to the extent that the clonal relationship between genomes is obliterated.


## Coda - what about *"strain-resolved"* metagenomics?

Traditionally microbiome analysis called for counting bacteria labelled by _species_ or a higher taxonomic level, such as genus, class or phylum.  This is because the short piece of the bacterial genome usually sequenced (a portion of the 16S ribosomal RNA gene) does not contain enough genetic variability to be confident in assignment below species. Metagenomic shotgun sequencing allows us to assemble the genomes of individual bacteria from multiple shotgun metagenomic data.  We can now discriminate genetically different *"strains"* of the same species within microbiome samples.  This may reveal important intra-specific cooperation and competition, functional diversity and niche adaptation<sup>12</sup>.  But what can be done with this data, and what is the relationship between a bacterial strain and a species exactly? There is a lot of interest in *"strain-resolved"* metagenomics, despite the fact that there is no universally accepted strain definition.  There are actually different analyses and different types of questions depending on the scale of the variation you consider. *"Clone-resolved"* metagenomics could be looking for small numbers of differences between bacteria of the same species from different patients or body sites within a patient that would infer transmission paths in an epidemic.  *"Substrain-resolved"* may be about the distribution of substrain (such as USA300) or comparing the numbers of individual strains of a species in different patients.  Ultimately, *"strain-resolved"* questions may be the most popular and useful because strains (as defined above) represent the most stable subspecies taxonomic levels. In any case, greater precision in terminology will reduce the confusion and help us understand the goals of the study.

_Thanks to my lab, especially Ashley Alexander, Michelle Su, Robert Petit and Brooke Talbot, for reading and comments_.

## References

1. [Jain C, Rodriguez-R LM, Phillippy AM, Konstantinidis KT, Aluru S. *High throughput ANI analysis of 90K prokaryotic genomes reveals clear species boundaries.* Nat Commun. 2018 Nov 30;9(1):5114.](https://doi.org/10.1038/s41467-018-07641-9)

2. [Olm MR, Crits-Christoph A, Diamond S, Lavy A, Matheus Carnevali PB, Banfield JF. *Consistent Metagenome-Derived Metrics Verify and Delineate Bacterial Species Boundaries.* mSystems. 2020 Jan 14;5(1).](https://doi.org/10.1128/msystems.00731-19)

3. [Tenover FC, Arbeit RD, Goering RV, Mickelsen PA, Murray BE, Persing DH, Swaminathan B. *Interpreting chromosomal DNA restriction patterns produced by pulsed-field gel electrophoresis: criteria for bacterial strain typing.* J Clin Microbiol. 1995 Sep;33(9):2233–2239.](https://doi.org/10.1128/jcm.33.9.2233-2239.1995)

4. [Dijkshoorn L, Ursing BM, Ursing JB. *Strain, clone and species: comments on three basic concepts of bacteriology.* J Med Microbiol. 2000 May;49(5):397–401.](https://doi.org/10.1099/0022-1317-49-5-397)

5. [Zhao S, Lieberman TD, Poyet M, Kauffman KM, Gibbons SM, Groussin M, Xavier RJ, Alm EJ. *Adaptive Evolution within Gut Microbiomes of Healthy People.* Cell Host Microbe. 2019 Apr 23;](https://doi.org/10.1016/j.chom.2019.03.007)

6. [Read TD, Petit RA, Yin Z, Montgomery T, McNulty MC, David MZ. *USA300 Staphylococcus aureus persists on multiple body sites following an infection.* BMC Microbiol. 2018 Dec 5;18(1):206.](https://doi.org/10.1186/s12866-018-1336-z)

7. [Wyres KL, Wick RR, Judd LM, Froumine R, Tokolyi A, Gorrie CL, Lam MMC, Duchêne S, Jenney A, Holt KE. *Distinct evolutionary dynamics of horizontal gene transfer in drug resistant and virulent clones of Klebsiella pneumoniae.* PLoS Genet. 2019 Apr 15;15(4):e1008114.](https://doi.org/10.1371/journal.pgen.1008114)

8. [Gladstone RA, Lo SW, Lees JA, Croucher NJ, van Tonder AJ, Corander J, Page AJ, Marttinen P, Bentley LJ, Ochoa TJ, Ho PL, du Plessis M, Cornick JE, Kwambana-Adams B, Benisty R, Nzenze SA, Madhi SA, Hawkins PA, Everett DB, Antonio M, Dagan R, Klugman KP, von Gottberg A, McGee L, Breiman RF, Bentley SD, Global Pneumococcal Sequencing Consortium. *International genomic definition of pneumococcal lineages, to contextualise disease, antibiotic resistance and vaccine impact.* EBioMedicine. 2019 May;43:338–346.](https://doi.org/10.1016/j.ebiom.2019.04.021) 

9. [Frisch MB, Castillo-Ramírez S, Petit RA 3rd, Farley MM, Ray SM, Albrecht VS, Limbago BM, Hernandez J, See I, Satola SW, Read TD. *Invasive Methicillin-Resistant Staphylococcus aureus USA500 Strains from the U.S. Emerging Infections Program Constitute Three Geographically Distinct Lineages.* mSphere. 2018 Jun 27;3(3).](https://doi.org/10.1128/msphere.00571-17) 

10. [Lees JA, Harris SR, Tonkin-Hill G, Gladstone RA, Lo SW, Weiser JN, Corander J, Bentley SD, Croucher NJ. *Fast and flexible bacterial genomic epidemiology with PopPUNK.* Genome Res. 2019 Jan 24 [cited 2018 Jul 9];360917.](https://dx.doi.org/10.1101%2Fgr.241455.118) 

11. [Sakoparnig T, Field C, van Nimwegen E. *Whole genome phylogenies reflect long-tailed distributions of recombination rates in many bacterial species.* bioRxiv. 2019 [cited 2019 Apr 9]. p. 601914.](https://doi.org/10.1101/601914) 

12. [Van Rossum T, Ferretti P, Maistrenko OM, Bork P. *Diversity within species: interpreting strains in microbiomes.* Nat Rev Microbiol. 2020 Jun 4;](https://doi.org/10.1038/s41579-020-0368-1) 
