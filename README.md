R Basics for Lecture 1 A
================

## Econ B2000, Statistics and Introduction to Econometrics

## Kevin R Foster, Colin Powell School, the City College of New York, CUNY

## Variable Coding

Some of the PUMS variables here have a natural interpretation, for
instance Age is measured in years. Actually even this has a bit of a
twist, look at the histogram.

``` r
hist(AGE[(AGE > 90)])
```

![](lecture_1A_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

There is a bit of weirdness in the right, where it looks like there are
suddenly a bunch of people who are 95 but nobody is 94 or 96. This is
due to a coding choice by the Census, where really old people are just
labeled as “95” (top-coding) so it actually should be interpreted as
meaning “92 or older”. So if you were to get finicky (and every good
statistician is\!) you might go back to the calculations of averages
previously and modify them all like this, to select just those who are
female and who are coded as having age less than 90. Many variables are
topcoded\! *And recall that topcoding wouldn’t change the median values
calculated before, which is a point in favor of that statistic.*

``` r
mean(AGE[ (female == 1) & (AGE<90) ]) 
```

    ## [1] 41.98866

You go make those other changes, figure out how top-coding changes the
calculations of average age by gender – I’ll wait right here…

## Variable Coding Again

So we were saying that some variables, like Age - ahem\! – have a
natural interpretation as a number.

Others are logical variables (called dummies) like female, Hispanic, or
married - there is a yes/no answer that is coded 1/0. *Note that if
you’re creating these on your own it’s good to give names that have
that sort of yes/no answer, so a variable named ‘female’ is better than
one named ‘gender’ where you’d have to remember who are coded as true
and who are false.*

Many variables, like PUMA, have no natural explanation at all. Here are
the first codes,

``` r
str(as.numeric(PUMA))
```

    ##  num [1:196585] 902 902 4002 4002 3803 ...

You have to go to the codebook (or, in this case, the file
PUMA\_levels.csv or acs2017\_codebook.txt from the zip file) to find out
that 3801 codes for Washington Heights/Inwood, 3802 is Hamilton
Heights/Manhattanville/West Harlem, etc. The program will happily
calculate the average value for PUMA (type in *mean(PUMA)* and see for
yourself\!) but this is a meaningless value – wtf is the average
neighborhood code value\!? If you want to select just people living in a
particular neighborhood then you’d have to look at the list below.

| PUMA | Neighborhood                                                        |
| ---- | ------------------------------------------------------------------- |
| 3701 | NYC-Bronx CD 8–Riverdale, Fieldston & Kingsbridge                   |
| 3702 | NYC-Bronx CD 12–Wakefield, Williamsbridge & Woodlawn                |
| 3703 | NYC-Bronx CD 10–Co-op City, Pelham Bay & Schuylerville              |
| 3704 | NYC-Bronx CD 11–Pelham Parkway, Morris Park & Laconia               |
| 3705 | NYC-Bronx CD 3 & 6–Belmont, Crotona Park East & East Tremont        |
| 3706 | NYC-Bronx CD 7–Bedford Park, Fordham North & Norwood                |
| 3707 | NYC-Bronx CD 5–Morris Heights, Fordham South & Mount Hope           |
| 3708 | NYC-Bronx CD 4–Concourse, Highbridge & Mount Eden                   |
| 3709 | NYC-Bronx CD 9–Castle Hill, Clason Point & Parkchester              |
| 3710 | NYC-Bronx CD 1 & 2–Hunts Point, Longwood & Melrose                  |
| 3801 | NYC-Manhattan CD 12–Washington Heights, Inwood & Marble Hill        |
| 3802 | NYC-Manhattan CD 9–Hamilton Heights, Manhattanville & West Harlem   |
| 3803 | NYC-Manhattan CD 10–Central Harlem                                  |
| 3804 | NYC-Manhattan CD 11–East Harlem                                     |
| 3805 | NYC-Manhattan CD 8–Upper East Side                                  |
| 3806 | NYC-Manhattan CD 7–Upper West Side & West Side                      |
| 3807 | NYC-Manhattan CD 4 & 5–Chelsea, Clinton & Midtown Business District |
| 3808 | NYC-Manhattan CD 6–Murray Hill, Gramercy & Stuyvesant Town          |
| 3809 | NYC-Manhattan CD 3–Chinatown & Lower East Side                      |
| 3810 | NYC-Manhattan CD 1 & 2–Battery Park City, Greenwich Village & Soho  |
| 3901 | NYC-Staten Island CD 3–Tottenville, Great Kills & Annadale          |
| 3902 | NYC-Staten Island CD 2–New Springville & South Beach                |
| 3903 | NYC-Staten Island CD 1–Port Richmond, Stapleton & Mariner’s Harbor  |
| 4001 | NYC-Brooklyn CD 1–Greenpoint & Williamsburg                         |
| 4002 | NYC-Brooklyn CD 4—Bushwick                                          |
| 4003 | NYC-Brooklyn CD 3–Bedford-Stuyvesant                                |
| 4004 | NYC-Brooklyn CD 2–Brooklyn Heights & Fort Greene                    |
| 4005 | NYC-Brooklyn CD 6–Park Slope, Carroll Gardens & Red Hook            |
| 4006 | NYC-Brooklyn CD 8–Crown Heights North & Prospect Heights            |
| 4007 | NYC-Brooklyn CD 16–Brownsville & Ocean Hill                         |
| 4008 | NYC-Brooklyn CD 5–East New York & Starrett City                     |
| 4009 | NYC-Brooklyn CD 18–Canarsie & Flatlands                             |
| 4010 | NYC-Brooklyn CD 17–East Flatbush, Farragut & Rugby                  |
| 4011 | NYC-Brooklyn CD 9–Crown Heights South, Prospect Lefferts & Wingate  |
| 4012 | NYC-Brooklyn CD 7–Sunset Park & Windsor Terrace                     |
| 4013 | NYC-Brooklyn CD 10–Bay Ridge & Dyker Heights                        |
| 4014 | NYC-Brooklyn CD 12–Borough Park, Kensington & Ocean Parkway         |
| 4015 | NYC-Brooklyn CD 14–Flatbush & Midwood                               |
| 4016 | NYC-Brooklyn CD 15–Sheepshead Bay, Gerritsen Beach & Homecrest      |
| 4017 | NYC-Brooklyn CD 11–Bensonhurst & Bath Beach                         |
| 4018 | NYC-Brooklyn CD 13–Brighton Beach & Coney Island                    |
| 4101 | NYC-Queens CD 1–Astoria & Long Island City                          |
| 4102 | NYC-Queens CD 3–Jackson Heights & North Corona                      |
| 4103 | NYC-Queens CD 7–Flushing, Murray Hill & Whitestone                  |
| 4104 | NYC-Queens CD 11–Bayside, Douglaston & Little Neck                  |
| 4105 | NYC-Queens CD 13–Queens Village, Cambria Heights & Rosedale         |
| 4106 | NYC-Queens CD 8–Briarwood, Fresh Meadows & Hillcrest                |
| 4107 | NYC-Queens CD 4–Elmhurst & South Corona                             |
| 4108 | NYC-Queens CD 6–Forest Hills & Rego Park                            |
| 4109 | NYC-Queens CD 2–Sunnyside & Woodside                                |
| 4110 | NYC-Queens CD 5–Ridgewood, Glendale & Middle Village                |
| 4111 | NYC-Queens CD 9–Richmond Hill & Woodhaven                           |
| 4112 | NYC-Queens CD 12–Jamaica, Hollis & St. Albans                       |
| 4113 | NYC-Queens CD 10–Howard Beach & Ozone Park                          |
| 4114 | NYC-Queens CD 14–Far Rockaway, Breezy Point & Broad Channel         |

Now you’re probably thinking, isn’t there some easier way? Yes there is.
R has variables called “factors” that join together the long list of
codes with a separate file telling what those codes mean. Later when we
do further statistics, R will know how to appropriately treat these
factors. (Also it will then give an error if you calculate mean(PUMA),
which is proper.)

``` r
PUMA <- as.factor(PUMA)
female <- as.factor(female)
```

I will leave you to worry over the recoding of the other variables,
because it’s good for the soul. I will show you 2 ways – the quick and
dirty way, and the fancy correct way.

First the quick and dirty way.

``` r
print(levels(female))
```

    ## [1] "0" "1"

``` r
levels(female) <- c("male","female")
```

Well, ways,

``` r
educ_indx <- factor((educ_nohs + 2*educ_hs + 3*educ_somecoll + 4*educ_college + 5*educ_advdeg), levels=c(1,2,3,4,5),labels = c("No HS","HS","SmColl","Bach","Adv"))
```

(If you can figure out how that bit of code works, that would be good)

These just type in the levels. But for things like PUMA, it could be a
long list and might not even match every one. To do it better, we need
help from an R package.

\#\#\#Detour on Packages But first a bit of a detour, to mention how to
use packages. R depends crucially on “packages” - that’s the whole
reason that the open-source works. Some statistician invents a cool new
technique, then writes up the code in R and makes it available. If you
used a commercial program you’d have to wait a decade for them to update
it; in R it’s here now. Also if somebody hacks a nicer or easier way to
do stuff, they write it up.

So enter this into the Console,

    install.packages("tidyverse")
    install.packages("plyr")

then

``` r
library(tidyverse)
```

    ## Warning: package 'ggplot2' was built under R version 3.6.3

    ## Warning: package 'tibble' was built under R version 3.6.3

    ## Warning: package 'tidyr' was built under R version 3.6.3

    ## Warning: package 'purrr' was built under R version 3.6.3

    ## Warning: package 'dplyr' was built under R version 3.6.3

    ## Warning: package 'forcats' was built under R version 3.6.3

``` r
library(plyr)
```

    ## Warning: package 'plyr' was built under R version 3.6.3

``` r
levels_n <- read.csv("PUMA_levels.csv")
levels_orig <- levels(PUMA) 
levels_new <- join(data.frame(levels_orig),data.frame(levels_n))
levels(PUMA) <- levels_new$New_Level
```

Alt, from R-Studio, click “Tools” then “Install Packages…” and tell it
to install the packages, “plyr” and “tidyverse”. That is nice if you
want to see some of the packages or if you don’t quite remember the
name. Then the next piece of code, library, tells the program that you
want to use commands from this package.

Those commands read in a little csv file that I had made, with the PUMA
codes, then matches the old codes with the new complete text. Note that
I’m lazy so codes in NY state outside of NYC are coded NA.

\#\#\#Back from Detour R will do the summary differently when it knows
the variable is a factor,

``` r
summary(female)
```

    ##   male female 
    ##  95222 101363

``` r
summary(PUMA)
```

    ##                   NYC-Bronx CD 8--Riverdale, Fieldston & Kingsbridge 
    ##                                                                  936 
    ##                NYC-Bronx CD 12--Wakefield, Williamsbridge & Woodlawn 
    ##                                                                 1092 
    ##              NYC-Bronx CD 10--Co-op City, Pelham Bay & Schuylerville 
    ##                                                                  767 
    ##               NYC-Bronx CD 11--Pelham Parkway, Morris Park & Laconia 
    ##                                                                 1115 
    ##        NYC-Bronx CD 3 & 6--Belmont, Crotona Park East & East Tremont 
    ##                                                                 1311 
    ##                NYC-Bronx CD 7--Bedford Park, Fordham North & Norwood 
    ##                                                                  854 
    ##           NYC-Bronx CD 5--Morris Heights, Fordham South & Mount Hope 
    ##                                                                 1112 
    ##                   NYC-Bronx CD 4--Concourse, Highbridge & Mount Eden 
    ##                                                                  917 
    ##              NYC-Bronx CD 9--Castle Hill, Clason Point & Parkchester 
    ##                                                                 1307 
    ##                  NYC-Bronx CD 1 & 2--Hunts Point, Longwood & Melrose 
    ##                                                                 1166 
    ##        NYC-Manhattan CD 12--Washington Heights, Inwood & Marble Hill 
    ##                                                                 1238 
    ##   NYC-Manhattan CD 9--Hamilton Heights, Manhattanville & West Harlem 
    ##                                                                  872 
    ##                                  NYC-Manhattan CD 10--Central Harlem 
    ##                                                                  897 
    ##                                     NYC-Manhattan CD 11--East Harlem 
    ##                                                                  769 
    ##                                  NYC-Manhattan CD 8--Upper East Side 
    ##                                                                 1167 
    ##                      NYC-Manhattan CD 7--Upper West Side & West Side 
    ##                                                                  949 
    ## NYC-Manhattan CD 4 & 5--Chelsea, Clinton & Midtown Business District 
    ##                                                                  944 
    ##          NYC-Manhattan CD 6--Murray Hill, Gramercy & Stuyvesant Town 
    ##                                                                  758 
    ##                      NYC-Manhattan CD 3--Chinatown & Lower East Side 
    ##                                                                 1062 
    ##  NYC-Manhattan CD 1 & 2--Battery Park City, Greenwich Village & Soho 
    ##                                                                 1136 
    ##          NYC-Staten Island CD 3--Tottenville, Great Kills & Annadale 
    ##                                                                 1303 
    ##                NYC-Staten Island CD 2--New Springville & South Beach 
    ##                                                                 1173 
    ##  NYC-Staten Island CD 1--Port Richmond, Stapleton & Mariner's Harbor 
    ##                                                                 1621 
    ##                         NYC-Brooklyn CD 1--Greenpoint & Williamsburg 
    ##                                                                 1293 
    ##                                          NYC-Brooklyn CD 4--Bushwick 
    ##                                                                 1060 
    ##                                NYC-Brooklyn CD 3--Bedford-Stuyvesant 
    ##                                                                 1082 
    ##                    NYC-Brooklyn CD 2--Brooklyn Heights & Fort Greene 
    ##                                                                 1320 
    ##            NYC-Brooklyn CD 6--Park Slope, Carroll Gardens & Red Hook 
    ##                                                                 1168 
    ##            NYC-Brooklyn CD 8--Crown Heights North & Prospect Heights 
    ##                                                                 1077 
    ##                         NYC-Brooklyn CD 16--Brownsville & Ocean Hill 
    ##                                                                  904 
    ##                     NYC-Brooklyn CD 5--East New York & Starrett City 
    ##                                                                 1321 
    ##                             NYC-Brooklyn CD 18--Canarsie & Flatlands 
    ##                                                                 2422 
    ##                  NYC-Brooklyn CD 17--East Flatbush, Farragut & Rugby 
    ##                                                                 1250 
    ##  NYC-Brooklyn CD 9--Crown Heights South, Prospect Lefferts & Wingate 
    ##                                                                  818 
    ##                     NYC-Brooklyn CD 7--Sunset Park & Windsor Terrace 
    ##                                                                 1291 
    ##                        NYC-Brooklyn CD 10--Bay Ridge & Dyker Heights 
    ##                                                                 1519 
    ##         NYC-Brooklyn CD 12--Borough Park, Kensington & Ocean Parkway 
    ##                                                                 1698 
    ##                               NYC-Brooklyn CD 14--Flatbush & Midwood 
    ##                                                                 1479 
    ##      NYC-Brooklyn CD 15--Sheepshead Bay, Gerritsen Beach & Homecrest 
    ##                                                                 1903 
    ##                         NYC-Brooklyn CD 11--Bensonhurst & Bath Beach 
    ##                                                                 2234 
    ##                    NYC-Brooklyn CD 13--Brighton Beach & Coney Island 
    ##                                                                  925 
    ##                          NYC-Queens CD 1--Astoria & Long Island City 
    ##                                                                 1748 
    ##                      NYC-Queens CD 3--Jackson Heights & North Corona 
    ##                                                                 1316 
    ##                  NYC-Queens CD 7--Flushing, Murray Hill & Whitestone 
    ##                                                                 2290 
    ##                  NYC-Queens CD 11--Bayside, Douglaston & Little Neck 
    ##                                                                 1344 
    ##         NYC-Queens CD 13--Queens Village, Cambria Heights & Rosedale 
    ##                                                                 2148 
    ##                NYC-Queens CD 8--Briarwood, Fresh Meadows & Hillcrest 
    ##                                                                 1393 
    ##                             NYC-Queens CD 4--Elmhurst & South Corona 
    ##                                                                  973 
    ##                            NYC-Queens CD 6--Forest Hills & Rego Park 
    ##                                                                 1041 
    ##                                NYC-Queens CD 2--Sunnyside & Woodside 
    ##                                                                 1158 
    ##                NYC-Queens CD 5--Ridgewood, Glendale & Middle Village 
    ##                                                                 2040 
    ##                           NYC-Queens CD 9--Richmond Hill & Woodhaven 
    ##                                                                 1694 
    ##                       NYC-Queens CD 12--Jamaica, Hollis & St. Albans 
    ##                                                                 2438 
    ##                          NYC-Queens CD 10--Howard Beach & Ozone Park 
    ##                                                                 1304 
    ##         NYC-Queens CD 14--Far Rockaway, Breezy Point & Broad Channel 
    ##                                                                  954 
    ##                                                                 NA's 
    ##                                                               125514

``` r
summary(educ_indx)
```

    ##  No HS     HS SmColl   Bach    Adv 
    ##  53267  55119  34012  30802  23385

To find mean and standard deviation by neighborhood, you could use
something like this,

``` r
ddply(acs2017_ny, .(PUMA), summarize, mean = round(mean(AGE), 2), sd = round(sd(AGE), 2), n_obsv = length(PUMA))
```

    ##     PUMA  mean    sd n_obsv
    ## 1    100 41.70 23.85   1819
    ## 2    200 43.47 23.45   2624
    ## 3    300 44.88 23.60   1724
    ## 4    401 45.12 24.28   1597
    ## 5    402 42.23 24.18   1774
    ## 6    403 43.22 23.66   2202
    ## 7    500 40.01 23.73   2214
    ## 8    600 39.77 23.29   1670
    ## 9    701 36.64 22.44   1692
    ## 10   702 42.13 23.02   1265
    ## 11   703 43.70 24.11   1945
    ## 12   704 44.71 23.83   2043
    ## 13   800 43.93 23.62   1871
    ## 14   901 45.35 24.04   1288
    ## 15   902 40.74 22.29   1126
    ## 16   903 38.70 22.42    947
    ## 17   904 43.70 23.84   1141
    ## 18   905 40.84 22.75   1099
    ## 19   906 41.48 24.26   1752
    ## 20  1000 42.77 23.43   1536
    ## 21  1101 44.33 24.22   1061
    ## 22  1102 42.49 23.23   1153
    ## 23  1201 44.28 24.74    952
    ## 24  1202 42.72 24.68   1122
    ## 25  1203 43.80 23.84    941
    ## 26  1204 43.57 23.84   1701
    ## 27  1205 40.35 23.50   1333
    ## 28  1206 37.94 22.96   1112
    ## 29  1207 45.42 23.92   1787
    ## 30  1300 43.73 23.35   1885
    ## 31  1400 43.80 24.01   1909
    ## 32  1500 40.94 23.49   1914
    ## 33  1600 43.30 24.68   1510
    ## 34  1700 43.52 23.86   1407
    ## 35  1801 42.63 23.60   1092
    ## 36  1802 43.08 23.23   1356
    ## 37  1900 42.81 23.57   1694
    ## 38  2001 37.81 22.86    923
    ## 39  2002 43.45 23.90   2030
    ## 40  2100 46.88 23.54   1404
    ## 41  2201 41.39 23.92   1311
    ## 42  2202 44.64 23.67   1358
    ## 43  2203 44.31 24.08   1950
    ## 44  2300 37.97 22.08   1105
    ## 45  2401 45.15 23.39   1109
    ## 46  2402 42.78 24.42   2172
    ## 47  2500 42.73 24.33   3051
    ## 48  2600 43.71 24.47   2039
    ## 49  2701 44.76 23.71   1118
    ## 50  2702 42.69 23.16   1533
    ## 51  2801 43.81 23.74   1542
    ## 52  2802 43.19 23.91   1620
    ## 53  2901 41.72 23.03   1042
    ## 54  2902 42.99 22.82   1195
    ## 55  2903 34.24 23.39   1323
    ## 56  3001 44.04 23.76    965
    ## 57  3002 42.34 24.24    943
    ## 58  3003 32.23 24.23   1232
    ## 59  3101 43.50 22.72    959
    ## 60  3102 43.58 23.51   1341
    ## 61  3103 43.50 23.89   1283
    ## 62  3104 39.12 23.54   1093
    ## 63  3105 42.69 24.40   1679
    ## 64  3106 42.68 23.85   1518
    ## 65  3107 42.05 23.57   1762
    ## 66  3201 43.25 25.02   1543
    ## 67  3202 44.08 24.19   1376
    ## 68  3203 43.40 24.06   1013
    ## 69  3204 43.77 23.56   1267
    ## 70  3205 42.64 23.83   1215
    ## 71  3206 40.57 23.55   1207
    ## 72  3207 42.79 24.08   1076
    ## 73  3208 43.68 23.96   1188
    ## 74  3209 43.11 23.37   1021
    ## 75  3210 42.34 23.58    987
    ## 76  3211 41.49 22.93    968
    ## 77  3212 44.51 25.12    962
    ## 78  3301 45.37 24.21    934
    ## 79  3302 43.40 24.24   1018
    ## 80  3303 43.01 24.03   1279
    ## 81  3304 41.35 23.85   1268
    ## 82  3305 48.60 24.31   1326
    ## 83  3306 43.79 22.92   1097
    ## 84  3307 41.86 22.68    881
    ## 85  3308 41.31 23.04   1131
    ## 86  3309 43.69 23.07    948
    ## 87  3310 39.30 22.38    975
    ## 88  3311 40.25 23.23   1036
    ## 89  3312 40.43 22.31    974
    ## 90  3313 40.90 23.82    966
    ## 91  3701 43.12 25.58    936
    ## 92  3702 40.22 22.96   1092
    ## 93  3703 43.63 24.07    767
    ## 94  3704 42.05 24.57   1115
    ## 95  3705 34.78 22.47   1311
    ## 96  3706 35.15 22.40    854
    ## 97  3707 33.70 22.15   1112
    ## 98  3708 35.25 22.01    917
    ## 99  3709 38.88 23.99   1307
    ## 100 3710 35.34 22.09   1166
    ## 101 3801 40.55 22.58   1238
    ## 102 3802 35.62 20.80    872
    ## 103 3803 39.45 21.16    897
    ## 104 3804 38.39 21.37    769
    ## 105 3805 43.53 23.63   1167
    ## 106 3806 42.44 22.85    949
    ## 107 3807 40.20 19.30    944
    ## 108 3808 40.66 21.37    758
    ## 109 3809 40.98 22.18   1062
    ## 110 3810 39.03 20.90   1136
    ## 111 3901 42.89 23.76   1303
    ## 112 3902 41.08 23.88   1173
    ## 113 3903 40.75 22.72   1621
    ## 114 4001 35.39 20.42   1293
    ## 115 4002 34.12 19.37   1060
    ## 116 4003 36.03 20.78   1082
    ## 117 4004 36.75 20.23   1320
    ## 118 4005 36.84 20.31   1168
    ## 119 4006 38.50 20.69   1077
    ## 120 4007 39.63 21.48    904
    ## 121 4008 36.65 22.17   1321
    ## 122 4009 41.51 23.14   2422
    ## 123 4010 42.14 23.08   1250
    ## 124 4011 39.77 22.98    818
    ## 125 4012 36.75 21.46   1291
    ## 126 4013 42.91 22.97   1519
    ## 127 4014 35.35 24.37   1698
    ## 128 4015 38.65 23.33   1479
    ## 129 4016 42.18 24.11   1903
    ## 130 4017 39.67 22.74   2234
    ## 131 4018 45.54 24.77    925
    ## 132 4101 38.65 20.21   1748
    ## 133 4102 38.72 22.55   1316
    ## 134 4103 44.60 23.11   2290
    ## 135 4104 45.76 23.24   1344
    ## 136 4105 41.99 23.29   2148
    ## 137 4106 40.17 23.88   1393
    ## 138 4107 40.09 21.87    973
    ## 139 4108 42.64 23.42   1041
    ## 140 4109 41.20 20.83   1158
    ## 141 4110 40.21 22.13   2040
    ## 142 4111 40.69 21.64   1694
    ## 143 4112 40.37 22.80   2438
    ## 144 4113 39.78 22.77   1304
    ## 145 4114 39.47 24.25    954

Although tapply would also work fine.

Here’s the 90th and 10th percentiles of wages by neighborhood,

``` r
dat_use1 <- subset(acs2017_ny,((INCWAGE > 0) & in_NYC))
ddply(dat_use1, .(PUMA), summarize, inc90 = quantile(INCWAGE,probs = 0.9), inc10 = quantile(INCWAGE,probs = 0.1), n_obs = length(INCWAGE))
```

    ##    PUMA  inc90 inc10 n_obs
    ## 1  3701 120000  3220   424
    ## 2  3702  85000  6700   541
    ## 3  3703 100500  3750   366
    ## 4  3704  90000  6980   510
    ## 5  3705  52000  3000   537
    ## 6  3706  63200  5940   359
    ## 7  3707  60000  4000   439
    ## 8  3708  62000  6000   376
    ## 9  3709  78800  5220   503
    ## 10 3710  55000  3580   420
    ## 11 3801 100000  5000   670
    ## 12 3802 120000  3000   399
    ## 13 3803 130000  6000   478
    ## 14 3804 120000  7000   368
    ## 15 3805 300000 17900   636
    ## 16 3806 326000  7860   509
    ## 17 3807 268000 10000   635
    ## 18 3808 300000 20560   460
    ## 19 3809 140000  5000   515
    ## 20 3810 300000  6000   695
    ## 21 3901 127000  6220   617
    ## 22 3902 125000  8000   524
    ## 23 3903 100000  7100   771
    ## 24 4001 149500 10000   736
    ## 25 4002  82000  9000   581
    ## 26 4003 110000  7200   557
    ## 27 4004 166000  7000   786
    ## 28 4005 200000 12000   681
    ## 29 4006 114000  8740   585
    ## 30 4007  79000  4800   361
    ## 31 4008  73000  6000   549
    ## 32 4009 100000  9600  1178
    ## 33 4010  80200  8360   610
    ## 34 4011  95400  7000   407
    ## 35 4012 102200  6880   625
    ## 36 4013 124000  7440   773
    ## 37 4014  90000  5590   654
    ## 38 4015 100000  7450   710
    ## 39 4016 101200  6000   899
    ## 40 4017  97000  7200  1070
    ## 41 4018 100000  5000   368
    ## 42 4101 104000  9600  1041
    ## 43 4102  82400  8000   624
    ## 44 4103 100000  7180  1107
    ## 45 4104 110000  8000   661
    ## 46 4105 100000  7000  1080
    ## 47 4106 102000  8000   641
    ## 48 4107  70000  8000   499
    ## 49 4108 140000 10000   563
    ## 50 4109 129600 11600   655
    ## 51 4110 100000 10000  1049
    ## 52 4111  90000  8680   865
    ## 53 4112  84800  7260  1213
    ## 54 4113  93000  6000   625
    ## 55 4114 108300  6700   378

You could also use table (or crosstabs) for factors with fewer items,

``` r
table(educ_indx,female)
```

    ##          female
    ## educ_indx  male female
    ##    No HS  27180  26087
    ##    HS     27309  27810
    ##    SmColl 15847  18165
    ##    Bach   14632  16170
    ##    Adv    10254  13131

``` r
xtabs(~educ_indx + female)
```

    ##          female
    ## educ_indx  male female
    ##    No HS  27180  26087
    ##    HS     27309  27810
    ##    SmColl 15847  18165
    ##    Bach   14632  16170
    ##    Adv    10254  13131

Want proportions instead of counts?

``` r
prop.table(table(educ_indx,female))
```

    ##          female
    ## educ_indx       male     female
    ##    No HS  0.13826080 0.13270087
    ##    HS     0.13891701 0.14146552
    ##    SmColl 0.08061144 0.09240278
    ##    Bach   0.07443091 0.08225450
    ##    Adv    0.05216064 0.06679553

*Remember prop.table later when we do marginals.*

Try it and see what happens if you use table with PUMA…

This data includes not just whether a person has a college degree but
also what field was the degree in: Economics or Psychology, for
instance. Look over the codebook about DEGFIELD and DEGFIELDD (that
second D means more detail) to see the codes. Maybe look at 10th and
90th percentiles by degree field?

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
at “prime-age” people, get the same results:

``` r
mean(educ_nohs[(AGE >= 25)&(AGE <= 55)])
```

    ## [1] 0.08354656

``` r
mean(educ_hs[(AGE >= 25)&(AGE <= 55)])
```

    ## [1] 0.2974594

``` r
mean(educ_somecoll[(AGE >= 25)&(AGE <= 55)])
```

    ## [1] 0.2057843

``` r
mean(educ_college[(AGE >= 25)&(AGE <= 55)])
```

    ## [1] 0.2383112

``` r
mean(educ_advdeg[(AGE >= 25)&(AGE <= 55)])
```

    ## [1] 0.1748986

``` r
# alternatively
restrict1 <- as.logical((AGE >= 25)&(AGE <= 55))
dat_age_primeage <- subset(acs2017_ny, restrict1)

detach()
attach(dat_age_primeage)

mean(educ_nohs)
```

    ## [1] 0.08354656

``` r
mean(educ_hs)
```

    ## [1] 0.2974594

``` r
mean(educ_somecoll)
```

    ## [1] 0.2057843

``` r
mean(educ_college)
```

    ## [1] 0.2383112

``` r
mean(educ_advdeg)
```

    ## [1] 0.1748986

``` r
detach()
```

So you detach the original data frame and instead attach the restricted
version. Then any subsequent analysis would be just done on that subset.
Just remember that you’ve done this (again, this is a good reason to
save the commands in a program so you can look back) otherwise you’ll
wonder why you suddenly don’t have any kids in the sample\!

## Why All These Details?

You might be tired and bored by these details, but note that there are
actually important choices to be made here, even in simply defining
variables. Take the fraught American category of “race”. This data has a
variable, RACED, showing how people chose to classify themselves, as
‘White,’ ‘Black,’ ‘American Indian,’ ‘Asian,’ various combinations,
and many more codes.

Suppose you wanted to find out how many Asians are in a particular
population. You could count how many people identify themselves as Asian
only; you could count how many people identify as Asian in any
combination. Sometimes the choice is irrelevant; sometimes it can skew
the final results (e.g. the question in some areas, are there more
African-Americans or more Hispanics?).

Again, there’s no “right” way to do it because there’s no science in
this peculiar-but-popular concept of “race”. People’s conceptions of
themselves are fuzzy and complicated; these measures are approximations.

## Basics of government race/ethnicity classification

The US government asks questions about people’s race and ethnicity.
These categories are social constructs, which is a fancy way of pointing
out that they are based on people’s own views of themselves (influenced
by how we think that other people think of us…). Currently the standard
classification asks people separately about their “race” and “ethnicity”
where people can pick labels from each category in any combination.

The “race” categories include: “White,” “African-American,” “American
Indian,” “Asian,” and others. Then the supplemental race categories
offer more detail.

These are a peculiar combination of very general (well over 40% of the
world’s population is “Asian”) and very specific (“American Indian” has
fewer) representing a peculiar history of popular attitudes in the US.
Only in the 2000 Census did they start to classify people in mixed
races. If you were to go back to historical US Censuses from more than a
century ago, you would find that the category “race” included separate
entries for Irish and French. Stephen J Gould has a fascinating book,
The Mismeasure of Man, discussing how early scientific classifications
of humans tried to “prove” which nationalities/races/groups were the
smartest. Ta-Nehisi Coates notes, “racism invented race in America.”
Throughout history, statistics have been used to try to prove peoples’
prejudices.

Note that “Hispanic” is not “race” but rather ethnicity (includes
various other labels such as Spanish, Latino, etc.). So a respondent
could choose “Hispanic” and any race category – some choose “White,”
some choose “African American,” some might be combined with any other of
those complicated racial categories.

If you wanted to create a variable for those who report themselves as
African-American and Hispanic, you’d use the expression (AfAm == 1) &
(Hispanic == 1); sometimes government stats report for non-Hispanic
whites so (white == 1) & (Hispanic \!= 1). You can create your own
classifications depending on what questions you’re investigating. This
data includes items on birthplace and ancestry (more detail on
relatives\!).

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

Educational attainment is also classified with complicated codes in this
data: the original data has code 63 to mean high school diploma, 64 for
a GED, 65 for less than a year of college, etc. I have transformed them
into a series of dummy variables, zero/one variables for whether a
person has no high school diploma, just a high school diploma, some
college, a bachelor’s degree, or an advanced degree. As with race, there
is no rule that you must always do it thus. Your own analysis might be
different. (Is it valid to lump together everybody who lacks a high
school diploma, no matter whether they completed just first grade or up
to 12th?)

That’s the whole point of learning to do the data work for yourself: you
can see all of the little decisions that go into creating a conclusion.
Some conclusions might be fragile so a tiny decision about coding could
change everything; other conclusions are robust to deviations. You must
find out.

# De-bugging

Without a doubt, programming is tough. In R or with any other program,
it is frustrating and complicated and difficult to do it the first few
times. Some days it feels like a continuous battle just to do the
simplest thing\! Keep going despite that, keep working on it.

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

\#\#Do it The first homework assignment asks you to start working on
these questions. Begin by running the code that I give here, just to see
if you can replicate my results. Then start asking more questions about
the data - there is so much info there\! Immigration/ancestry status,
how they commute, health insurance, poverty, income, owner or renter
(and how much they pay\!), all sorts of info. Have some fun.
