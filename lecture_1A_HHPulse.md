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
suddenly a bunch of people who were born in 1936 but many fewer in 1937
or 1938. This is due to a coding choice by the Census, where really old
people are labeled as born in “1936” (top-coding) but it actually should
be interpreted as meaning “in 1936 or before”. So if you were to get
finicky (and every good statistician is!) you might go back to the
calculations of averages previously and modify them all. For instance,
to select just those who are female and who are coded as having birth
year after 1936. Many variables are topcoded! *And recall that topcoding
wouldn’t change the median values calculated before, which is a point in
favor of that statistic.*

``` r
mean(TBIRTH_YEAR[ (GENID_DESCRIBE == "female") & (TBIRTH_YEAR > 1936) ]) 
```

    ## [1] 1970.493

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
    ##          585         1282        10094        15415         7614        19137 
    ##      adv deg 
    ##        17025

R helpfully provides labels for each of those all together. Later when
we get into more depth, we might have to dig into the y/n answers that
are underneath.

It’s important to remember that there are some cases where the factor
values are well ordered (as with highest educational qualification)
versus others such as EST_ST (the state the person lives in) where there
is not necessarily an ordering. (It’s alphabetical, nothing else.)

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

then

``` r
library(tidyverse)
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
    ##                 1074                  825                 2018 
    ##             Arkansas           California             Colorado 
    ##                  932                 5215                 1860 
    ##          Connecticut             Delaware District of Columbia 
    ##                 1237                  693                  757 
    ##              Florida              Georgia               Hawaii 
    ##                 2613                 1957                  764 
    ##                Idaho             Illinois              Indiana 
    ##                 1099                 1625                 1387 
    ##                 Iowa               Kansas             Kentucky 
    ##                 1204                 1166                 1107 
    ##            Louisiana                Maine             Maryland 
    ##                 1021                  759                 1634 
    ##        Massachusetts             Michigan            Minnesota 
    ##                 1892                 2033                 1461 
    ##          Mississippi             Missouri              Montana 
    ##                  781                 1350                  680 
    ##             Nebraska               Nevada        New Hampshire 
    ##                  896                 1001                  981 
    ##           New Jersey           New Mexico             New York 
    ##                 1287                 1340                 1381 
    ##       North Carolina         North Dakota                 Ohio 
    ##                 1410                  474                 1287 
    ##             Oklahoma               Oregon         Pennsylvania 
    ##                 1195                 1880                 1906 
    ##         Rhode Island       South Carolina         South Dakota 
    ##                  691                 1170                  752 
    ##            Tennessee                Texas                 Utah 
    ##                 1385                 3456                 1551 
    ##              Vermont             Virginia           Washington 
    ##                  597                 1896                 2892 
    ##        West Virginia            Wisconsin              Wyoming 
    ##                  599                 1436                  545

``` r
summary(INCOME)
```

    ##                       NA HH income less than $25k  HH income $25k - $34.9k 
    ##                    12256                     6782                     5156 
    ##    HH income $35k - 49.9    HH income $50k - 74.9     HH income $75 - 99.9 
    ##                     6192                     9461                     7844 
    ##    HH income $100k - 149     HH income $150 - 199        HH income $200k + 
    ##                    10444                     5537                     7480

I know, we’d like if household income were a regular number not a
factor, but that’s what Census provides, as a way of helping keep
confidentiality.

To find mean and standard deviation of age by state, you could use
something like this,

``` r
Household_Pulse_data %>%
  group_by(EST_ST) %>%
  summarize(
    avg = mean(2024 - TBIRTH_YEAR),
    stdev = sd(2024 - TBIRTH_YEAR), 
    n_obs = n()
  ) 
```

    ## # A tibble: 51 × 4
    ##    EST_ST                 avg stdev n_obs
    ##    <fct>                <dbl> <dbl> <int>
    ##  1 Alabama               55.1  16.1  1074
    ##  2 Alaska                53.7  15.6   825
    ##  3 Arizona               56.6  16.0  2018
    ##  4 Arkansas              54.2  16.2   932
    ##  5 California            53.9  15.8  5215
    ##  6 Colorado              53.2  16.1  1860
    ##  7 Connecticut           54.4  15.9  1237
    ##  8 Delaware              57.2  15.8   693
    ##  9 District of Columbia  45.3  14.3   757
    ## 10 Florida               57.2  16.1  2613
    ## # ℹ 41 more rows

Although tapply would also work fine.

Here’s the 90th and 10th percentiles of age by state, which reminds you
that there aren’t children in this sample. Then sorts by 90th
percentile.

``` r
Household_Pulse_data %>%
  group_by(EST_ST) %>%
  summarize(
    age90th = quantile((2024 - TBIRTH_YEAR),probs = 0.9),
    age10th = quantile((2024 - TBIRTH_YEAR),probs = 0.1), 
    n_obs = n()
  ) %>%
  arrange(desc(age90th), .by_group = TRUE)
```

    ## # A tibble: 51 × 4
    ##    EST_ST        age90th age10th n_obs
    ##    <fct>           <dbl>   <dbl> <int>
    ##  1 Arizona          77      34    2018
    ##  2 Florida          77      34    2613
    ##  3 Hawaii           77      37.3   764
    ##  4 New Mexico       77      35    1340
    ##  5 Delaware         76      35     693
    ##  6 Idaho            76      34    1099
    ##  7 Nevada           76      34    1001
    ##  8 New Hampshire    76      35     981
    ##  9 West Virginia    76      34     599
    ## 10 Arkansas         75.9    32     932
    ## # ℹ 41 more rows

You could also use table (or crosstabs) for factors with fewer items,

``` r
table(EEDUC,GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC            NA male female transgender other
    ##   less than hs  342   98    134           3     8
    ##   some hs       713  233    319           3    14
    ##   HS diploma   5279 1929   2818          15    53
    ##   some coll    7877 3200   4204          41    93
    ##   assoc deg    3987 1373   2200           8    46
    ##   bach deg     9842 4432   4721          49    93
    ##   adv deg      8662 3824   4434          28    77

``` r
xtabs(~EEDUC + GENID_DESCRIBE)
```

    ##               GENID_DESCRIBE
    ## EEDUC            NA male female transgender other
    ##   less than hs  342   98    134           3     8
    ##   some hs       713  233    319           3    14
    ##   HS diploma   5279 1929   2818          15    53
    ##   some coll    7877 3200   4204          41    93
    ##   assoc deg    3987 1373   2200           8    46
    ##   bach deg     9842 4432   4721          49    93
    ##   adv deg      8662 3824   4434          28    77

Want proportions instead of counts?

``` r
prop.table(table(EEDUC,GENID_DESCRIBE))
```

    ##               GENID_DESCRIBE
    ## EEDUC                    NA         male       female  transgender        other
    ##   less than hs 4.806611e-03 1.377333e-03 1.883292e-03 4.216326e-05 1.124353e-04
    ##   some hs      1.002080e-02 3.274680e-03 4.483360e-03 4.216326e-05 1.967619e-04
    ##   HS diploma   7.419328e-02 2.711097e-02 3.960535e-02 2.108163e-04 7.448842e-04
    ##   some coll    1.107067e-01 4.497414e-02 5.908478e-02 5.762312e-04 1.307061e-03
    ##   assoc deg    5.603497e-02 1.929672e-02 3.091972e-02 1.124353e-04 6.465033e-04
    ##   bach deg     1.383236e-01 6.228918e-02 6.635091e-02 6.886665e-04 1.307061e-03
    ##   adv deg      1.217394e-01 5.374410e-02 6.231729e-02 3.935237e-04 1.082190e-03

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

    ## [1] 1970.11

``` r
# alternatively
restrict1 <- as.logical((REGION == "Northeast"))
dat_northeast <- subset(Household_Pulse_data, restrict1)

detach()
attach(dat_northeast)

mean(TBIRTH_YEAR)
```

    ## [1] 1970.11

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
posing, you might wonder if it’s useful to combine together the 3
different responses NA, transgender, and other. Or you might not!

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

If you have troubles that you can’t solve, ping me for help. But try to
narrow down your question: if you run 20 lines of code that produce an
error, is there a way to reproduce the error in just 5 lines? What if
you did the same command on much simpler data, would it still cause an
error? Sending messages like “I have a problem with errors” might be
cathartic but is not actually useful to solving the problem. If you
slack me with the minimal code that recreates the error, along with the
text of the error and/or a screenshot, then that will help more.

## Do it

The first homework assignment asks you to start working on these
questions. Begin by running the code that I give here, just to see if
you can replicate my results. Then start asking more questions about the
data - there is so much info there! Have some fun.
