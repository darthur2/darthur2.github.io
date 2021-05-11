---
layout: post
title: "Small Towns and Large Cities: Where to Live?"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
categories: analysis
tags: hypothesis-testing eda
---

I was recently asked whether I think people prefer living in rural or
urban areas. I thought it would be interesting to scrounge up some data
to try and answer this question and see what I find. If it were
possible, the best way to answer this question would be to randomly
assign a lot of people to living their lives in either an urban or rural
area and then give their experience a rating. Then we could compare the
average scores between the two places and see which one was
higher.\[1\]. We (or at least I) can’t do that, so I tried to do
something that was both simple, but still somewhat similar. In this blog
post I will do the following:

  - Explain where the data came from
  - Explain how the sample was collected
  - Use simple statistics to compare “rural” and “urban” locations

This is a straightforward analysis so we can get right to it\!

# The Data

Collecting these data was a somewhat involved process. Part of the
problem had to do with how to define “rural” and “urban”. The [United
States Census
Bureau](https://www.census.gov/programs-surveys/geography/guidance/geo-areas/urban-rural/2010-urban-rural.html)
provides some definitions, but unfortunately they don’t involve clear
cut distinctions. I thought that the definitions might be based on
population size or density, but this wasn’t the case. Since I wasn’t
able to find a list of all cities in the United States with a tag for
each designating its status as rural or urban, I tried to come up with
my own, clear cut (but hopefully sensible) definition.

My first approach was to use a cutoff for population density, but this
led to some odd outcomes. After testing several cutoffs, I still ended
up with small towns being labeled “urban”. When you only have 100
people, but they all live right on top of each other, the population
density can be comparable to some very large metropolitan areas. This
wouldn’t work. New York City and Samson, Alabama just aren’t the
same.\[2\]

After trying several other things, I finally decided to just distinguish
between places with large populations and places with small populations.
Places with more than 20,000 people were considered large and those with
less than 20,000 were considered small. I also decided to exclude
locations with less than 500 people. With small places like this it was
sometimes difficult to find ratings for them on Niche. So technically my
sample generalizes to “small” towns with more than 500 but less than
20,000 people and “large” cities with more than 20,000 people. You can
find the list of cities and their populations
[here](https://www2.census.gov/geo/docs/maps-data/data/gazetteer/2019_Gazetteer/)
and
[here](https://www.census.gov/programs-surveys/popest/data/tables.2019.html).

The other data source was [Niche](https://www.niche.com/). Niche is a
website that provides location ratings for several different things
(Crime, Education, etc.). After selecting the locations (a process I
will describe in the next section), I was able to look them up and
gather the ratings for each location. I didn’t actually do this by hand,
I wrote a program to do most of it for me, but the idea is the same.

# Sampling Procedure

How the locations were selected was important. I wanted things to be as
random as possible. Any time your sample is collected in a systematic
way, you run into problems with confounding variables and other biases.
I also wanted the sample to also be representative of the United States.
This meant that just randomly selecting cities and towns from the entire
country wouldn’t work. States with a lot of people would be represented
more in the sample than other, smaller states. Finally, I wanted to make
sure that I had enough small and large locations to perform a fair
comparison. Most of the locations in the U.S. are smaller places. It
wouldn’t be as informative to compare 200 small towns to 10 large
cities.

To make sure all of these requirements were met, I selected the same
number of locations (10) from each state. Locations in each state were
divided up beforehand depending on their population size (large vs
small) and then five small towns and five large cities were randomly
selected. This resulted in 460 locations to make comparisons, 230 small
and 230 large. You might immediately notice that this number should be
500 since there are 50 states, but unfortunately some very small states
didn’t have enough cities and towns for me to get five of both so they
were left out (these included Delaware, Vermont, Alaska, and Hawaii).
However, hopefully other states are similar enough to these states that
the results can still generalize to them.

As I mentioned before, after these 460 locations were selected, I used a
web scraper to collect the ratings off Niche. After this was done, I had
all the data I needed to make some simple comparisons.

# Location Comparisons

After gathering the scores for each locations, the data looked like
this:

    ##           NAME  STNAME POP2019 URBAN_IND Overall Niche Grade Public Schools
    ## 9       Daphne Alabama   26869         1                   A             A-
    ## 31 Phenix City Alabama   72974         1                   B             B-
    ## 17      Helena Alabama   39850         1                  A+             A-
    ## 35  Tuscaloosa Alabama  101129         1                  A-              B
    ## 29      Oxford Alabama   42450         1                  A-              A
    ## 32      Newnan Georgia   41581         1                  A-             B+
    ##    Crime & Safety Housing Nightlife Good for Families Diversity Jobs Weather
    ## 9               B      A-        B+                 A        A-    A       B
    ## 31              C      B-         B                B-         A    C      B+
    ## 17             B+       A        B-                A+        B+    A       B
    ## 35              C      C+         A                B+         A   B-       B
    ## 29             C+      A-        B-                A-        A-    B       B
    ## 32              C       B        B-                A-        A+   A-       B
    ##    Cost of Living Health & Fitness Outdoor Activities Commute
    ## 9              B+               B+                 B-      C+
    ## 31             B+               C+                  B      B-
    ## 17             B+               B+                 B+      C-
    ## 35              B               B-                 B+      B+
    ## 29             A-               B-                 B+      B-
    ## 32              B               B+                 A-      B-

The names of the categories that were rated for each location are:

    ##  [1] "Overall Niche Grade" "Public Schools"      "Crime & Safety"     
    ##  [4] "Housing"             "Nightlife"           "Good for Families"  
    ##  [7] "Diversity"           "Jobs"                "Weather"            
    ## [10] "Cost of Living"      "Health & Fitness"    "Outdoor Activities" 
    ## [13] "Commute"

If you want to see more information about how scores were computed, you
can find that
[here](https://www.niche.com/about/methodology/best-places-to-live/).

I converted the letter grades to numeric scores using the usual “GPA”
scale and the result was this:

    ##           NAME  STNAME POP2019 URBAN_IND Overall Niche Grade Public Schools
    ## 9       Daphne Alabama   26869         1                 4.0            3.7
    ## 31 Phenix City Alabama   72974         1                 3.0            2.7
    ## 17      Helena Alabama   39850         1                 4.4            3.7
    ## 35  Tuscaloosa Alabama  101129         1                 3.7            3.0
    ## 29      Oxford Alabama   42450         1                 3.7            4.0
    ## 32      Newnan Georgia   41581         1                 3.7            3.3
    ##    Crime & Safety Housing Nightlife Good for Families Diversity Jobs Weather
    ## 9             3.0     3.7       3.3               4.0       3.7  4.0     3.0
    ## 31            2.0     2.7       3.0               2.7       4.0  2.0     3.3
    ## 17            3.3     4.0       2.7               4.4       3.3  4.0     3.0
    ## 35            2.0     2.3       4.0               3.3       4.0  2.7     3.0
    ## 29            2.3     3.7       2.7               3.7       3.7  3.0     3.0
    ## 32            2.0     3.0       2.7               3.7       4.4  3.7     3.0
    ##    Cost of Living Health & Fitness Outdoor Activities Commute
    ## 9             3.3              3.3                2.7     2.3
    ## 31            3.3              2.3                3.0     2.7
    ## 17            3.3              3.3                3.3     1.7
    ## 35            3.0              2.7                3.3     3.3
    ## 29            3.7              2.7                3.3     2.7
    ## 32            3.0              3.3                3.7     2.7

The following plot shows the comparisons for each categories. Each plot
shows two box plots that represent the distribution of scores for the
two types of locations. In this case “Urban Status” is just an indicator
for large and small locations. The pink color (0) is for small towns and
the blue color (1) is for large cities.

![](/images/living-unnamed-chunk-11-1.png)<!-- -->

Just by looking at this graph it looks like the median score for large
cities tends to be higher for most categories. However, if I were to run
through this process again and I randomly selected a different set of
460 locations, the results would look slightly different. This is why
it’s important to not just ask if two groups are different, but if
they are “significantly different”. This means that you are confident
that these results would apply to almost any random sample you
collected, not just the one you are looking at. Below is another graph
that shows these comparisons again, but now there is an asterisk for
each category for which there is a statistically significant difference.

![](/images/living-unnamed-chunk-12-1.png)<!-- -->

It looks like many of the differences are statistically significant and
that they tend to favor larger cities. In other words, places with
populations larger than 20,000 received significantly higher scores for
the following categories: Commute, Diversity, Health & Fitness,
Nightlife, Outdoor Activities, Overall Niche Grade, and Public Schools.
Places with fewer than 20,000 people (but more than 500) did win in two
categories: Cost of Living and Crime & Safety. I should mention there
were a lot of missing values for Crime & Safety and most of these were
for smaller locations. However, my guess is that even if there weren’t
any missing values, the outcome would be the same. I think it’s pretty
well known that places with fewer people generally experience less
crime. It also makes sense that they would have a better (lower) cost of
living.

Apart from that though, it seems like it’s “better” to live in places
with more than 20,000 people. This is probably just because when there
are more people, you have more access to things like Gyms or Clubs. This
isn’t to say at all that people are better off for living in “large”
cities and the results would change depending on what you consider
large. These data just suggest that places with more people enjoy
certain perks. In the future I might look into looking into the specific
relationships between these variables (correlations, regression
relationships, etc.). I also might look into how the overall rating
might change depending on personal preferences. If I don’t care about
the Nightlife, then my overall score wouldn’t put much importance on
this. However, for now I think it’s interesting just to get a broad
picture about how the two types of locations compare.

# Conclusion

There you have it\! Thanks for reading another blog post\! I was
honestly surprised with the outcome. I expected to see higher scores for
smaller towns. After looking at the results and thinking about it some
more, it made sense that places with more people would score higher for
many of these categories though. I suppose that’s why we ask questions
and then collect data. Sometimes our intuition is wrong and we find
something that suprises us.

1.  I guess technically the best way would involve an omniscient being
    that could just tell us which was better, but I didn’t want to stray
    too far from reality

2.  This is for many reasons, one of them being that Samson has an
    “Eternal Mayor” named Clay “Not Guilty” King as their form of
    government. New York City does not.
