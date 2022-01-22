---
title: Help, I have too many genome sequences! 
description: ""
author: ["Vishnu Raghuram"]
date: "2022-01-21"
featured_image: "/images/Vishnu_Fig2.png"
draft: false
---
 
#### Said no one, ever. Right? 

###### (tl;dr at bottom)

In [a recent publication](https://journals.asm.org/doi/10.1128/spectrum.01334-21)<sup>1</sup>, we analysed the quorum sensing operon of > 40,000 _Staphylococcus aureus_ genomes to identify recurring patterns of genetic variation. To infer transmission of specific mutations, we were tasked with constructing phylogenies for multiple clonal lineages, each comprising thousands of isolates<sup>1</sup>. 

Whole genome maximum likelihood phylogeny construction is a both time and computation-intensive process, especially for large numbers of tips. Moreover, as we were working with publicly available sequences, there is a lot of redundancy in the uploaded data. Presence of too many near-identical isolates would confound our phylogenetic trees -_ too much data?_

**So, how does one rationally reduce a large dataset while retaining as much of the diversity as possible?** Especially when there’s a sampling bias leading to certain genotypes being sampled (or occurring naturally) more frequently than others? (In our case, for every mutant genotype, there were ~25 isolates with the WT genotype) 

With the rapid growth of genome sequencing data, this is not an uncommon or even a new problem<sup>2,3</sup>. There are several existing strategies for dereplicating genomes to reduce sample size - MASH, ANI, PopPunk. However, what methods are sensitive enough to actually distinguish between “identical” and “near-identical” genomes? 

The “ideal” method would be to perform whole genome alignments and calculate SNP counts. As this would give us a pretty accurate picture of the exact number of nucleotide differences between two genomes. The disadvantage here is that this method does not scale well. Alignments become much slower when more samples are added. Therefore, we need fast methods to estimate the approximate pairwise distances between multiple genomes while also maintaining a reasonable level of accuracy. 

How does a  distance “approximation” method compare against a more robust metric like SNP distance? To answer this question, I used 380 strains from the Staphopia NRD set, a curated dataset of high quality _S. aureus_ whole genomes comprising several Sequence Types and Clonal Complexes<sup>4</sup>. 

I performed core genome alignments with [parsnp ](https://github.com/marbl/parsnp)and calculated pairwise SNP distances using [snp-dists](https://github.com/tseemann/snp-dists). The size of the core genome for 380 diverse _S. aureus_ strains is 1.56Mbp, while the average _S. aureus_ genome size is 2.8Mbp. I also calculated pairwise MASH distances using mash dist. [MASH ](https://github.com/marbl/Mash)is a fast, accurate and relatively computationally inexpensive method to estimate the distance between two genomes. Tools like [PopPunk](https://poppunk.readthedocs.io/en/latest/index.html) and [Assembly Dereplicator](https://github.com/rrwick/Assembly-Dereplicator) use(d) MASH to estimate genomic distances (PopPunk now uses [pp-sketchlib](https://github.com/johnlees/pp-sketchlib)). Then, I plotted the mash distance estimates for each alignment pair against the SNP distance. 
 
![Fig1](/images/Vishnu_Fig1.png "Fig1")

Overall, MASH Distance has great concordance with the SNP distance. But how well does mash perform for low SNP distances? Our goal was to ascertain possible transmission of specific mutant genotypes across generations, therefore we were interested in distinguishing closely related genomes. So, let’s zoom in to look at &lt; 50 SNPs

![Fig2](/images/Vishnu_Fig2.png "Fig2")

The Red vertical line is the 50 SNP mark, the black horizontal line marks a MASH distance of 0.0005. Genomes &lt; 50 SNPs apart could have a MASH distance anywhere between 0 to 0.004 . Moreover, genomes 0 - 500 SNPs apart can all have a MASH dist between 0 - 0.005. 

![Fig3](/images/Vishnu_Fig3.png "Fig3")

The median SNP distances (red vertical line) for a MASH distance threshold &lt; 0.0005 and 0.001 are 47 and 56 respectively. For a MASH distance threshold of &lt; 0.05, the median SNP distance is 208. 

How do you pick a MASH distance-based clustering threshold for comparing such closely related genomes? 

This is subjective, as it depends on the research question. In our case, we were aware that our mutant genotypes would undergo few transmission events, and our main interest was in estimating the long-term transmission capabilities. Therefore, we settled on a threshold of 0.0005 (~47 SNPs). For _S. aureus_, this translates to an approximate of 1 - 2 transmission events<sup>5</sup>. This means, our clustering threshold would collapse genomes that were ~2 transmission events apart. 

No matter what MASH threshold is picked, the clustering is going to be less than ideal. Looking back at Fig 2, even with our strict threshold of 0.0005, the possibility of not collapsing genomes less than 50 SNPs apart still remains, as some pairs can have a mash distance of up to 0.004. This still leaves some redundancy in our final dataset. However, I do believe it is safer to err on the conservative side. Picking a lower threshold would only leave some redundancy whereas picking a higher threshold could collapse more unrelated genomes. In our case, this would lead to missing some intermediate - long-term transmission events. 

### Conclusions

In conclusion - whichever dereplication method and cutoff is used, we need to know what that cutoff means in terms of the number of SNPs and the biological significance of that SNP distance. It is also important to note that not all dereplication tools are the same, even with the same cutoff<sup>6</sup>. 

#### **TL;DR:** **How does one rationally reduce a large dataset while retaining as much of the diversity as possible?**

The following workflow worked well for us:


1. From your initial large dataset, identify a small subset (~ 200 - 300 genomes) that captures the spectrum of genetic diversity. 
    1. In our case, we used Staphopia’s NRD set which comprises many _S. aureus _sequence types.  
2. Using this small but diverse dataset, perform whole genome alignments and calculate pairwise SNP distances. Also perform pairwise distance  estimations using your genomic distance approximation method of choice (eg: MASH, ANI).
    2. We used parsnp and MASH respectively. The SNP distances may also vary based on the genome alignment method used.
3. Based on your biological question, select a distance approximation cutoff based on its concordance with the number of SNPs [Fig 1]
    3. We used a MASH distance cutoff of 0.0005 (~47 SNPs or 1 - 2 transmission events)
4. Repeat your pairwise distance estimation method but now with ALL your data and cluster based on your chosen threshold.

**References**

1. Raghuram V, Alexander AM, Loo HQ, Petit RA 3rd, Goldberg JB, Read TD. Species-Wide Phylogenomics of the Staphylococcus aureus _Agr_ Operon Revealed Convergent Evolution of Frameshift Mutations. Microbiol Spectr. 2022 Jan 19:e0133421. doi: 10.1128/spectrum.01334-21. Epub ahead of print. PMID: 35044202.
2. Genbank and WGS statistics. https://www.ncbi.nlm.nih.gov/genbank/statistics/
3. Aldhous P. Managing the genome data deluge. Science. 1993 Oct 22;262(5133):502-3. doi: 10.1126/science.8211171. PMID: 8211171.
4. Petit RA 3rd, Read TD. _Staphylococcus aureus_ viewed from the perspective of 40,000+ genomes. PeerJ. 2018 Jul 12;6:e5261. doi: 10.7717/peerj.5261. PMID: 30013858; PMCID: PMC6046195.
5. Hall MD, Holden MT, Srisomang P, Mahavanakul W, Wuthiekanun V, Limmathurotsakul D, Fountain K, Parkhill J, Nickerson EK, Peacock SJ, Fraser C. Improved characterisation of MRSA transmission using within-host bacterial sequence diversity. Elife. 2019 Oct 8;8:e46402. doi: 10.7554/eLife.46402. PMID: 31591959; PMCID: PMC6954020.
6. Evans JT, Denef VJ. To Dereplicate or Not To Dereplicate? mSphere. 2020 May 20;5(3):e00971-19. doi: 10.1128/mSphere.00971-19. PMID: 32434845; PMCID: PMC7380574.