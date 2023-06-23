R Basics for Lecture 1 A
================

## Econ B2000, Statistics and Introduction to Econometrics

## Kevin R Foster, Colin Powell School, the City College of New York, CUNY

## Variable Coding

Some of the HouseholdPulse variables here have a natural interpretation,
for instance TBIRTH_YEAR is measured in years. Actually even this has a
bit of a twist, look at the histogram.

``` r
hist(TBIRTH_YEAR[(TBIRTH_YEAR < 1950)])
```

![](lecture_1A_HHPulse_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

There is a bit of weirdness in the left, where it looks like there are
suddenly a bunch of people who were born in 1935 but many fewer in 1936
or 1938. This is due to a coding choice by the Census, where really old
people are labeled as born in “1935” (top-coding) but it actually should
be interpreted as meaning “in 1935 or before”. So if you were to get
finicky (and every good statistician is!) you might go back to the
calculations of averages previously and modify them all. For instance,
to select just those who are female and who are coded as having birth
year after 1935. Many variables are topcoded! *And recall that topcoding
wouldn’t change the median values calculated before, which is a point in
favor of that statistic.*

``` r
mean(TBIRTH_YEAR[ (GENID_DESCRIBE == "female") & (TBIRTH_YEAR > 1935) ]) 
```

    ## [1] 1971.578

You go make those other changes, figure out how top-coding changes the
calculations of average age by gender – I’ll wait right here…

## Variable Coding Again

So we were saying that some variables, like Birth Year - ahem! – have a
natural interpretation as a number.

Others are logical variables (called dummies) like GENID_DESCRIBE,
RHISPANIC, or RECVDVACC - there is a series of yes/no answers that are
coded 1/0. *Note that if you’re creating these on your own it’s good to
give names that have that sort of yes/no answer, so a variable named
‘RECVDVACC’ is good, since it’s easy to remember that Yes or No answers
“did the person receive a vaccine”. If that were labeled as ‘vaccstatus’
then you’d have to remember who are coded as true and who are false.*

Dummy variables get A TON of use and the R language provides lots of
help with them – they’re called factors in the R language. Basically a
factor lumps together a bunch of 0/1 answers.

The factor, ‘RHISPANIC’, is a single y/n or 0/1 answer: is the
respondent Hispanic. But then EEDUC is a whole bunch of y/n answers: is
the person’s highest completed educational level less than high school;
is the person’s highest completed educational level some high school;
did they end after getting high school diploma; etc. When you ask for a
summary of EEDUC,

``` r
summary(EEDUC)
```

    ## less than hs      some hs   HS diploma    some coll    assoc deg     bach deg 
    ##          435          911         7583        12638         6118        16668 
    ##      adv deg 
    ##        14937

R helpfully provides labels for each of those all together. Later when
we get into more depth, we might have to dig into the y/n answers that
are underneath.

It’s important to remember that there are some cases where the factor
values are well ordered (as with highest educational qualification)
versus others such as EST_ST (the state the person lives in) where there
is not necessarily an ordering.

Factors are really useful, enough that different people have developed
packages specifically to manipulate factors.

### Packages

R depends crucially on “packages” - that’s the whole reason that the
open-source works. Some statistician invents a cool new technique, then
writes up the code in R and makes it available. If you used a commercial
program you’d have to wait a decade for them to update it; in R it’s
here now. Also if somebody hacks a nicer or easier way to do stuff, they
write it up. Packages are extensions for specific tasks and you can tell
R to install specific ones. Many people who use R don’t need to create
detailed maps but if you want that, there’s a package. If you want to
analyze genetic sequences, there’s a package.

Hadley Wickham wrote ‘forcats’ for categorical data (ie factors). It’s
part of the ‘tidyverse’ package.

So enter this into the Console,

    install.packages("tidyverse")
    install.packages("plyr")

then

``` r
library(tidyverse)
library(plyr)
```

Alt, from R-Studio, click “Tools” then “Install Packages…” and tell it
to install the package, “tidyverse”. That is nice if you want to see
some of the packages or if you don’t quite remember the name. Then the
next piece of code, library, tells the program that you want to use
commands from this package. You only need to install once, then just put
library() into your code and run that part.

### Factors

R will do the summary differently when it knows the variable is a
factor,

``` r
summary(EST_ST)
```

    ##              Alabama               Alaska              Arizona 
    ##                  798                  717                 1600 
    ##             Arkansas           California             Colorado 
    ##                  823                 4066                 1499 
    ##          Connecticut             Delaware District of Columbia 
    ##                  903                  658                  790 
    ##              Florida              Georgia               Hawaii 
    ##                 1977                 1477                  619 
    ##                Idaho             Illinois              Indiana 
    ##                 1344                 1324                 1031 
    ##                 Iowa               Kansas             Kentucky 
    ##                  987                 1124                  898 
    ##            Louisiana                Maine             Maryland 
    ##                  772                  694                 1283 
    ##        Massachusetts             Michigan            Minnesota 
    ##                 1410                 1661                 1319 
    ##          Mississippi             Missouri              Montana 
    ##                  756                  989                  815 
    ##             Nebraska               Nevada        New Hampshire 
    ##                 1041                  887                  907 
    ##           New Jersey           New Mexico             New York 
    ##                 1051                 1186                 1038 
    ##       North Carolina         North Dakota                 Ohio 
    ##                 1003                  595                  957 
    ##             Oklahoma               Oregon         Pennsylvania 
    ##                  953                 1451                 1418 
    ##         Rhode Island       South Carolina         South Dakota 
    ##                  556                  848                  708 
    ##            Tennessee                Texas                 Utah 
    ##                 1032                 2841                 1529 
    ##              Vermont             Virginia           Washington 
    ##                  647                 1496                 2323 
    ##        West Virginia            Wisconsin              Wyoming 
    ##                  718                 1088                  683

``` r
summary(INCOME)
```

    ##                       NA HH income less than $25k  HH income $25k - $34.9k 
    ##                    10833                     4764                     3811 
    ##    HH income $35k - 49.9    HH income $50k - 74.9     HH income $75 - 99.9 
    ##                     5147                     7959                     6719 
    ##    HH income $100k - 149     HH income $150 - 199        HH income $200k + 
    ##                     9131                     4742                     6184

I know, we’d like if household income were a regular number not a
factor, but that’s what Census provides, as a way of helping keep
confidentiality.

To find mean and standard deviation of age by state, you could use
something like this,

``` r
ddply(Household_Pulse_data, .(EST_ST), summarize, mean = round(mean(2023 - TBIRTH_YEAR), 2), sd = round(sd(2023 - TBIRTH_YEAR), 2), n_obsv = length(EST_ST))
```

    ##                  EST_ST  mean    sd n_obsv
    ## 1               Alabama 50.89 15.58    798
    ## 2                Alaska 52.63 15.21    717
    ## 3               Arizona 54.24 16.74   1600
    ## 4              Arkansas 51.74 15.93    823
    ## 5            California 52.08 15.74   4066
    ## 6              Colorado 51.33 16.13   1499
    ## 7           Connecticut 51.96 15.53    903
    ## 8              Delaware 55.96 15.48    658
    ## 9  District of Columbia 49.59 15.47    790
    ## 10              Florida 54.78 16.25   1977
    ## 11              Georgia 51.82 15.57   1477
    ## 12               Hawaii 56.62 15.82    619
    ## 13                Idaho 51.84 15.94   1344
    ## 14             Illinois 50.86 15.73   1324
    ## 15              Indiana 50.45 16.13   1031
    ## 16                 Iowa 52.05 15.88    987
    ## 17               Kansas 51.84 15.93   1124
    ## 18             Kentucky 52.04 16.10    898
    ## 19            Louisiana 52.77 16.12    772
    ## 20                Maine 54.62 15.04    694
    ## 21             Maryland 52.68 15.36   1283
    ## 22        Massachusetts 50.59 15.83   1410
    ## 23             Michigan 52.24 15.72   1661
    ## 24            Minnesota 51.53 15.70   1319
    ## 25          Mississippi 52.81 16.17    756
    ## 26             Missouri 51.15 16.32    989
    ## 27              Montana 52.04 16.06    815
    ## 28             Nebraska 50.95 16.00   1041
    ## 29               Nevada 53.90 15.67    887
    ## 30        New Hampshire 53.80 15.31    907
    ## 31           New Jersey 52.27 15.18   1051
    ## 32           New Mexico 54.64 16.03   1186
    ## 33             New York 49.44 15.95   1038
    ## 34       North Carolina 52.62 16.56   1003
    ## 35         North Dakota 48.58 15.71    595
    ## 36                 Ohio 51.83 16.26    957
    ## 37             Oklahoma 51.48 16.42    953
    ## 38               Oregon 51.62 15.90   1451
    ## 39         Pennsylvania 51.06 15.88   1418
    ## 40         Rhode Island 53.88 15.46    556
    ## 41       South Carolina 52.21 16.17    848
    ## 42         South Dakota 52.04 15.49    708
    ## 43            Tennessee 51.17 16.36   1032
    ## 44                Texas 51.22 15.99   2841
    ## 45                 Utah 49.38 16.64   1529
    ## 46              Vermont 51.36 15.28    647
    ## 47             Virginia 51.51 15.66   1496
    ## 48           Washington 51.08 15.82   2323
    ## 49        West Virginia 52.13 15.13    718
    ## 50            Wisconsin 52.10 16.35   1088
    ## 51              Wyoming 52.49 15.56    683

Although tapply would also work fine.

Here’s the 90th and 10th percentiles of age by state, which reminds you
that there aren’t children in this sample,

``` r
ddply(Household_Pulse_data, .(EST_ST), summarize, age90th = quantile((2023 - TBIRTH_YEAR),probs = 0.9), age10th = quantile((2023 - TBIRTH_YEAR),probs = 0.1), n_obs = length(TBIRTH_YEAR))
```

    ##                  EST_ST age90th age10th n_obs
    ## 1               Alabama    71.0    30.0   798
    ## 2                Alaska    72.0    32.0   717
    ## 3               Arizona    75.0    31.0  1600
    ## 4              Arkansas    73.0    30.0   823
    ## 5            California    73.0    31.0  4066
    ## 6              Colorado    73.0    30.8  1499
    ## 7           Connecticut    73.0    31.0   903
    ## 8              Delaware    75.0    33.0   658
    ## 9  District of Columbia    71.0    30.0   790
    ## 10              Florida    76.0    32.0  1977
    ## 11              Georgia    72.0    31.0  1477
    ## 12               Hawaii    76.0    33.8   619
    ## 13                Idaho    73.0    30.0  1344
    ## 14             Illinois    71.0    30.0  1324
    ## 15              Indiana    72.0    29.0  1031
    ## 16                 Iowa    72.0    30.0   987
    ## 17               Kansas    72.0    30.0  1124
    ## 18             Kentucky    73.0    29.7   898
    ## 19            Louisiana    74.0    30.1   772
    ## 20                Maine    73.0    33.0   694
    ## 21             Maryland    73.0    32.0  1283
    ## 22        Massachusetts    72.0    30.0  1410
    ## 23             Michigan    73.0    31.0  1661
    ## 24            Minnesota    72.0    31.0  1319
    ## 25          Mississippi    73.0    30.0   756
    ## 26             Missouri    72.0    29.0   989
    ## 27              Montana    72.0    31.0   815
    ## 28             Nebraska    72.0    30.0  1041
    ## 29               Nevada    74.0    31.0   887
    ## 30        New Hampshire    73.0    33.0   907
    ## 31           New Jersey    72.0    32.0  1051
    ## 32           New Mexico    75.0    32.0  1186
    ## 33             New York    71.0    29.0  1038
    ## 34       North Carolina    74.0    30.0  1003
    ## 35         North Dakota    70.0    29.4   595
    ## 36                 Ohio    72.0    29.0   957
    ## 37             Oklahoma    73.0    29.0   953
    ## 38               Oregon    73.0    31.0  1451
    ## 39         Pennsylvania    73.0    31.0  1418
    ## 40         Rhode Island    72.5    32.5   556
    ## 41       South Carolina    73.0    29.0   848
    ## 42         South Dakota    73.0    31.7   708
    ## 43            Tennessee    73.0    29.0  1032
    ## 44                Texas    73.0    30.0  2841
    ## 45                 Utah    73.0    28.0  1529
    ## 46              Vermont    71.4    31.0   647
    ## 47             Virginia    73.0    31.0  1496
    ## 48           Washington    73.0    31.0  2323
    ## 49        West Virginia    72.0    31.7   718
    ## 50            Wisconsin    74.0    30.7  1088
    ## 51              Wyoming    72.0    31.0   683

You could also use table (or crosstabs) for factors with fewer items,

``` r
table(EEDUC,GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC            NA male female transgender other
    ##   less than hs   18  173    224           5    15
    ##   some hs        26  323    535           6    21
    ##   HS diploma    124 2979   4358          25    97
    ##   some coll     144 5110   7179          76   129
    ##   assoc deg      68 2263   3710          18    59
    ##   bach deg      164 7346   8927          69   162
    ##   adv deg       121 6526   8115          47   128

``` r
xtabs(~EEDUC + GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC            NA male female transgender other
    ##   less than hs   18  173    224           5    15
    ##   some hs        26  323    535           6    21
    ##   HS diploma    124 2979   4358          25    97
    ##   some coll     144 5110   7179          76   129
    ##   assoc deg      68 2263   3710          18    59
    ##   bach deg      164 7346   8927          69   162
    ##   adv deg       121 6526   8115          47   128

Want proportions instead of counts?

``` r
prop.table(table(EEDUC,GENID_DESCRIBE))
```

    ##               GENID_DESCRIBE
    ## EEDUC                    NA         male       female  transgender        other
    ##   less than hs 3.035925e-04 2.917861e-03 3.778040e-03 8.433125e-05 2.529938e-04
    ##   some hs      4.385225e-04 5.447799e-03 9.023444e-03 1.011975e-04 3.541913e-04
    ##   HS diploma   2.091415e-03 5.024456e-02 7.350312e-02 4.216563e-04 1.636026e-03
    ##   some coll    2.428740e-03 8.618654e-02 1.210828e-01 1.281835e-03 2.175746e-03
    ##   assoc deg    1.146905e-03 3.816833e-02 6.257379e-02 3.035925e-04 9.951088e-04
    ##   bach deg     2.766065e-03 1.238995e-01 1.505650e-01 1.163771e-03 2.732333e-03
    ##   adv deg      2.040816e-03 1.100692e-01 1.368696e-01 7.927138e-04 2.158880e-03

*Remember prop.table later when we do marginals.*

Try it and see what happens if you use table with EST_ST…

### Alt versions

In general, R is very flexible so there are often many different ways to
get the same answer. There are some people who love to debate which is
best. (Often, tradeoff between speed and intelligibility.) For now just
worry about learning at least one way. Later on you can go back and
refine your techniques.

Sometimes attaching a dataset makes things easier. But as you get more
advanced you might find it better to include the dataset name inside the
function. There are advantages and disadvantages each way and some of
the intro texts suggest one or the other.

If you do a lot of analysis on a particular subgroup, it might be
worthwhile to create a subset of that group, so that you don’t have to
always add on logical conditions. These two sets of expressions, looking
at people in the Northeast, get the same results:

``` r
mean(TBIRTH_YEAR[(REGION == "Northeast")])
```

    ## [1] 1971.19

``` r
# alternatively
restrict1 <- as.logical((REGION == "Northeast"))
dat_northeast <- subset(Household_Pulse_data, restrict1)

detach()
attach(dat_northeast)

mean(TBIRTH_YEAR)
```

    ## [1] 1971.19

``` r
detach()
```

So you detach the original data frame and instead attach the restricted
version. Then any subsequent analysis would be just done on that subset.
Just remember that you’ve done this (again, this is a good reason to
save the commands in a program so you can look back) otherwise you’ll
wonder why you suddenly don’t have southerners in the sample!

Obviously for a single restriction that might not be worthwhile but
later you might have more complicated propositions.

## Why All These Details?

You might be tired and bored by these details, but note that there are
actually important choices to be made here, even in simply defining
variables. Take the fraught American category of “race”. This data has a
variable, RRACE, showing how people chose to classify themselves, as
‘White,’ ‘Black,’ ‘Asian,’ or other.

In this case the Census has chosen particular values while alternate
responses go into the category of “Other”. In this case that was done
for confidentiality, just like the measures of income. Other government
data has more detail.

There’s no “right” way to do it because there’s no science in this
peculiar-but-popular concept of “race”. People’s conceptions of
themselves are fuzzy and complicated; these measures are approximations.

## Basics of government race/ethnicity classification

The US government asks questions about people’s race and ethnicity.
These categories are social constructs, which is a fancy way of pointing
out that they are based on people’s own views of themselves (influenced
by how we think that other people think of us…). Currently the standard
classification asks people separately about their “race” and “ethnicity”
where people can pick labels from each category in any combination.

The “race” categories include: “White,” “Black,” “Asian,” and others.
These represent a recent history of popular attitudes in the US. Only in
the 2000 Census did they start to classify people in mixed races. If you
were to go back to historical US Censuses from more than a century ago,
you would find that the category “race” included separate entries for
Irish and French. Stephen J Gould has a fascinating book, The Mismeasure
of Man, discussing how early scientific classifications of humans tried
to “prove” which nationalities/races/groups were the smartest. Ta-Nehisi
Coates notes, “racism invented race in America.” Throughout history,
statistics have been used to try to prove peoples’ prejudices.

Note that “Hispanic” is not “race” but rather ethnicity (includes
various other labels such as Spanish, Latino, etc.). So a respondent
could choose “Hispanic” and any race category – some choose “White,”
some choose “Black” or “Asian” or “Other”.

If you wanted to create a variable for those who report themselves as
Black and Hispanic, you’d use the expression (RRACE == “Black) &
(RHISPANIC ==”Hispanic”); sometimes government stats report for
non-Hispanic whites so (RRACE == “White”) & (RHISPANIC != “Hispanic”).
You can create your own classifications depending on what questions
you’re investigating.

The Census Bureau gives more information
[here](http://www.census.gov/newsroom/minority_links/minority_links.html).

All of these racial categories make some people uneasy: is the
government encouraging racism by recognizing these classifications? Some
other governments choose not to collect race data. But that doesn’t mean
that there are no differences, only that the government doesn’t choose
to measure any of these differences. In the US, government agencies such
as the Census and BLS don’t generally collect data on religion.

### Re-Coding complicated variables from initial data

If we want more combinations of variables then we create those. Usually
a statistical analysis spends a lot of time doing this sort of
housekeeping - dull but necessary. It has a variety of names: data
carpentry, data munging…

For instance this data provides detail about gender id and sexual
orientation but summary shows that “NA” is a common classification. They
force EGENID_BIRTH to be binary. Depending on what questions you’re
posing, you might want to combine together the 3 different responses NA,
transgender, and other. Or you might not!

That’s the whole point of learning to do the data work for yourself: you
can see all of the little decisions that go into creating a conclusion.
Some conclusions might be fragile so a tiny decision about coding could
change everything; other conclusions are robust to deviations. You must
find out.

# De-bugging

Without a doubt, programming is tough. In R or with any other program,
it is frustrating and complicated and difficult to do it the first few
times. Some days it feels like a continuous battle just to do the
simplest thing! Keep going despite that, keep working on it.

Your study group will be very helpful of course.

Often a google search of the error message helps. If you’ve isolated the
error and read the help documentation on that command, then you’re on
your way to solving the problem on your own.

If you have troubles that you can’t solve, email me for help. But try to
narrow down your question: if you run 20 lines of code that produce an
error, is there a way to reproduce the error in just 5 lines? What if
you did the same command on much simpler data, would it still cause an
error? Sending emails like “I have a problem with errors” might be
cathartic but is not actually useful to anyone. If you email me with the
minimal code that recreates the error, along with the text of the error
and/or a screenshot, then that will help more.

## Do it

The first homework assignment asks you to start working on these
questions. Begin by running the code that I give here, just to see if
you can replicate my results. Then start asking more questions about the
data - there is so much info there! Have some fun.
