2/20/2022

# Collecting Anime listings from various platforms

I currently have a tableau dashboard located
[here](https://public.tableau.com/app/profile/patrick.mendoza5877/viz/WhatAnimetoWatchNextMyAnimeList/Dashboard).
This dashboard is an exploratory view of top ranked and popular anime
titles based primarily on data from <http://myanimelist.net>. The
purpose of this dashboard is to assist the end-user in locating their
next anime to watch. However, what is lacking from this tool is the
streaming platform that currently houses the titles.

To add that information into the dashboard, I will need to either
locate, or scrape the anime titles that are housed on various platforms.

The most popular platforms for viewing anime titles are:

-   Crunchyroll
-   [Funimation](https://github.com/patmendoza330/funimationtitles)
-   Netflix
-   Hulu
-   Amazon Prime

This list is not all-inclusive and in no particular order.

## First things first - setting some variables

Set your working directory to wherever you’d like in the
`WORKINDIRECTORY` section.

``` r
wd1 = WORKINGDIRECTORY
setwd(wd1)
```

## Install and load any libraries

The `tidyverse` contains the `rvest` library which will be used to
scrape the data, while `dplyr` and `tidyr` will be used to
manipulate/transform data.

``` r
install.packages(c("tidyverse","textutils"))
```

Next, we want to load three libraries:

``` r
library(tidyverse)
library(rvest)
library(textutils)
```

Although rvest is a component of the tidyverse, it doesn’t automatically
load with the library call `tidyverse`, as a result, you’ll need to load
it separately. The library `textutils` will allow you to decode html
special characters into their expected values.

Lets start with Crunchyroll:

## Crunchyroll

This is the first of few streaming platforms that I will be scraping as
an add-on to my anime suggestion tableau dashboard. Thankfully,
Crunchyroll has a listing of all of their titles on a webpage at:
<http://crunchyroll.com/videos/anime/alpha?group=all>.

### Scraping the Crunchyroll Title List

``` r
crunchyroll <- read_html('http://crunchyroll.com/videos/anime/alpha?group=all')

crunchyroll_titles <- crunchyroll %>% 
  html_elements('a') %>% 
  html_attr('title') %>%
  HTMLdecode() %>%
  na.omit()
```

The code above pulls all titles within link references `a` and decodes
any special html characters to regular characters and omits any null
entries.

### Cleaning the dataset

``` r
head(crunchyroll_titles, n=80)
```

    ##  [1] "Winter 2022"                                            
    ##  [2] "Fall 2021"                                              
    ##  [3] "Summer 2021"                                            
    ##  [4] "Spring 2021"                                            
    ##  [5] "Winter 2021"                                            
    ##  [6] "Fall 2020"                                              
    ##  [7] "Summer 2020"                                            
    ##  [8] "Spring 2020"                                            
    ##  [9] "Winter 2020"                                            
    ## [10] "Fall 2019"                                              
    ## [11] "Summer 2019"                                            
    ## [12] "Spring 2019"                                            
    ## [13] "Winter 2019"                                            
    ## [14] "Fall 2018"                                              
    ## [15] "Summer 2018"                                            
    ## [16] "Spring 2018"                                            
    ## [17] "Winter 2018"                                            
    ## [18] "Fall 2017"                                              
    ## [19] "Summer 2017"                                            
    ## [20] "Spring 2017"                                            
    ## [21] "Winter 2017"                                            
    ## [22] "Fall 2016"                                              
    ## [23] "Summer 2016"                                            
    ## [24] "Spring 2016"                                            
    ## [25] "Winter 2016"                                            
    ## [26] "Fall 2015"                                              
    ## [27] "Summer 2015"                                            
    ## [28] "Spring 2015"                                            
    ## [29] "Winter 2015"                                            
    ## [30] "Fall 2014"                                              
    ## [31] "Summer 2014"                                            
    ## [32] "Spring 2014"                                            
    ## [33] "Winter 2014"                                            
    ## [34] "Fall 2013"                                              
    ## [35] "Summer 2013"                                            
    ## [36] "Spring 2013"                                            
    ## [37] "Winter 2013"                                            
    ## [38] "Fall 2012"                                              
    ## [39] "Summer 2012"                                            
    ## [40] "Spring 2012"                                            
    ## [41] "Winter 2012"                                            
    ## [42] "Fall 2011"                                              
    ## [43] "Summer 2011"                                            
    ## [44] "Spring 2011"                                            
    ## [45] "Winter 2011"                                            
    ## [46] "Fall 2010"                                              
    ## [47] "Summer 2010"                                            
    ## [48] "Spring 2010"                                            
    ## [49] "Winter 2010"                                            
    ## [50] "Fall 2009"                                              
    ## [51] "Summer 2009"                                            
    ## [52] "Spring 2009"                                            
    ## [53] "Winter 2009"                                            
    ## [54] "07 Ghost"                                               
    ## [55] "100% Teacher Pascal"                                    
    ## [56] "11eyes"                                                 
    ## [57] "22/7 (nanabun no nijyuuni)"                             
    ## [58] "8 Man After"                                            
    ## [59] "86 EIGHTY-SIX"                                          
    ## [60] "91 Days"                                                
    ## [61] "<DOGEZA>I Tried Asking While Kowtowing."                
    ## [62] "A Bridge to the Starry Skies - Hoshizora e Kakaru Hashi"
    ## [63] "A Centaur's Life"                                       
    ## [64] "A Certain Magical Index"                                
    ## [65] "A Certain Scientific Accelerator"                       
    ## [66] "A Certain Scientific Railgun"                           
    ## [67] "A Dark Rabbit has Seven Lives"                          
    ## [68] "A Destructive God Sits Next to Me"                      
    ## [69] "A Lull in the Sea (Nagi-Asu: Nagi no Asukara)"          
    ## [70] "A Place Further Than the Universe"                      
    ## [71] "A Sister's All You Need."                               
    ## [72] "A Talk on Titan"                                        
    ## [73] "A Town Where You Live"                                  
    ## [74] "A Wind Named Amnesia"                                   
    ## [75] "Aachi & Ssipak"                                         
    ## [76] "ABCiee Working Diary"                                   
    ## [77] "ACCA: 13-Territory Inspection Dept."                    
    ## [78] "Accel World"                                            
    ## [79] "Accel World: Infinite Burst"                            
    ## [80] "Ace Attorney"

Based on this, it looks like the first 53 links are not needed. So lets
get rid of them

``` r
length(crunchyroll_titles)
```

    ## [1] 1242

``` r
crunchyroll_titles <- tail(crunchyroll_titles, n = 1189)
```

We’ve removed the leading titles, now we need to find the cutoff

``` r
tail(crunchyroll_titles, n=30)
```

    ##  [1] "Yotsuiro Biyori"                                         
    ##  [2] "You and Me"                                              
    ##  [3] "You and Me 2"                                            
    ##  [4] "You Don't Know Gunma Yet"                                
    ##  [5] "You're Being Summoned, Azazel"                           
    ##  [6] "Young Black Jack"                                        
    ##  [7] "Your lie in April"                                       
    ##  [8] "Yowamushi Pedal"                                         
    ##  [9] "Yowamushi Pedal New Generation"                          
    ## [10] "Yu-Gi-Oh!"                                               
    ## [11] "Yu-Gi-Oh! 5D's"                                          
    ## [12] "Yu-Gi-Oh! ARC-V"                                         
    ## [13] "Yu-Gi-Oh! GX"                                            
    ## [14] "Yu-Gi-Oh! VRAINS"                                        
    ## [15] "Yu-Gi-Oh! ZEXAL"                                         
    ## [16] "YU-NO: A Girl Who Chants Love at the Bound of This World"
    ## [17] "YUKI YUNA IS A HERO"                                     
    ## [18] "Yuri!!! on ICE"                                          
    ## [19] "Yurumates3Dei"                                           
    ## [20] "YuruYuri"                                                
    ## [21] "Yuuna and the Haunted Hot Springs"                       
    ## [22] "Zapuni"                                                  
    ## [23] "Zo Zo Zombie"                                            
    ## [24] "ZOMBIE LAND SAGA"                                        
    ## [25] "Zombie Loan"                                             
    ## [26] "Attack on Titan"                                         
    ## [27] "Shenmue the Animation"                                   
    ## [28] "Demon Slayer: Kimetsu no Yaiba"                          
    ## [29] "The Case Study of Vanitas"                               
    ## [30] "CUE!"

The last title on the webpage is ‘Zombie Loan’, so we need to eliminate
the last five items

``` r
length(crunchyroll_titles)
```

    ## [1] 1189

``` r
crunchyroll_titles <- head(crunchyroll_titles, n = 1184)
```

And there we have it, a list of all crunchyroll titles scrapped from
their website.

### Formatting and exporting

Lets make it a dataframe and export the table

``` r
df <- data.frame(title = crunchyroll_titles)
knitr::kable(head(df))
```

| title                      |
|:---------------------------|
| 07 Ghost                   |
| 100% Teacher Pascal        |
| 11eyes                     |
| 22/7 (nanabun no nijyuuni) |
| 8 Man After                |
| 86 EIGHTY-SIX              |

``` r
write.csv(df, 'crunchyrolltitles.csv', row.names = FALSE) 
```
