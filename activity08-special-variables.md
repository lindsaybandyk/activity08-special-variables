Activity 8
================
Lindsay Bandykk

## Data and packages

Load the entirety of the `{tidyverse}`. `{forcats}` and `{stringr}` are
loaded as part of this. If you wish to work with dates during this
activity, you will need to also load `{lubridate}`. Be sure to avoid
printing out any unnecessary information and give the code chunk a
meaningful name.

Cheatsheets that you might want to add to your collection:

-   [`{stringr}`](https://stringr.tidyverse.org/)
-   [`{forcats}`](https://forcats.tidyverse.org/)
-   [`{lubridate}`](https://lubridate.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv` .
Assign the file to a meaningful object name, be sure to avoid printing
any unnecessary information, and give the code chunk a meaningful name.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.4     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
songs <- read_delim(here::here("data/billboard_songs.txt"), delim="\t")
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   title = col_character(),
    ##   artist = col_character(),
    ##   `overall peak` = col_double(),
    ##   `weeks on chart` = col_double(),
    ##   `chart date` = col_double()
    ## )

These data include information on song popularity. In the US, the
Billboard Hot 100 is a list that comes out every week, showing the 100
most played songs that week. More information about the creation of this
dataset, as well as some analyses by the author, can be found here:
<https://mikekling.com/analyzing-the-billboard-hot-100/>. The dataset
you are provided is a limited version of the full data, containing:

-   `title`
-   `artist`
-   `overall peak`: The highest rank the song ever reached (1 is the
    best)
-   `weeks on chart`: The number of weeks the song was on the chart
-   `chart date`: The latest date the song appeared on the Billboard Hot
    100

This is a long dataset (34,605 observations)! You might like to create a
small dataset with only, say, 200 of the rows to try all your code out
on the smaller dataset first, and then only run the analysis of the full
data after you have experienced everything. One way to do this is to use
a function like `slice_sample(n = ...)` from `{dplyr}`.

    # Randomly sample 200 rows
    dataset %>% 
      slice_sample(n = 200)

``` r
sample <- songs %>% 
  slice_sample(n=200)
```

If you wish to work with `{stringr}`, I find it useful to work on
vectors first. A way to do this is with `pull` from `{dplyr}`.

    # Turn variable from dataset into a vector
    dataset %>% 
      pull(variable)

## Analysis

You are encouraged to explore these data as you wish using functions
from the packages for the three special variable types. Some ideas that
you might be interested in:

-   What 10 songs (display title, artist, and week) were on the charts
    for the longest?
-   What distinct date did the oldest song(s) leave the charts?
-   What songs could have been played at your 16th birthday party? That
    is, which songs that eventually peaked at \#1 **entered** the charts
    within a couple months (before or after) your 16th birthday?
    Assuming you were 16 years old by 2015.
-   Which artist has been **featured** on the most Billboard charting
    songs?
-   Which artist has **collaborated** on the most Billboard charting
    songs?
-   Create some data visualization controlling the order of the
    character/string variables.

``` r
sample %>% 
  arrange(desc(`weeks on chart`)) %>% 
  slice_head(n=10) %>% 
  select(title, artist, `weeks on chart`)
```

    ## # A tibble: 10 x 3
    ##    title              artist                        `weeks on chart`
    ##    <chr>              <chr>                                    <dbl>
    ##  1 Yeah               USHER feat LUDACRIS & LIL JON               45
    ##  2 If You're Gone     MATCHBOX TWENTY                             42
    ##  3 Freek-A-Leek       PETEY PABLO                                 39
    ##  4 Gloria             LAURA BRANIGAN                              36
    ##  5 Take A Bow         MADONNA                                     30
    ##  6 Boogie Nights      HEATWAVE                                    28
    ##  7 Missing You        DIANA ROSS                                  27
    ##  8 Set U Free         PLANET SOUL                                 26
    ##  9 This I Promise You 'N SYNC                                     26
    ## 10 G.H.E.T.T.O.U.T.   CHANGING FACES                              25

``` r
songs %>% 
  mutate(days=as_date(ymd(`chart date`))) %>% 
  arrange(days)
```

    ## # A tibble: 34,605 x 6
    ##    title      artist     `overall peak` `weeks on chart` `chart date` days      
    ##    <chr>      <chr>               <dbl>            <dbl>        <dbl> <date>    
    ##  1 Playmates  KAY KYSER…              4                1     19400720 1940-07-20
    ##  2 Where Was… CHARLIE B…              6                1     19400720 1940-07-20
    ##  3 Imaginati… TOMMY DOR…              8                1     19400720 1940-07-20
    ##  4 Make-Beli… MITCHELL …             10                1     19400720 1940-07-20
    ##  5 Make-Beli… JAN SAVIT…              8                1     19400727 1940-07-27
    ##  6 Imaginati… GLENN MIL…              3                3     19400803 1940-08-03
    ##  7 Pennsylva… GLENN MIL…              5                6     19400824 1940-08-24
    ##  8 When The … GLENN MIL…             10                1     19400824 1940-08-24
    ##  9 6 Lessons… JIMMY DOR…              7                2     19400831 1940-08-31
    ## 10 Fools Rus… GLENN MIL…              8                2     19400907 1940-09-07
    ## # … with 34,595 more rows

``` r
birth <- ymd(20141115)
songs %>% 
  filter(`overall peak`==1) %>% 
  mutate(startDate=ymd(`chart date`)-7*(`weeks on chart`)) %>% 
  filter(birth %within% interval(ymd(startDate), ymd(`chart date`)))
```

    ## # A tibble: 7 x 6
    ##   title     artist       `overall peak` `weeks on chart` `chart date` startDate 
    ##   <chr>     <chr>                 <dbl>            <dbl>        <dbl> <date>    
    ## 1 Blank Sp… TAYLOR SWIFT              1               17     20150307 2014-11-08
    ## 2 Shake It… TAYLOR SWIFT              1               27     20150307 2014-08-30
    ## 3 All Abou… MEGHAN TRAI…              1               33     20150307 2014-07-19
    ## 4 Rude      MAGIC!                    1               41     20150214 2014-05-03
    ## 5 Fancy     IGGY AZALEA…              1               39     20141213 2014-03-15
    ## 6 All Of Me JOHN LEGEND               1               56     20141206 2013-11-09
    ## 7 Happy     PHARRELL WI…              1               47     20141206 2014-01-11

``` r
songs %>% 
  mutate(feat=str_extract(artist, pattern="featuring .*")) %>% 
  mutate(featured=str_sub(feat, start=10)) %>% 
  mutate(individual=str_split(featured, pattern=c(", ", "&")))
```

    ## Warning in stri_split_regex(string, pattern, n = n, simplify = simplify, :
    ## longer object length is not a multiple of shorter object length

    ## # A tibble: 34,605 x 8
    ##    title   artist   `overall peak` `weeks on chart` `chart date` feat   featured
    ##    <chr>   <chr>             <dbl>            <dbl>        <dbl> <chr>  <chr>   
    ##  1 Uptown… MARK RO…              1               15     20150307 featu… " BRUNO…
    ##  2 Thinki… ED SHEE…              2               20     20150307 <NA>    <NA>   
    ##  3 Love M… ELLIE G…              3                7     20150307 <NA>    <NA>   
    ##  4 Sugar   MAROON 5              4                6     20150307 <NA>    <NA>   
    ##  5 Take M… HOZIER                2               28     20150307 <NA>    <NA>   
    ##  6 FourFi… RIHANNA…              4                5     20150307 <NA>    <NA>   
    ##  7 Blank … TAYLOR …              1               17     20150307 <NA>    <NA>   
    ##  8 Style   TAYLOR …              8                9     20150307 <NA>    <NA>   
    ##  9 Earned… THE WEE…              9                9     20150307 <NA>    <NA>   
    ## 10 Lips A… MEGHAN …              4               18     20150307 <NA>    <NA>   
    ## # … with 34,595 more rows, and 1 more variable: individual <list>

## Attribution

Parts of this Activity are based on a lab from [Dr. Kelly
Bodwin’s](https://www.kelly-bodwin.com/) STAT 331 course.
