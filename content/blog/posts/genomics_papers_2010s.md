---
title: "Seven significant bacterial genomics papers from the 2010s"
description: ""
author: ["Tim Read"]
date: 2020-01-08
featured_image: "/images/2020-01-08_blogpost.jpg"
draft: false
---

If you recognize the birth of bacterial genomics as the 1995 publication of the complete genome sequence of _Haemophilus influenzae_ Rd<sup><a href="http://www.ncbi.nlm.nih.gov/pubmed/7542800">1</a> </sup>then the field reached the beginning of the 2010s in adolescence and by the end of the decade had matured into adulthood.  Much of the progress was driven by the steadily increasing output and decreasing costs of the workhorse Illumina short read sequencing platform.  This meant more genomes per dollar, and with higher per base coverage, relatively few errors in the assembled portions of the genome. However, it has not all been about just churning out more of the same but for less money, the 2010s saw a huge number of new applications and finding new questions to answer using genomics.  The PacBio and Oxford Nanopore long read sequencing platforms became increasingly influential toward the end of the decade and found their own distinctive niches.

Here is a list of seven great papers that emplexify some of the trends. They were originally chosen for a student who asked for an introduction to the field.  The list was drawn from my own reading, which is, unsurprisingly, highly biased towards what interests me the most - infectious diseases and in particular _Staphylococcus aureus_. I am not claiming that each is a “first” or the “best” in its arena - there are lots of other great papers that could have been picked. They were chosen because I enjoyed reading them and they conveyed some of the excitement of innovative genomics opening new research questions.  I have ordered them chronologically and given each a brief contextual summary. 

**2010: Harris et al “Evolution of MRSA During Hospital Transmission and Intercontinental Spread”<sup><a href="http://dx.doi.org/10.1126/science.1182395">2</a></sup>**

At the beginning of the decade there was scepticism in some quarters about the value of draft Illumina genome assemblies. This paper demonstrated that S. aureus MRSA strains belonging to the same sequence type (therefore unresolvable by older genotyping schema such as MLST) could accurately be placed on a whole-genome phylogeny that reflected patterns of intercontinental transmission.  It was an important proof-of-concept for using cheap Illumina sequence to distinguish very closely-related genomes and a forerunner of numerous bacterial genomic epidemiology papers.

**2011 Lieberman et al “Parallel bacterial evolution within multiple patients identifies candidate pathogenicity genes”<sup><a href="http://dx.doi.org/10.1038/ng.997">3</a></sup>**

The 2010s saw a flourishing of genomics studies mapping evolution of bacteria during host infection. In this paper, 112 _Burkholderia dolosa_ strains from 14 people chronically infected with cystic fibrosis were sequenced. Although there were only a few genetic differences between strains taken from the same patient there were enough to infer patterns of movements between body sites (lung and bloodstream).  The authors also showed that similar genes were mutated in strains from different patients, to an extent that was unlikely to happen by random chance.  Evolution in these genes may help bacteria evade the immune response and survive long-term in their host. 

**2014 Laabei et al “Predicting the virulence of MRSA from its genome sequence”<sup><a href="http://dx.doi.org/10.1101/gr.165415.113">4</a></sup>**

With cheaper whole-genome sequencing, bacterial GWAS (genome-wide association studies) emerged as a strategy to associate genetic variants with phenotypes.  In this paper 90 sequenced MRSA strains were tested in the laboratory for their level of toxin production.  A number of putatively associated variants were identified from GWAS scanning and then experimentally validated by testing mutant strains in a mouse model.  Not only was this one of the first bacterial GWAS reports but it illustrated nicely the future of connecting bioinformatics with “classic” microbial molecular genetics. (I’m a bit biased here as I was visiting Ruth Massey’s lab on a summer sabbatical while this was going on and ended up a co-author).

**2017: Good et al “The dynamics of molecular evolution over 60,000 generations”<sup><a href="http://dx.doi.org/10.1038/nature24287">5</a></sup>**

Genomics became an increasingly important part of bacterial experimental evolution studies over the decade. The most famous study is Richard Lenski’s long-term evolution experiment, where twelve lines of E. coli strains have been propagated on defined media for more than 60,000 generations. In this paper, a pooled sequencing approach was used to find allele frequency changes across the genome compared to the ancestral strains every 500 generations. From this extremely rich dataset examples of epistasis and genetic parallelism were detected and the cultures were shown to be continuously evolving despite the simplicity of the experimental setup. 

**2017: Devault et al “A molecular portrait of maternal sepsis from Byzantine Troy”<sup><a href="https://elifesciences.org/content/6/e20983](https://elifesciences.org/content/6/e20983">6</a></sup>**

Bacterial DNA can often be extracted from ancient human remains.  In the 2010s there were several papers revealing striking insights into the history of pathogens such as _Yersinia pestis_, _Salmonella typhimurium_ and _Mycobacterium tuberculosis_ through reconstruction of whole genomes from Illumina sequenced metagenomes of bone and tooth samples.   In this paper, genomes of vaginal bacteria _Gardnerella vaginalis_ and from a woman who died about 800 years ago were reconstructed and compared against contemporary strains.  The G. vaginalis genome fell within a known clade found but the S. saprophyticus was from a lineage that does not frequently cause human disease today.

**2018: Doron et al “Systematic discovery of antiphage defense systems in the microbial pangenome”<sup><a href="http://science.sciencemag.org/content/early/2018/01/24/science.aar4120">7</a></sup>**

Over the course of the decade, the number of completed bacterial genomes grew dramatically, offering opportunities for genetic discovery through re-analysis of data deposited into the public domain by other research groups.  This paper used a clever approach to identify genes that protected the bacteria from phage attack based using a database of 45,000 sequenced bacterial genomes.  The premise was that similar functions tend to cluster together on chromosomes, so known phage defense genes could be used as a “bait” to find co-located genes across strains.  This type of unbiased strategy to ascribe function to “hypothetical” genes works best with large databases of high quality genome data.

**2019: Břinda et al “Rapid heuristic inference of antibiotic resistance and susceptibility by genomic neighbor typing”<sup><a href="https://www.biorxiv.org/content/10.1101/403204v2">8</a></sup>**

Oxford Nanopore technology produces long read data within minutes of starting the sequencing run, offering interesting potential for using whole genome sequencing as a clinical diagnostic.  This article shows how antibiotic resistance can be called in real-time for _Streptococcus pneumoniae_ and _Neisseria_ _gonorrhoeae_, based partly on inferring which subclade of the species the strain sequenced belongs to.  The algorithm uses databases of already sequenced genomes from each species to make these predictions - as we sequence more deeply in the coming years the new data should make these types of approaches more accurate.


## References


1. 	[Fleischmann RD, Adams MD, White O, Clayton RA, Kirkness EF, Kerlavage AR, Bult CJ, Tomb JF, Dougherty BA, Merrick JM. Whole-genome random sequencing and assembly of Haemophilus influenzae Rd. Science [Internet]. 1995 Jul 28;269(5223):496–512. Available from: http://www.ncbi.nlm.nih.gov/pubmed/7542800 PMID: 7542800](http://www.ncbi.nlm.nih.gov/pubmed/7542800)


2. 	[Harris SR, Feil EJ, Holden MTG, Quail MA, Nickerson EK, Chantratita N, Gardete S, Tavares A, Day N, Lindsay JA, Edgeworth JD, de Lencastre H, Parkhill J, Peacock SJ, Bentley SD. Evolution of MRSA During Hospital Transmission and Intercontinental Spread. Science [Internet]. 2010;327(5964):469–474. Available from: http://dx.doi.org/10.1126/science.1182395](http://dx.doi.org/10.1126/science.1182395)


3. 	[Lieberman TD, Michel J-B, Aingaran M, Potter-Bynoe G, Roux D, Davis MR, Skurnik D, Leiby N, Lipuma JJ, Goldberg JB, McAdam AJ, Priebe GP, Kishony R. Parallel bacterial evolution within multiple patients identifies candidate pathogenicity genes. Nat Genet [Internet]. Nature Publishing Group; 2011;43(12):1275–1280. Available from: http://dx.doi.org/10.1038/ng.997](http://dx.doi.org/10.1038/ng.997)


4. 	[Laabei M, Recker M, Rudkin JK, Aldeljawi M, Gulay Z, Sloan TJ, Williams P, Endres JL, Bayles KW, Fey PD, Yajjala VK, Widhelm T, Hawkins E, Lewis K, Parfett S, Scowen L, Peacock SJ, Holden M, Wilson D, Read TD, van den Elsen J, Priest NK, Feil EJ, Hurst LD, Josefsson E, Massey RC. Predicting the virulence of MRSA from its genome sequence. Genome Res [Internet]. Cold Spring Harbor Lab; 2014 May;24(5):839–849. Available from: http://dx.doi.org/10.1101/gr.165415.113 PMCID: PMC4009613](http://dx.doi.org/10.1101/gr.165415.113)


5. 	[Good BH, McDonald MJ, Barrick JE, Lenski RE, Desai MM. The dynamics of molecular evolution over 60,000 generations. Nature [Internet]. 2017 Oct 18; Available from: http://dx.doi.org/10.1038/nature24287 PMID: 29045390](http://dx.doi.org/10.1038/nature24287)


6. 	[Devault AM, Mortimer TD, Kitchen A, Kiesewetter H, Enk JM, Brian Golding G, Southon J, Kuch M, Duggan AT, Aylward W, Gardner SN, Allen JE, King AM, Wright G, Kuroda M, Kato K, Briggs DEG, Fornaciari G, Holmes EC, Poinar HN, Pepperell CS, Perry GH. A molecular portrait of maternal sepsis from Byzantine Troy. eLife Sciences [Internet]. eLife Sciences Publications Limited; 2017 Jan 10 [cited 2017 Jan 10];6:e20983. Available from: https://elifesciences.org/content/6/e20983](https://elifesciences.org/content/6/e20983)


7. 	[Doron S, Melamed S, Ofir G, Leavitt A, Lopatina A, Keren M, Amitai G, Sorek R. Systematic discovery of antiphage defense systems in the microbial pangenome. Science [Internet]. American Association for the Advancement of Science; 2018 Jan 25 [cited 2018 Jan 25];eaar4120. Available from: http://science.sciencemag.org/content/early/2018/01/24/science.aar4120](http://science.sciencemag.org/content/early/2018/01/24/science.aar4120)


8. 	[Břinda K, Callendrello A, Ma KC, MacFadden DR, Charalampous T, Lee RS, Cowley L, Wadsworth CB, Grad YH, Kucherov G, O’Grady J, Baym M, Hanage WP. Rapid heuristic inference of antibiotic resistance and susceptibility by genomic neighbor typing [Internet]. bioRxiv. 2019 [cited 2018 Aug 29]. p. 403204. Available from: https://www.biorxiv.org/content/10.1101/403204v2](https://www.biorxiv.org/content/10.1101/403204v2)


