---
title: "Assembling ancestral Haemophilus influenzae genomes with nanopore and Illumina data"
description: ""
author: ["Abraham (Jon) Moller"]
date: "2026-04-15"
featured_image: "/images/posts/2026-04-15-ag-molle-ancestral-hflu-genomes/fig-1-coverage.png"
draft: false
---

**By [Abraham (Jon) Moller](https://www.linkedin.com/in/abraham-jon-moller-258a8928/)**

*This is an example of coassembly of nanopore and Illumina reads to complete bacterial genomes. The complete GA81666 and GA54827 genomes were used as the basis for analysis in Bixler et al. 2025 [^1].*

## Methods

### Nanopore sequencing and sequence assembly

*Haemophilus influenzae* strain GA81666 and GA54827 genomic DNA were extracted using the Promega Wizard Genomic DNA Purification Kit. Sequencing libraries were prepared using the SQK-LSK109 1D ligation sequencing kit and sequenced on a FLO-FLG001 Flongle flow cell, yielding 496.9 Mb and 552.6 Mb of raw reads (~267x and ~297x coverage) for GA81666 and GA54827, respectively. The *H. influenzae* GA81666 and GA54827 genomes were then assembled from Nanopore and Illumina paired-end reads using Unicycler (Wick et al., 2017).

### Coverage-assembly association

Nanopore reads were subsampled to 2, 3, 5, 10, 20, 30, 40, and 50-fold coverage with seqtk (Li, 2012). Respective assemblies were then constructed using Unicycler with the Illumina reads and each corresponding set of nanopore reads. The number of contigs was then analyzed with respect to nanopore coverage to identify the minimum nanopore coverage necessary to generate a closed, complete assembly (i.e., 1 large genomic contig). Mutations were called in the assemblies relative to the Illumina reads using breseq (Barrick et al., 2014).

## Results

The final GA81666 Unicycler assembly included one circular 1.875 Mb contig, while the final GA54827 Unicycler assembly included one circular 1.885 Mb and one circular 37.6 Kb contig. Increasing the nanopore coverage from 2 to 50-fold closed the larger genomic contig for both strains at 10x coverage. The GA81666 assembly always included two contigs over this nanopore coverage range. The GA81666 larger contig became circular at 2-fold coverage, while the GA54827 larger contig first became circular at 50-fold coverage. The GA81666 and GA54827 assemblies also always included a 62.7 Kb and a 37.6 Kb circular contig, respectively. While the GA81666 assemblies never had any detectable mutations relative to the Illumina reads alone, as nanopore coverage increased, the total number of mutations increased and then decreased for the GA54827 assemblies, indicating better quality assembly. While the number of large deletions fell to zero with increasing coverage, the number of base substitutions increased from 0 to 1 (at 5-fold coverage) and remained such for the rest of the coverage range examined.

## Figures

**Figure 1:** Relationship between the number of contigs generated and input nanopore coverage for *H. influenzae* strain GA54827 and GA81666 Unicycler genome assemblies. Nanopore reads were subsampled from 2 to 50-fold coverage with seqtk and used to construct complete assemblies with Unicycler. Number of output contigs was then plotted relative to input nanopore coverage.

![Figure 1: Contigs vs. nanopore coverage](/images/posts/2026-04-15-ag-molle-ancestral-hflu-genomes/fig-1-coverage.png)

**Figure 2:** Relationship between the number of breseq-detected mutations and input nanopore coverage for *H. influenzae* strain GA54827 Unicycler genome assemblies. Nanopore reads were subsampled from 2 to 50-fold coverage with seqtk and used to construct complete assemblies with Unicycler. Mutations were called for all assemblies relative to the Illumina reads using breseq. Total number of mutations, as well as the count for each type of mutation, were then plotted relative to input nanopore coverage.

![Figure 2: Mutations vs. nanopore coverage](/images/posts/2026-04-15-ag-molle-ancestral-hflu-genomes/fig-2-coverage.png)

## References

[^1]: Bixler BJ, Royer CJ, Petit RA III, Moller AG, Sefton S, Thomas S, et al. Comparative genomic analysis of emerging non-typeable *Haemophilus influenzae* (NTHi) causing emerging septic arthritis in Atlanta. *PeerJ* 2025;**13**:e19081. https://doi.org/10.7717/peerj.19081
