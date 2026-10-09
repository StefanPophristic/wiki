---
layout: default
title: Behavioral preprocessing
parent: Behavioral Analyses
nav_order: 1
has_children: False
writer: Aline-Priscillia Messi
section: "Behavioral"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

This page is the results of a larger behavioral analyses round-table from April 4th 2025.

# Cleaning steps

## Exclusion criteria

### At a participant level
- Have a trusted pilot before doing the experiment
- Can run an online pilot of people doing the participant which is a small pool to see if the experiment works
- What should the expected results be?
- Run basic descriptive statistics: mean accuracy, reaction time
- Check the buttons to see if people are getting things wrong repeatedly
- If people don’t score well then set a threshold
- Set a threshold based on how much you’re excluding participants; try to not exclude more than 20% of participants
- Be suspicious if more than 15% of participants => if running online then Prolific can be suspicious/sketchy
- Can be task dependent! If the task is not an attention check then check the relevant literature
- Applies if the task is a cognitive evaluation of something vs making sure
- !!!Look at behavioral when seeing if to exclude an MEG participant!!

### Individual trials (by item)
#### Hard cutoff
- Set a hard cutoff for reaction time to exclude any trials:
- Lower bound: 200-300ms
- Upper bound: 4-5s
- Upper bounds vary a lot by task and by the attention check that is being used!! Check the mean reaction times in the literature for the task that you have chosen and language
- Very important!!
- (though see https://quantling.org/~hbaayen/publications/BaayenMilin2010.pdf for 5ms - 500ms)

#### Relative cutoff
- Reject trials that are 3SD away from the participant mean reaction time (standard)
- Check the distribution of the reaction times for your items (cf winter book)
- Citations for Cutoff considerations:
  - Zandt, T. (2002). Analysis of response time distributions. In J. Wixted & H. Pashler (Eds.), Stevens handbook of experimental psychology, volume 4: Methodology in experimental psychology (pp. 461516). New York: Wiley.;
  - Ratclff, R. (1993). Methods for dealing with reaction time outliers. Psychological Bulletin, 114, 510532;
  - https://quantling.org/~hbaayen/publications/BaayenMilin2010.pdf
- Can also do a by item cutoff:
-   Across all participants to reject items that have a weird behavior
-   Can be prone to over-cleaning so not recommended

### Order of operations
1. Participant accuracy exclusion
2. Hard cutoffs
3. By participant exclusions (outliers)
4. By item
*!!!Order of hard cutoff vs relative cutoff matters!!!*
Otherwise you are getting rid of things that can influence each other!!!

### Data visualization and normality
#### Check the distribution of your data
- Plot RTs at the group level => then look at individuals if the group level looks weird
- Can be a decision to exclude them based on behavior or default exclude and then look deeper to include them
- Plot by condition averages
- Histogram color coded by condition => helpful to see if the distributions are different between conditions
- If they are different, are those differences expected, or does it seem like an artifact from a confound? (e.g. LDT you expect a right skewed distribution for all conditions)
- Focus on the main contrast and the one you have an expectation of

#### Normality
- Might be overblown
- Know what to expect from your paradigm
- Data tends to be right-skewed in lexical decision (in reaction time)
- Run the basic normality tests anyways!!
- If fail the tests then check if transforming the data fixes it

### Transformations

#### Logging for skewed distributions
- Most people just log RT BUT refer to zscoring section and some do both
- It is recommended to log then zscore => since logging is a non-linear transformation and can change the variance in the data

#### Z-scoring
- It is better to z-score predictor variables!!
- For likert scales is more informative because takes into account scale bias (different people will use the scale differently)
- For reaction times:
  - Removes the absolute difference between participants; is better to standardize the data so that we can have an ‘absolute’ measure of their performance
  - This makes it easier to do comparisons that are percentage-based for the items across participants
- Linear transformation so doesn’t matter
- To do: add specific variables that are logged and zscored for this section

https://lindeloev.github.io/shiny-rt/ → good link for wiki
