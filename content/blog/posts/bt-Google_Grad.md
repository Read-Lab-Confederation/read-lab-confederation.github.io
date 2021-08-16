---
title: "OK Google, How do I Learn Data Science?"
description: "An R how-to guide to finding your how-ho guides in Google Search history"
author: ["Brooke Talbot"]
date: "2021-08-16"
featured_image: "/images/bt-Image_5.jpg"
draft: false
---

Something that delights me as a data scientist is that concepts and techniques are very readily shared within the community. It's also astounding to me that each and every day we have access to an infinite body of knowledge which can help supplement formal and informal learning - namely, that we can gain a lot of information very quickly by turning to Professor Google! Investigating how we as scientists search this seemingly endless collection of facts and figures and code blocks, and for what exact purposes, can give some great insights into how we communicate and learn and ultimately improve our reseach.   

## Looking at my Google Search history over time

I decided to take on this task of exploring just exactly how I learned from the internet in the past few years. With some significant help and inspiration [from this delightful how-to guide](https://towardsdatascience.com/explore-your-activity-on-google-with-r-how-to-analyze-and-visualize-your-search-history-1fb74e5fb2b6) by Saúl Buentello, I extracted the Google Search history from my personal Google account and brought that data into RStudio.  

Using Buentello's excellent web scraping code and then taking a couple of trips around the [Tidyverse](https://www.tidyverse.org/) - voila! -  I was able to filter down and count just how often I have used Google Search since I created the account back in 2009.   

```
# LIBRARIES
library(lubridate)
library(rvest)
library(tidyverse)
library(magrittr)
library(data.table)


# CREATING SUBSET OF SCRAPED SEARCHED DATA FROM HISTORY
Searched <- searchedData[(searchedData$type == "Searched"),]


# OVERALL PLOT
allSearched <- ggplot(Searched, aes(x = year)) + 
               geom_bar(fill = "gray48") + 
               theme_classic() +
               labs(x= "Year", y= "Count") + 
               ggtitle("How much do I use Google Search over time?")
allSearched
```

![](/images/bt-Image_1.jpg)

Unsurprisingly, I had a huge spike in the number of searches in the year 2020, which is likely the result of a mix of the switch to a work-from-home environment ("how to update Zoom desktop"), new-demands at work ("how to get healthcare workers masks"), and taking a big step in starting my current PhD program at Emory, switching fields from epidemiology to a course of study more focused on microbial genetics ("what is Bioconda?").

## Strategies for paring down to learning-related searches

Of all these searches, though, I wondered what were the ones dedicated to teaching myself. I have been a student and/or early career scientist for the past 11 years and I know I have had a lot of questions. Personally, Google is my home search bar on my mobile device and my laptop, so many of my searches are lazy passageways to websites I want to access or funny pictures and videos I want to share, and it's also a decision making tool for finding whatever is a "[insert resource here] near me." 

I do have one idiosyncrasy which may be a hold-over from learning to use Ask Jeeves during elementary school computer science: I do, in fact, use question words for some of my searches. Although not a perfect dividing line, I figured this could be an excellent proxy for filtering down to searches that were instances where I was trying to learn something new. I used regular expressions to find common English question words (who, what, where, how, why, when) as well as the word "tutorial" and created a new variable which categorized my searches into questions and non-questions: 

```
#ADDED PADDING TO FRONT AND BACK OF STRING
Searched$search <- paste(" ",Searched$search, " ", sep = "")

#ASSIGNING QUESTION CATEGORY

Searched <- Searched %>% mutate(Questions = case_when(str_detect(search, "how to|How to| How | how | why|Why| who | Who | when|When|what|What|where|Where|tutorial") == "TRUE" ~ 1, TRUE ~ 0)) %>% mutate(Questions = as.factor(Questions))

Searched_Q_Sum <- Searched %>% group_by(year, month, day, Questions) %>% summarise(n = n()) 

questionsSearched <- ggplot(Searched_Q_Sum, aes(x = year, y=n, fill=Questions)) + 
                     geom_bar(position="stack", stat="identity") +
                     theme_classic() +
                     scale_fill_discrete(type = c("gray48","darkolivegreen2"),name = "Question Search", labels = c("No", "Yes")) +
                     labs(x= "Year", y= "Count") + 
                     ggtitle("How often do I ask Professor Google a question?")
questionsSearched
```

![](/images/bt-Image_2.jpg)

These phrases make up about 6.6% of all of my searches (2,528 of 38,269 searches!), which while proportionally small is still a tractable amount to work with. So far so good. But what about the kinds of things I am trying to learn with these questions?  

## Subsetting the computational questions 

I shifted fields when I started my PhD program, and with that shift also came a shift in computational languages. I had a sense that many of my more recent searches would probably be related to understanding R, Python, and Bash script, and I was curious what impact this sudden shift in learning would have on my search history. As I mentioned, the positions I have held during this time involved a lot of training, some of which also included learning other languages and softwares, so how much did my first year of self-driven PhD research alter my patterns?      

Similar to my "Questions" variable, I used regular expressions to find the searches with key phrases that I knew were most likely related to computation and research/work. This included names of programming languages, analytical softwares, hardware terms, common functions, file types and statistical concepts. I also sorted the searches with phrases that are related to analysis and bioinformatics while excluding phrases that were more likely to relate to general concepts in my field. For example the search "what is a consensus tree" is a computational search because I was almost certainly applying this directly to a phylogenetic tree analysis, but the search "what is the comparative method [in] evolutionary biology" is not computational because it relates to background knowledge I was gaining about a specific biological concept. I also excluded software or program names that were related to more social or leisure activities from the computation category,  such as Instagram and Facebook. So, what does that look like?

```
#SETTING UP COMPUTATIONAL VARIABLE

Searched <- Searched %>% mutate(Computational = case_when(str_detect(search, " r |axis|data|graph|Graph|label|plot|phylogenetic|vim|code| in r |unix|grep|linux|unix|commandline|ggtree|ggplot|vector|bash|invariant sites|fastq|fasta|sas|SAS|shell script|matrix| R|rmarkdown|alignment|gunzip|gzip|untar|conda|sql|iqtree|gz file|shortbred|github|command| row|python|software|calculate|incidence|odds ratio|tableau|endnote|snippy-core|.sh|qsub|compare|citations|consensus|consensus tree| png |robinson-| fna |n50|google search|browsing|fastly| median |heatmap|brewer.pal|sequence file|newscale fill|vcf|CFML|unite function| cp |SNP|dirpath| awk | vi |.exe|binary file|bam file|bootstrap value|ubuntu|control c|metagenomic|paired end reads|bowtie1|bowtie2| excel |Use_Dev|snippy|YAML|travis| gbk |unit test|variant calling|biobakery|statistical power|treeio|json|xmfa| tar |disty|parsnp|gingr|harvest tools|jupyter| wsl |confidence intervals|power calculations|maximum likelihood| proc |mantel haenszel|knit to powerpoint|xml|tidyverse| irr |powerpoint|t-test|download|iqr|attack rate|nodupkey|pdf|stdev.s|set statements|access=read| e value|MAVEN|maven|DCPHIS|propensity score|$500.|best32|positive predictive value|attributable risk|effect measure modification| translate a page|prevalence ratio| bic number|negative predictive|NPV|image j|imageJ|P drive|I drive|heading error|\r|parallel|bioinformatics|genbank") == "TRUE" & Questions == 1 ~ 1, TRUE ~ 0)) %>% mutate(Computational = as.factor(Computational))

Searched_C_Sum <- Searched %>% group_by(year, month, day, Questions, Computational) %>% summarise(n = n()) 


computationalQs <- ggplot(Searched_C_Sum[which(Searched_C_Sum$Questions=="1"),], aes(x = year, y=n, fill=Computational)) + 
                     geom_bar(position="stack", stat="identity") +
                     theme_classic() +
                     scale_fill_discrete(type = c("gray48","darkolivegreen2"), name = "Computational Question", labels = c("No", "Yes")) +
                     labs(x= "Year", y= "Count") + 
                     ggtitle("How often am I asking for computational help from Professor Google?")
computationalQs
```
![](/images/bt-Image_3.jpg)

It's quite shocking to me that proportionally the year 2020 really took a hold of my computational questions. After all, I really started to learn how to work with robust statistical programs in 2016, and a lot of my job as an epidemiologist revolved around trying (and failing and trying again) to build and analyze large datasets. One reason for the shift could be that I really did take on learning much more computation in the last year. Other reasons, too, could be related to a bias in my search behavior changing in 2020, including using the question format more frequently (Sorry Jeeves!), or switching from a work device which is sometimes logged into my account versus a personal device which is almost always logged into my account.  

In any case, there are any number of other ways to query these data, and Buentello's guide has some lovely examples for breaking up searches by week, month, and time of day. For fun, I thought I would look at my total question searches by day of the week: 

```
#COMPUTATIONAL QUESTIONS BY DAY

ComputationalQsDay <- ggplot(Searched_C_Sum[which(Searched_C_Sum$Questions=="1"),], aes(x = day, y=n, fill=Computational)) + 
                     geom_bar(position="stack", stat="identity") +
                     theme_classic() +
                     scale_fill_discrete(type = c("gray48","darkolivegreen2"), name = "Computational Question", labels = c("No", "Yes")) +
                     labs(x= "Weekday", y= "Count") + 
                     ggtitle("How often do I ask for computational help by weekday?")
ComputationalQsDay
```
![](/images/bt-Image_4.jpg)

I guess I am quite inspired or wired on Wednesdays!

And if I zoom in on 2020 to look at the change-over to becoming a student, starting in the Fall:

```
# AND BREAKING OUT BY MONTH IN 2020

Day_years  <- ggplot(Searched_C_Sum[which(Searched_C_Sum$Questions=="1" & Searched_C_Sum$year %in% c("2020")),], aes(x = day, y=n, fill=Computational)) + geom_bar(position="stack", stat="identity") + 
  facet_wrap(vars(month)) + 
  theme_classic() +
  theme(strip.background = element_blank(), 
        axis.text = element_text(size = 10), 
        strip.text.x = element_text(size = 12, face = "bold"), 
        title = element_text(size = 16)) +                                                   
  scale_x_discrete(labels = c("Sun", "Mon", "Tues", "Wed", "Thurs", "Fri", "Sat")) +
  scale_fill_discrete(type = c("gray48","darkolivegreen2"), name = "Computational Question", labels = c("No", "Yes")) +
  labs(x= "Weekday", y= "Count", title = "What days did I ask a computational quesiton in 2020?" ) 

# SILLY LABEL TEXT
ann_text <- data.frame(day = "Wednesday", n = 29 ,lab = "Text",
                      month = factor("Sep", levels = c("Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec")), Computational = factor("0", levels = c("0","1")))

Day_years + geom_label(data = ann_text,label = "Seriously, what is Bioconda?", size = 4, show.legend = FALSE, fill = "white" )

```
![](/images/bt-Image_5.jpg)

I guess the Wednesday trend and the volume increase really does line up with the start of the semester! 

## Google search history is pretty much brain vomit 

A couple of issues came up in sorting my final data. For one, trying to sort out question words was imperfect when faced with commonly searched acronyms in my field. As someone involved in Public Health research, the World Health Organization ("WHO") came up a fair number of times. Similarly, trying to use a word like "guide" resulted in a similar mix of my own education and work-related references, and the very revealing fact that The Hitchhiker's Guide to the Galaxy is my favorite book. Secondly, "computational" definitely became a hunt-and-peck adventure when using regular expressions. I also recognize that I have other more chaotic ways of searching for help with my code, and ultimately that means those searches were excluded from this particular analysis, resulting in a major under-representation of my dependency.   

## What's Next?

Nevertheless, I think this was an excellent introspective project, and got me thinking about how to search and sift through research data by picking up on patterns that can act as flags for a final analysis. Your search history is a rich source of data, so it makes for fun practice both for the techniques and for developing interesting questions. For the future, I think it would be interesting to see if there is a pattern in the sites I actually visit as a result of these searches, as these are likely to have a bit more repetition and standardization in the free text. Perhaps as I improve my skills during my studies I will come up with even better ways to tackle this question of "How do I use the internet to learn?" and I look forward to seeing how the trends change in four- or five-years time.   

## TL;DR

- Google Search history can be readily brought into R, scraped, and used to detect patterns in learning or self-teaching, especially when it comes to computational learning. 
- I detected a really interesting increase in how I use Google, particularly regarding how to learn how to conduct computational techniques for my research, as soon as I became a PhD student. 
- Working with my Google Search history was excellent and fun practice in regular expressions and graphical display. 
- You can find [the full code here](https://github.com/bmtalbot/Google-Search-History/blob/main/README.md) to create these graphs and play around with the computational and question variables, and I highly recommend [this how-to guide](https://towardsdatascience.com/explore-your-activity-on-google-with-r-how-to-analyze-and-visualize-your-search-history-1fb74e5fb2b6) for scraping your Google Search history.
