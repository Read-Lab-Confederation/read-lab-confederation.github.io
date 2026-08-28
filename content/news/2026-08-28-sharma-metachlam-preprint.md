---
title: "New Preprint by Parul Sharma: MetaChlam pipeline for typing Chlamydia trachomatis in metagenomes"
date: 2026-08-28
draft: false
---

[Dr. Parul Sharma](/group/parul-sharma/) has a new preprint on bioRxiv: ["Detecting and typing *Chlamydia trachomatis* strains in metagenomes using the MetaChlam pipeline"](https://www.biorxiv.org/content/10.64898/2026.08.18.745514v1).

Metagenomes from the sites *C. trachomatis* infects — endocervix, conjunctiva, rectum — rarely contain enough reads for traditional genotyping methods such as MLST or *ompA* genotyping. MetaChlam is an ensemble Nextflow pipeline that combines LINtax, StrainScan, StrainGE, and Sourmash with custom databases to classify *C. trachomatis* strains from as few as 250 reads. Using 109 publicly available genomes, the authors established a 99.75% ANI threshold for distinguishing strains, and showed on simulated data that the pipeline correctly identifies strains in both single-strain and multi-strain mixtures. Applied to NCBI SRA metagenomes, MetaChlam showed higher specificity than NCBI PebbleScout — and turned up the surprising result that *C. trachomatis* reads appear as contaminants in samples from body sites where this obligate intracellular pathogen is almost certainly absent.

The pipeline is available at [github.com/parul-sharma/MetaChlam](https://github.com/parul-sharma/MetaChlam).
