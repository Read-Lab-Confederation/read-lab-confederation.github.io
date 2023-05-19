---
title: "How I freed NCBI antibiogram data from XML" 
description: ""
author: ["Tim Read"]
date: "2023-05-18"
featured_image: "/images/Fig1-202302.png"
draft: false
---


# How I freed NCBI antibiogram data from XML


## TL;DR


* How I made a tidy data frame of >24,000 bacterial BioSample IDs with antibiograms and linked genomic sequence data using a customized R script.
* Code and data available on the [github site](https://github.com/Read-Lab-Confederation/antibiograms/blob/main/README.md).


## Antibiograms

Antibiotic resistant bacteria continue to become more common worldwide.  Diagnosis of antibiotic resistance is a frontline defense for fighting back against these dangerous pathogens.  Antibiograms are phenotypic tests used routinely in clinical settings to ascertain the profile of antibiotics that will effectively treat the infection.  More information about the variety of antibiograms and how they are refined and evolved can be found in these posts from the [Minnesota Department of Health ](https://www.health.state.mn.us/diseases/antibioticresistance/abx/antibiograms.pdf)and the [American Society for Microbiology](https://asm.org/Articles/2022/February/Updating-Breakpoints-in-Antimicrobial-Susceptibili).  My group is interested in using antibiogram phenotypes from whole-genome sequenced strains for developing novel genome-based prediction tools (Su et al; Wissel et al).


## How to get your hands on public antibiogram data 

NCBI has been [collecting antibiograms ](https://www.ncbi.nlm.nih.gov/biosample/docs/antibiogram/)and linking them to BioSample accessions, which can then be linked to a variety of other databases.  (I also looked briefly at the ENA (Matatmoros et al) and PATRIC (Antonopoulos et al) databases but only found a few records when using the general search term “antibiogram”. I may come back to investigate  these resources in the future but for now I concentrated on the NCBI.)  When I searched the Biosample database for the term”antibiogram” I got 483 hits.  These have a list of three-letter codes of drugs for the strain presumably has resistance, eg. “AMP-GEN-SSS-SXT”.  I suspected that there was more than that, and actually, if you refine the search using “antibiogram[filter]” you get thousands of hits (thanks Emily!) (Oddly, the results from “antibiogram[filter]” don’t include the ones from the search for just “antibiogram”).  Further, you can add a filter for strains which also have an SRA accession (potentially genome sequence data).  


![Fig1](/images/Fig1-202302.png)

The data is already extensive: - 25,046 BioSample accessions.  Looking through typical records, most had a table with 10 columns.

![Fig2](/images/Fig2-202303.png)

But a few (exclusively Mycobacterium tuberculosis, I think) had antibiogram tables with 6 columns.

![Fig3](/images/Fig3-202303.png)

When I tried to download these data I made the unpleasant finding that the standard text file (itself in a freakishly difficult format for parsing) didn’t contain the antibiotic tables.  In order to use the information I would have to work with the XML-formatted output file.

![Fig4](/images/Fig4-202303.png) 

## XML: Oh no!

It was something of a downer to find that the key data was in an XML format. I have always thought of XML as being particularly awkward to parse and have tried as hard as possible to avoid learning the necessary tools.  However, there was no alternative for this project.


## R scripting solutions

My goal was to get the XML-formatted download into a neat R dataframe linking the BioSample ID with one drug result on each row. Luckily I found a [nice blog post ](https://rstudio-pubs-static.s3.amazonaws.com/499292_d6edbb19b08f456097333fbf9443f9b7.html)by Stephen Howe  to help me get started.  He used the R XML library, which seems to be the most used tool for XML parsing, although there are several others that are probably at least as good.

First step was to upload the XML data.


```
library(XML)
library(tidyverse)
xml2 <- xmlParse("./biosample_result-4.xml")
xmltop2 = xmlRoot(xml2)
NumRecords = xmlSize(xmltop2)
```


The `xmlRoot `function gives the convenience of accessing the root node of the XML document.  I spent a bit of time exploring the structure of the XML database to formulate a plan (and tried a couple of preliminary strategies that didn’t eventually pan out). The major problem was that all the tables were appended as “comments” with unnamed rows and columns.  Eventually the solution I hit on was a function that made a dataframe using the row and columns for that record alone. The rows were split based on the column length (which could be 10 or 6) and added to the data frame sequentially.  It worked on the records I tested.  The function could probably be speeded up (looking at the `for` loop) but … good enough for now.


```
populate_rows <- function(k){
  # k is the record ID from the larger table named  xmltop2
  T_vals <- xpathSApply(xmltop2,paste0('//*[',k,']/Description/Comment/Table/Body/Row/Cell'),xmlValue)
  T_cols <- xpathSApply(xmltop2, paste0('//*[',k,']/Description/Comment/Table/Header/Cell'), xmlValue)
  numcols <- length(T_cols)
  numrows <- length(T_vals)/numcols
  df <- data.frame(BioSample = rep(xmlValue(xmltop2[[k]][['Ids']][[1]]),numrows),Organism = rep(xmlValue(xmltop2[[k]][['Description']][['Organism']]),numrows))   
  # add all the columns
  df[T_cols] <- NA
  #loop to add each row (faster structure possible?)
  for (x in 1:numrows) {
  vs = 1+(x-1)*numcols
  ve = 1+(x-1)*numcols+(numcols-1)
  df[x,3:(numcols+2)]=T_vals[vs:ve]
  }
return(df)
}
```


So the plan was to process all the samples using this function.  Then  I came across the ugly truth that a minority of samples had two tables in their comments and broke the `populate_rows` function. I could identify them by having two entries under their “comment” field.  I decide to filter these out and possibly come back to them at a later time.


```
all_comments <- sapply(1:NumRecords, function(x) xmlSize(xmltop2[[x]][['Description']][['Comment']]))
samples_to_process <- which(all_comments == 1)
table(all_comments)
all_comments
    1     2 
24286   760
```


To parse the results I used the <code>[map_df](https://purrr.tidyverse.org)</code> function of the amazing<code><span style="text-decoration:underline;"> purrr</span></code> library.  Super easy and seamlessly handled the problem of merging data frames of different lengths.  As an added bonus, it comes with a progress bar so that I could see that the mapping operation wasn’t stalling.


```
combined_df <- map_df(samples_to_process,populate_rows,.progress = TRUE)
```



## Results

This took 8 hours to run (!) so there are lots of reasons to go back and make it more efficient.  Parallelization could be possible as well.  I spot checked the resulting data frame of > 400,000 rows against random BioSample records and everything seemed OK. 

There are a few things to sort out in the future.  I dropped adding a SRA ID field because the XML was not consistent with how it represented this across records - it can be cross-linked through a separate search.  In some cases, also because of XML record variability, the Organism field was blank - this can also be fixed later.  It may also be worth a separate function to sort out the 760 strains with both types of tables.

I have made the scripts, input XML file and output in TSV format on a public [github site](https://github.com/Read-Lab-Confederation/antibiograms/blob/main/README.md).


## References


Su M, Satola SW, Read TD. Genome-Based Prediction of Bacterial Antibiotic Resistance. J Clin Microbiol 2019;57.: https://doi.org/10.1128/JCM.01405-18.](http://paperpile.com/b/5QSxYp/Ttfs)


Wissel EF, Talbot BM, Toyosato NAB, Petit RA, Hertzberg V, Dunlop A, et al. hAMRoaster: a tool for comparing performance of AMR gene detection software. bioRxiv 2023:2022.01.13.476279. https://doi.org/10.1101/2022.01.13.476279.](http://paperpile.com/b/5QSxYp/4cCC)


Matamoros S, Hendriksen RS, Pataki BÁ, Pakseresht N, Rossello M, Silvester N, et al. Accelerating surveillance and research of antimicrobial resistance - an online repository for sharing of antimicrobial susceptibility data associated with whole-genome sequences. Microb Genom 2020. https://doi.org/10.1099/mgen.0.000342.](http://paperpile.com/b/5QSxYp/0hYg)


Antonopoulos DA, Assaf R, Aziz RK, Brettin T, Bun C, Conrad N, et al. PATRIC as a unique resource for studying antimicrobial resistance. Brief Bioinform 2017. https://doi.org/10.1093/bib/bbx083.](http://paperpile.com/b/5QSxYp/zHW1)
