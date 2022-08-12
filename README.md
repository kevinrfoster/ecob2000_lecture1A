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
suddenly a bunch of people who were born in 1933 but nobody in 1932 or
earlier. This is due to a coding choice by the Census, where really old
people are labeled as born in “1933” (top-coding) but it actually should
be interpreted as meaning “in 1933 or before”. So if you were to get
finicky (and every good statistician is!) you might go back to the
calculations of averages previously and modify them all. For instance,
to select just those who are female and who are coded as having age less
than 90. Many variables are topcoded! *And recall that topcoding
wouldn’t change the median values calculated before, which is a point in
favor of that statistic.*

``` r
mean(TBIRTH_YEAR[ (GENID_DESCRIBE == "female") & (TBIRTH_YEAR > 1933) ]) 
```

    ## [1] 1968.792

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
    ##          411          936         7857        14596         7508        20075 
    ##      adv deg 
    ##        17731

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
    ##                  918                 1232                 1744 
    ##             Arkansas           California             Colorado 
    ##                  917                 5359                 1667 
    ##          Connecticut             Delaware District of Columbia 
    ##                 1229                  892                  761 
    ##              Florida              Georgia               Hawaii 
    ##                 2728                 1655                  812 
    ##                Idaho             Illinois              Indiana 
    ##                 1245                 1387                 1247 
    ##                 Iowa               Kansas             Kentucky 
    ##                  995                 1315                  938 
    ##            Louisiana                Maine             Maryland 
    ##                  895                  606                 1605 
    ##        Massachusetts             Michigan            Minnesota 
    ##                 1965                 1753                 1488 
    ##          Mississippi             Missouri              Montana 
    ##                  658                 1229                  695 
    ##             Nebraska               Nevada        New Hampshire 
    ##                  997                 1200                  970 
    ##           New Jersey           New Mexico             New York 
    ##                 1391                 1373                 1292 
    ##       North Carolina         North Dakota                 Ohio 
    ##                 1310                  449                 1030 
    ##             Oklahoma               Oregon         Pennsylvania 
    ##                  969                 1934                 1727 
    ##         Rhode Island       South Carolina         South Dakota 
    ##                  634                 1042                  628 
    ##            Tennessee                Texas                 Utah 
    ##                 1206                 3766                 1862 
    ##              Vermont             Virginia           Washington 
    ##                  664                 1740                 2634 
    ##        West Virginia            Wisconsin              Wyoming 
    ##                  680                 1133                  548

``` r
summary(INCOME)
```

    ##                       NA HH income less than $25k  HH income $25k - $34.9k 
    ##                    14637                     5698                     4600 
    ##    HH income $35k - 49.9    HH income $50k - 74.9     HH income $75 - 99.9 
    ##                     5805                     9330                     7830 
    ##    HH income $100k - 149     HH income $150 - 199        HH income $200k + 
    ##                    10117                     4980                     6117

I know, we’d like if household income were a regular number not a
factor, but that’s what Census provides, as a way of helping keep
confidentiality.

To find mean and standard deviation of age by state, you could use
something like this,

``` r
ddply(Household_Pulse_data, .(EST_ST), summarize, mean = round(mean(2021 - TBIRTH_YEAR), 2), sd = round(sd(2021 - TBIRTH_YEAR), 2), n_obsv = length(EST_ST))
```

    ##                  EST_ST  mean    sd n_obsv
    ## 1               Alabama 52.93 16.24    918
    ## 2                Alaska 51.21 15.92   1232
    ## 3               Arizona 54.70 16.09   1744
    ## 4              Arkansas 52.31 15.76    917
    ## 5            California 52.94 15.92   5359
    ## 6              Colorado 52.31 15.93   1667
    ## 7           Connecticut 53.95 15.87   1229
    ## 8              Delaware 55.45 15.64    892
    ## 9  District of Columbia 53.85 15.24    761
    ## 10              Florida 56.24 15.93   2728
    ## 11              Georgia 53.19 15.32   1655
    ## 12               Hawaii 54.28 16.45    812
    ## 13                Idaho 52.78 16.22   1245
    ## 14             Illinois 52.11 16.26   1387
    ## 15              Indiana 52.08 15.58   1247
    ## 16                 Iowa 53.83 15.63    995
    ## 17               Kansas 51.33 16.27   1315
    ## 18             Kentucky 53.57 15.51    938
    ## 19            Louisiana 51.96 15.24    895
    ## 20                Maine 54.61 15.26    606
    ## 21             Maryland 53.18 15.72   1605
    ## 22        Massachusetts 54.11 15.73   1965
    ## 23             Michigan 54.72 15.70   1753
    ## 24            Minnesota 51.35 16.04   1488
    ## 25          Mississippi 52.76 15.54    658
    ## 26             Missouri 53.84 16.11   1229
    ## 27              Montana 53.70 15.58    695
    ## 28             Nebraska 52.14 16.12    997
    ## 29               Nevada 53.86 15.86   1200
    ## 30        New Hampshire 56.08 15.38    970
    ## 31           New Jersey 54.14 15.14   1391
    ## 32           New Mexico 55.23 15.63   1373
    ## 33             New York 51.73 16.23   1292
    ## 34       North Carolina 53.27 15.74   1310
    ## 35         North Dakota 52.04 15.64    449
    ## 36                 Ohio 53.65 15.56   1030
    ## 37             Oklahoma 53.31 15.54    969
    ## 38               Oregon 51.47 16.14   1934
    ## 39         Pennsylvania 52.43 16.26   1727
    ## 40         Rhode Island 54.18 15.36    634
    ## 41       South Carolina 55.14 16.05   1042
    ## 42         South Dakota 52.69 15.84    628
    ## 43            Tennessee 51.85 16.51   1206
    ## 44                Texas 52.15 16.05   3766
    ## 45                 Utah 47.97 16.20   1862
    ## 46              Vermont 56.61 15.13    664
    ## 47             Virginia 53.37 15.22   1740
    ## 48           Washington 53.06 15.75   2634
    ## 49        West Virginia 54.32 15.48    680
    ## 50            Wisconsin 52.40 15.95   1133
    ## 51              Wyoming 54.62 15.03    548

Although tapply would also work fine.

Here’s the 90th and 10th percentiles of age by state,

``` r
ddply(Household_Pulse_data, .(EST_ST), summarize, age90th = quantile((2021 - TBIRTH_YEAR),probs = 0.9), age10th = quantile((2021 - TBIRTH_YEAR),probs = 0.1), n_obs = length(TBIRTH_YEAR))
```

    ##                  EST_ST age90th age10th n_obs
    ## 1               Alabama    74.0    30.0   918
    ## 2                Alaska    71.0    30.0  1232
    ## 3               Arizona    75.0    32.0  1744
    ## 4              Arkansas    73.0    31.0   917
    ## 5            California    74.0    31.0  5359
    ## 6              Colorado    73.0    31.0  1667
    ## 7           Connecticut    74.0    32.0  1229
    ## 8              Delaware    74.9    33.0   892
    ## 9  District of Columbia    74.0    34.0   761
    ## 10              Florida    76.0    34.0  2728
    ## 11              Georgia    74.0    32.0  1655
    ## 12               Hawaii    74.0    31.0   812
    ## 13                Idaho    75.0    31.0  1245
    ## 14             Illinois    73.0    30.0  1387
    ## 15              Indiana    72.0    31.0  1247
    ## 16                 Iowa    73.0    33.0   995
    ## 17               Kansas    72.0    29.0  1315
    ## 18             Kentucky    72.3    32.0   938
    ## 19            Louisiana    72.0    31.0   895
    ## 20                Maine    73.0    32.5   606
    ## 21             Maryland    74.0    32.4  1605
    ## 22        Massachusetts    74.0    32.0  1965
    ## 23             Michigan    74.0    33.0  1753
    ## 24            Minnesota    72.0    30.0  1488
    ## 25          Mississippi    73.0    32.0   658
    ## 26             Missouri    75.0    32.0  1229
    ## 27              Montana    74.0    32.0   695
    ## 28             Nebraska    74.0    31.0   997
    ## 29               Nevada    74.0    32.0  1200
    ## 30        New Hampshire    75.0    33.9   970
    ## 31           New Jersey    73.0    34.0  1391
    ## 32           New Mexico    74.0    33.0  1373
    ## 33             New York    73.0    30.0  1292
    ## 34       North Carolina    73.0    32.0  1310
    ## 35         North Dakota    71.0    32.0   449
    ## 36                 Ohio    73.0    32.0  1030
    ## 37             Oklahoma    73.0    33.0   969
    ## 38               Oregon    73.0    30.0  1934
    ## 39         Pennsylvania    73.0    31.0  1727
    ## 40         Rhode Island    73.0    33.0   634
    ## 41       South Carolina    75.0    32.0  1042
    ## 42         South Dakota    72.0    32.0   628
    ## 43            Tennessee    73.0    29.0  1206
    ## 44                Texas    73.0    30.0  3766
    ## 45                 Utah    71.0    28.0  1862
    ## 46              Vermont    74.7    36.0   664
    ## 47             Virginia    73.0    33.0  1740
    ## 48           Washington    74.0    32.0  2634
    ## 49        West Virginia    72.1    32.0   680
    ## 50            Wisconsin    73.0    31.0  1133
    ## 51              Wyoming    73.0    35.0   548

You could also use table (or crosstabs) for factors with fewer items,

``` r
table(EEDUC,GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC             NA  male female transgender other
    ##   less than hs    14   158    204          11    24
    ##   some hs         25   379    505           7    20
    ##   HS diploma     154  2844   4734          28    97
    ##   some coll      207  5569   8607          53   160
    ##   assoc deg      124  2447   4835          15    87
    ##   bach deg       319  8143  11401          46   166
    ##   adv deg        288  7256   9977          42   168

``` r
xtabs(~EEDUC + GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC             NA  male female transgender other
    ##   less than hs    14   158    204          11    24
    ##   some hs         25   379    505           7    20
    ##   HS diploma     154  2844   4734          28    97
    ##   some coll      207  5569   8607          53   160
    ##   assoc deg      124  2447   4835          15    87
    ##   bach deg       319  8143  11401          46   166
    ##   adv deg        288  7256   9977          42   168

Want proportions instead of counts?

``` r
prop.table(table(EEDUC,GENID_DESCRIBE))
```

    ##               GENID_DESCRIBE
    ## EEDUC                    NA         male       female  transgender        other
    ##   less than hs 0.0002025639 0.0022860781 0.0029516451 0.0001591573 0.0003472524
    ##   some hs      0.0003617212 0.0054836936 0.0073067685 0.0001012819 0.0002893770
    ##   HS diploma   0.0022282027 0.0411494053 0.0684955291 0.0004051278 0.0014034783
    ##   some coll    0.0029950517 0.0805770177 0.1245333796 0.0007668490 0.0023150158
    ##   assoc deg    0.0017941372 0.0354052724 0.0699568828 0.0002170327 0.0012587898
    ##   bach deg     0.0046155627 0.1178198339 0.1649593425 0.0006655670 0.0024018289
    ##   adv deg      0.0041670284 0.1049859652 0.1443557022 0.0006076916 0.0024307666

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

    ## [1] 1967.101

``` r
# alternatively
restrict1 <- as.logical((REGION == "Northeast"))
dat_northeast <- subset(Household_Pulse_data, restrict1)

detach()
attach(dat_northeast)

mean(TBIRTH_YEAR)
```

    ## [1] 1967.101

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
