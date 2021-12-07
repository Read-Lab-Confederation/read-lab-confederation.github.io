---
title: "Bacterial strain names and Biosample IDs"
description: ""
author: ["Tim Read"]
date: 2020-03-15
featured_image: "/images/biosample_logo.png"
draft: false
---

The public databases maintained by NCBI and other members of the International Nucleotide Sequence Database Collaboration [(INSDC)](http://www.insdc.org/) are central resources for bacterial bioinformatic analysis.  Two key concepts to know about working with these databases are **BioProject** and **BioSample**.  These were created<sup><a href="http://dx.doi.org/10.1093/nar/gkr1163" target="_blank">1</a></sup> at NCBI as genome data began to gush in, at the beginning of the 2010s following the development of “second generation” sequencing technologies.  The structure is mirrored between the INSDC databases, although IDs for the same data can be different.

[BioProject](https://www.ncbi.nlm.nih.gov/books/NBK169438/) was created to be attached to one or more BioSamples and hold metadata about the study. BioProject IDs are often cited in manuscripts that introduce primary data submission.

[BioSample](https://www.ncbi.nlm.nih.gov/books/NBK169436/) tracks distinct biological specimens.  In our studies, this typically means a bacterial strain, though it could also be a human cell line, plant or other biological entity.  BioSamples should have a strain name and contain metadata about the strain provenance (e.g. date of isolation, place, host species).  They are also linked to the NCBI Taxonomy database and, if DNA sequencing has been performed, there are links to SRA Experiment and Run tables.  Assemblies submitted to NCBI are linked to BioSample as are many NCBI databases.  Theoretically, a BioSample ID could be linked to one, multiple or zero BioProjects.

Often, our group wants to get all public DNA sequencing runs associated with a species (or a BioProject or other identifier).  This can be easily done through NCBI with the [SRA Run Selector tool](https://www.ncbi.nlm.nih.gov/Traces/study/). (Note, here the “SRA_Study” ID appears to map to a single BioProject).


## Searching for strains in published articles

This is great, but suppose you are looking for publications that mention a particular sequenced strain.  This may happen after making an interesting discovery based on a sequence analysis - for example, you detect an unusual toxin gene in that genome.  You want to find out more about that strain and if any subsequent analysis has been published.  In almost all cases, microbiologists like to use strain names instead of BioSample IDs when they refer to genomes. There are no guidelines for strain names, everyone makes up their own system. Often these names are not good for searching the internet because of their ambiguity - for instance “A12” or “London”. The same name may also be typed differently by different groups, e.g “B+24”,”B 24”, “B-24”, further confounding searches.  Sometimes, when a strain is shipped to another lab, it is given a new accession number - ie the same strain often has multiple names (which themselves may not even uniquely identify that strain in internet searches).  If you try internet searches using the strain name you may get no results at all (perhaps you used the wrong version of the name?), or you get too many and you can’t read through every paper.  Also, can you be sure that the “_E. coli_ A12” you find really the strain that matches the sequenced genome?

One solution would be for microbiologists to create unique strain names (but I don’t know how it would be possible to enforce this). Alternatively, maybe a simpler approach would be to use BioSample ID’s instead of (or in addition to) arbitrary strain names. If you register a genome sequence in a public INSDC database, you have to create a BioSample ID anyway. The BioSample ID is a precise way to assign a unique identifier to bacterial strains.  It can even help with the problem of tracing isolates of the same strain that have been shared between laboratories. If you search the BioSample database for common lab strains you will often see multiple BioSample IDs for a given strain name (e.g. look for “_Staphylococcus aureus_ N315”).  This means the same strain (and mutant variants) have been sequenced in different labs.  This is a *good thing* because it theoretically enables you to discern different lineages of common strains that may have evolved different phenotypic properties<sup><a href="http://dx.doi.org/10.1111/j.1365-2958.2007.05710.x" target="_blank">2</a></sup>.  

Unfortunately, BioSample IDs are rarely used in the text of scientific articles. It is rare to see BioSample ID in papers where the original genome sequencing of the strain was not published.  This is a shame because these follow-on studies on sequenced strains add to the overall metadata picture and can be valuable for linking phenotype to genotype.

We confronted the problem of trying to link literature to strains in 2018 when we were searching for metadata on 43,000+ _Staphylococcus aureus_ public genomes for the Staphopia database<sup><a href="http://dx.doi.org/10.7717/peerj.5261" target="_blank">3</a></sup>.  Based on what was reported to NCBI at the time of database submission, only 6,700 strains could be matched to citing articles.  We built our own database of several thousand PDFs of _S. aureus_ papers compiled using the Paperpile citation manager software and individually matched them against every BioProject (Study), BioSample, Run and Experiment ID using the Mac ‘mdfind’ command.   Using this method we found an additional 5,200 strains linked to citations.  This meant that more than 31,000 strains listed in the NCBI database were effectively invisible to literature search despite our best efforts.

If BioSample IDs were used in text more frequently, how could we find them without going to the trouble of downloading thousands of PDFs?  Google Scholar indexes the full text of many scientific articles and it is possible to identify Project and BioSample IDs.  Similarly, [PubMed Europe](https://europepmc.org/)) does full text indexing of 5.8 million open access articles. Importantly, neither appears to index the article’s supplemental material - searches only match IDs that are in the main body of the text.  If you are looking for a particular BioSample ID but are returned a negative, you can infer its presence of the paper by searching for linked IDs - e.g [BioProject, Run and Experiment](https://www.ncbi.nlm.nih.gov/sra/docs/submitmeta/)), as we did in the Staphopia analysis above. My group is trying to be more sensitive to this issue. In our recent BioRxiv preprint describing the Bactopia pipeline<sup><a href="https://www.biorxiv.org/content/10.1101/2020.02.28.969394v1.abstract" target="_blank">4</a></sup> where we reanalysed > 1,600 _Lactobacillus_ genomes, we made a list of the 300+ BioProject IDs where we drew data from in the main part of the text where they will be indexed by Google Scholar. This was more efficient than listing 1,600 BioSample IDs, even though in some cases there were strains in BioProjects that were not part of our analysis.


## Conclusions/ Recommendations

Unfortunately, few microbiologists now use BioSample ID but we could start to adopt them as sort of the ORCID of bacterial strain naming.  We should try to use them in article text (quoting strain and BioSample ID together). If many strains are referenced the article, an alternative would be to include a table of information about strains with name, BioSample ID and other metadata. If possible, these data should not be consigned to Excel sheets in the supplementary information, which don't seem to be indexed by most search engines. Most scientists when writing papers don’t consider how other scientists use internet search engines to find their information.  They should, because text search-ability it is one of the huge advantages of open access publishing.  The easier it is to find terms and names through search engines such as Google Scholar and PubMed, the more your article is read and likely, the more citations your work will get.


## References

1. [Barrett T, Clark K, Gevorgyan R, Gorelenkov V, Gribov E, Karsch-Mizrachi I, Kimelman M, Pruitt KD, Resenchuk S, Tatusova T, Yaschenko E, Ostell J. BioProject and BioSample databases at NCBI: facilitating capture and organization of metadata. Nucleic Acids Res. 2012 Jan;40 Database issue:D57–63.](http://dx.doi.org/10.1093/nar/gkr1163)

2. [Hobman JL, Penn CW, Pallen MJ. Laboratory strains of Escherichia coli: model citizens or deceitful delinquents growing old disgracefully? Mol Microbiol. 2007;64(4):881–885.](http://dx.doi.org/10.1111/j.1365-2958.2007.05710.x) 

3. [Petit RA III, Read TD. Staphylococcus aureus viewed from the perspective of 40,000+ genomes. PeerJ. 2018 Jul 12;6:e5261.](http://dx.doi.org/10.7717/peerj.5261)

4. [Petit RA, Read TD. Bactopia: a flexible pipeline for complete analysis of bacterial genomes. bioRxiv. 2020. p. 2020.02.28.969394.](https://www.biorxiv.org/content/10.1101/2020.02.28.969394v1.abstract)