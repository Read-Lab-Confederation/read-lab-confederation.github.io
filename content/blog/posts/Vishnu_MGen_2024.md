---
title: "Diving into the pool: genome based strategies to effectively sample pathogen diversity" 
description: ""
author: ["Tim Read","Vishnu Raghuram"]
date: "2024-01-17"
featured_image: "/images/2024-Vishnu-Fig2.png"
draft: false
---


# Diving into the pool: genome based strategies to effectively sample pathogen diversity

**What is the optimal to sample genomes of a human-colonizing  pathogen like _Staphylococcus aureus_? In a [recently published collaboration](https://www.microbiologyresearch.org/content/journal/mgen/10.1099/mgen.0.001111) with Dr. Michael David at the University of Pennsylvania, our group compared the traditional method of sequencing individual colony genomes to sequencing pools of colonies.**

Efforts to track the spread of bacterial pathogens typically include collecting samples from potentially infected people, such as skin or nasal swabs. The traditional next step is to selectively enrich the pathogen of interest to isolate it from other microorganisms. Typically, the sample is spread over the surface of a petri dish containing nutrient-rich media that favors the growth of the bacteria of interest (a process called “plating”). This growth can appear as multiple individual clumps of cells called “colonies”, where each colony represents descendants of a single bacterial cell that has divided repeatedly.  Then the next step is to use a “pure culture” - selecting one colony to represent the sample, and growing it further for tests of phenotypes (observable traits) such as antibiotic resistance. In addition, sequencing the genome for bioinformatic screens is becoming increasingly common, where we identify potentially clinically relevant genes from the DNA of the sequenced pathogens.

The issue with using a single colony pure culture is that it might not represent the diversity of the pathogen in the original sample.  Bacteria from the same species can be subdivided into “strains'' with different phenotypes.  If there were multiple strains present in the original sample, then they would be missed by only sampling one colony. Even if only a single strain was present, we are beginning to understand that there is still  “within-strain” diversity - fractions of the bacterial cells may have acquired mutations or genes through horizontal transfer that would be missed through focus on single pure culture.  The alternative approach to overcome this limitation has been to “pool” colonies from the original selection plate and sequence the genomes of a potentially diverse population.

We had an opportunity to test the relative benefits of pure versus pooled cultures through an ongoing study in Dr. David and Dr. Read’s labs called SEMAPHORE - Study of the Evolution of MRSA, Antibiotics, and Persistence Having the Outcome of Recurrence. MRSA, or methicillin resistant *S. aureus*, is an antibiotic resistant version of the common bacteria *S. aureus* that can cause a wide range of diseases, from minor skin infections to serious life-threatening conditions such as pneumonia, bloodstream infections, and toxic shock syndrome. SEMAPHORE aims to collect samples of *S. aureus* bacteria from infected people and find out what makes MRSA more likely to cause a recurrent infection after treatment. To do this, we swabbed study participants and took up to eight individual colonies from each swab. We also pooled the remaining colonies on each plate and stored them. Then, we sequenced the genomes of all of the samples, both individual and pooled.  At the time our study began we had pools and “collections'' of 8 single colony cultures from multiple body sites and from up to 5 quarterly time points after infection from 85 different study participants. As we had both single colony sequences and pooled sequences (“pool-seq”) from the same samples, we realized that our data gave us the chance to compare singles and pools to identify exactly what we would be missing if we only looked at singles. 

![Fig1](/images/2024-Vishnu-Fig1.png)

*Sampling strategy*


The main conclusions of our study were first, that there was significant variation in *S. aureus *populations from human skin, nose and throat that was missed in single colony cultures and second that pool-seq genomic data could be used to give a readout of the genomic diversity present. We found that 17 percent of samples contained multiple_ S. aureus_ strains, a higher number than previously appreciated. We also found that looking at only a single colony can miss important genetic variation in the total populations, such as the presence of genes involved in antibiotic resistance. We found the pool to have predicted resistance to at least one more class of antibiotics than what we found in the singles, meaning if we had picked only singles, we would have missed the full antibiotic resistance capabilities of the total population.

![Fig2](/images/2024-Vishnu-Fig2.png)

*On average, the pool is predicted to be resistant to at least one more class of antibiotics than one or even eight colonies combined. The black vertical line shows the median number of classes of antibiotics with predicted resistance for one colony, eight colonies combined, and the pool.*


So why do we need singles at all if we can always sequence the pools? For most laboratory tests, a single pure culture of bacteria is required to minimize experimental variability and maximize reproducibility, making the tests more robust. This however is difficult to do especially with heterogeneous pools where the diverse population can cause experimental variability. In that case, why not pick several individual singles and sequence them all? As one can imagine, this process is time and labor intensive, not to mention very cost-ineffective. 

Based on the results from our study, an effective sampling strategy to use if it is important not to miss pathogen genomic diversity while still performing phenotypic tests is to sequence one single colony and one pool- we call this “pool plus one”.  If the single and pool are highly similar (as was the case for many of the samples in our study), then we can conclude that there is only one strain in the sample and use the single for further analysis. If the pool looks significantly different, then it is possible the pool contains multiple strains. In this case, the pool can be plated to pick more individual colonies for further analysis. We highlighted multiple useful metrics in our study that can help in detecting pool heterogeneity, most of which were derived from default outputs of commonly used microbial genomics tools. We believe the methods we outlined will be a useful resource for clinical microbiologists and scientists studying within-host evolution of infectious bacteria. 

Isolation and examination of pure cultures in microbiology is an important aspect of linking pathogens to specific diseases, a notion that dates back to Robert Koch’s postulates published in 1890. However, we now have the genomics tools  to probe within-species diversity and we should consider how we sample bacteria to best make use of them. 

Genomes sequenced for this study are available under accession PRJNA918392. All code and raw data are available at [https://github.com/VishnuRaghuram94/GASP](https://github.com/VishnuRaghuram94/GASP) and [https://doi.org/10.6084/m9.figshare.24161634.v1](https://doi.org/10.6084/m9.figshare.24161634.v1), respectively.

This study was funded by NIH awards AI158452 and AI139188 . V.R. was supported by the NIH T32 AI138952 "Infectious Disease Across Scales Training Program (IDASTP).

*Reference*
[Raghuram V, Gunoskey JJ, Hofstetter KS, Jacko NF, Shumaker MJ, Hu Y-J, et al. Comparison of genomic diversity between single and pooled Staphylococcus aureus colonies isolated from human colonization cultures. Microb Genom 2023;9.: https://doi.org/10.1099/mgen.0.001111.](https://www.microbiologyresearch.org/content/journal/mgen/10.1099/mgen.0.001111)











