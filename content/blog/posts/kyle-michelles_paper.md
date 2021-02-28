---
title: "Genetics of strain-specific levels of toxin production in *Staphylococcus aureus*"
description: ""
author: ["Kyle Winston"]
date: 2020-05-11
featured_image: "/images/Kyle_Michelle_fig-4-full.png"
draft: false
---

(This article was written as part of Kyle's [Emory REAL program](https://www.sph.emory.edu/rollins-life/community-engaged-learning/real/index.html) studentship with the lab)

**Su, M., Lyles, J. T., Petit, R. A., III, Peterson, J., Hargita, M., Tang, H., Solis-Lemus, C., Quave, C. L., & Read, T. D. (2020). Genomic analysis of variability in Delta-toxin levels between *Staphylococcus aureus* strains. _PeerJ_, _8_, e8717. https://doi.org/10.7717/peerj.8717**

Extracellular toxins play a central role in many *Staphylococcus aureus* infections. However, producing toxins can take resources away from other cellular functions, and may in fact limit bacterial growth and come at a negative fitness cost. Therefore, bacteria have complex regulatory mechanisms to turn on and off the production of various toxins. In *S. aureus*, delta-toxin is an important contributor to disease severity, but little was known about the variation in production in different strains and which genetic factors influence it. In this paper Michelle Su used genome-wide association study (GWAS) methods and constructed machine learning (XGBoost) models to better understand strain variation in delta-toxin production and identify genetic loci that are important for delta-toxin production.

![](/images/Kyle_Michelle_fig-3-full.png)

<sup>**Associations of delta-toxin production to methicillin resistance, Agr type and clonal complex (Fig. 3 in the paper)** (A) Differences in delta-toxin production between MSSA and MRSA strains. (B) Differences in δ-toxin production between agr types. (C) Differences in δ-toxin production between CCs. *p &lt; .05, **p &lt; .01, ***&lt;.001.</sup>

Michelle found several interesting genomic associations with delta-toxin production. The four known accessory gene regulator (Agr) groups in _S. aureus _have previously been found to be linked to different levels of cytotoxicity. Michelle found that there were significant differences in delta-toxin production between the four Agr groups, with Agr I and IV having higher levels of delta-toxin production than Agr III. She also found that methicillin resistance was associated with a decreased delta-toxin production, which makes sense since methicillin resistance has been shown to interfere with Agr functions and thus limit toxin production. Finally, she characterized the delta-toxin production in different clonal complexes and found that the average production in CC30 strains was significantly lower than in CC45 and CC121 strains. This matches what would be expected from clinical findings relating clonal complexes and atopic dermatitis, the main disease associated with delta-toxin. Compared to their representation in colonization, CC30 is underrepresented in atopic dermatitis cases, where CC121 is overrepresented.

![](/images/Kyle_Michelle_fig-4-full.png)

<sup>**Impact of gene knockouts on δ-toxin production (Fig. 4 in the paper)** A subset of genes that were found to be significantly associated with δ-toxin by GWAS were tested for their effect of δ-toxin production. δ-toxin from transposon mutants from the Nebraska Transposon Mutant Library (NTML) was measured via HPLC. *p &lt; .05.</sup>


Michelle also examined the importance of several candidate genes on delta-toxin production. Using GWAS and machine learning methods, she identified several genetic variants that were potentially associated with delta-toxin production. Most of the identified variants were in genes involved in metabolic pathways, indicating that metabolic genes contribute to virulence phenotypes. By quantifying the delta-toxin production in mutants which had transposons inserted to knock out the candidate genes, she found that *agrA* and *carA* were necessary for delta-toxin production. In mutant strains delta-toxin could not produced. This effect of *carA* was a surprising finding, as it was previously uncharacterized. Experiments with *Caenorhabditis elegans* models demonstrated that mutants without functional *carA* genes had reduced infectivity, suggesting that delta-toxin has an important role in *S. aureus* infection. This finding can be used to guide further studies to understand why *carA* is necessary for delta-toxin production and how this gene plays a part in the mechanisms of *S. aureus* infection.

Overall, Michelle demonstrated that delta-toxin production in *S. aureus* is strain-specific and is regulated by a complex network of genes. While many of these genes modify the production of delta-toxin, *carA* was found to be essential. Additionally, Michelle demonstrated that GWAS and machine learning techniques can be successful in identifying the underlying genes for the delta-toxin phenotype and implementing genome-based toxin phenotype prediction.
