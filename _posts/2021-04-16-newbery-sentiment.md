---
layout: post
title: "Sentiment Analysis for Reviews of Newbery Winners"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
categories: analysis
tags: sentiment nlp
---

In my previous two posts
([here](https://darthur2.github.io/analysis/2021/04/14/newbery-lda.html)
and
[here](https://darthur2.github.io/analysis/2021/04/15/newbery-lda-2.html),
I have been learning more about Newbery award winners and what people
think about them. I thought it would be interesting to do a sentiment
analysis on the book reviews I had collected from different websites and
blogs. In this post, I will do the following:

1.  Explain a little about sentiment analysis.
2.  Perform a sentiment analysis on the book reviews for Newbery
    winners.

I won’t be explaining much about where the data came from in this post
but, you can go to my [first
post](https://darthur2.github.io/analysis/2021/04/14/newbery-lda.html)
if you would like to know a little more about that.

# Sentiment Analysis

The goal of a sentiment analysis is to analyze text data to understand
how people feel about something. This is something that many people are
interested in (especially large companies) which means there a lot of
tools and methods out there to do this, some of which can be very
sophisticated. However, no matter the level of sophistication, there are
three main ideas that contribute to any sentiment analysis: polarity,
valence shifters, and amplifiers. We’ll look at a simple example to see
how these ideas come into play when determining the sentiment of a text.
Consider the following:

    ## [1] "The book was okay. I didn't really like it though. It was too long and the characters weren't very interesting"

When we read this, we have a pretty good idea whether the sentiment is
negative or positive, but how would a computer do it? The first step
would be to assign words a polarity. The polarity of a word is a numeric
value that represents where that word falls on a scale of “very
negative” to “very positive”. For now we will say our scale goes from
-1 to 1. Most words, like “okay”, “I”, and “long”, would be neutral and
therefore have a polarity of 0. Other words like “interesting” or
“like”, by themselves, would be positive and might have a polarity
of 0.75 and 0.5 respectively. If we look at the polarity of each word by
itself, we would say that these sentences have a lot of positive words.
However, we know that saying the sentiment here is positive would be a
mistake, so polarity by itself isn’t enough to judge sentiment.

This is where the other two concepts of valence shifting and
amplification come into play. Amplifiers are words that enhance the
polarity of nearby words. In our sentences, the words “really” and
“very” are amplifiers and they enhance the words “like” and
“interesting”. Valence shifters are words that reverse the polarity of
a surrounding word. In our sentences, words like “didn’t” and “weren’t”
would be considered valence shifters. By adding the word “really” to
“like” the sentiment might increase from 0.5 to 0.65 and then by
augmenting the phrase “really like” with “didn’t” we would flip the
polarity to obtain a score of -0.65. By doing this with our text, a
computer would begin to recognize that the sentiment here is negative,
not positive.

Some algorithms and sentiment analysis models add upon these ideas, but
these three ideas are at the heart of most sentiment analysis tools. The
tool I will be using today computes a sentiment score for each
individual sentence using these ideas of polarity, valence shifting, and
amplification. It also takes into account how many words in the sentence
are neutral so short sentences with a lot of positive phrases will
receive a more positive sentiment score than long sentences with a few
positive things here are there. The sentence level sentiment scores for
our example text would be:

    ##    sentence_id word_count   sentiment
    ## 1:           1          4  0.00000000
    ## 2:           2          6 -0.04082483
    ## 3:           3         10 -0.36366193

So the first sentence is neutral, the second sentence is slightly
negative, and the third sentence is more negative. Now that we have a
better understanding of how sentiment analysis works, we can turn our
attention to determinent the sentiment of reviews of Newbery award
winners.

# Determining Sentiment for Reviews of Newbery Winners

Some sentiment analyses would look into how sentiment changes over time
or from sentence to sentence, but for now I am just going to do
something simple. For every book, I have combined book reviews from
several different people into one long text. I will just be computing
the sentiment scores for each sentence of that long text and then
average the individual sentiment scores to get a final sentiment score
for the book as a whole. Then we can order the books from most negative
to most positive. Perhaps in the future I might consider diving deeper,
but I think this by itself is an interesting thing to know for now.

One nice thing about sentiment analysis is I don’t have to worry so much
about cleaning up the text. In fact, removing stop words and other words
like I’ve done in the past with other analyses would skew the results
since the sentiment score account for the number of words in a sentence.
That means the analysis essentially amount to running the data through
the sentiment analyzer.

![](/images/sent-unnamed-chunk-4-1.png)<!-- -->

From the plot of book rank by average sentiment score we can see that
the book with the most negative average sentiment is The Slave Dancer,
while the one with the most positive average sentiment is Dobry. I have
to admit I was a little suspicious about that. I haven’t read Dobry, but
from what I have heard about it I didn’t expect it to be a stand out
winner. Indeed, after looking at the actual reviews I collected I saw
that people weren’t particularly impressed. People didn’t hate it, but
they didn’t love it. So why was it so far out in front?

<figure>

<img src="https://static0.srcdn.com/wordpress/wp-content/uploads/2019/07/Harry-Potter-Dobby-Feature.jpg" alt="Dobby" style="width:50%" class = "center">

<figcaption>

Everyone’s gotta have an origin story

</figcaption>

</figure>

When we look at the number of sentences for each book, we find that the
number of sentences for reviews of Dobry is 3-4 times smaller than that
of other books. This means that a few really positive sentences can help
Dobry more than they would other books. The great score for Dobry
doesn’t mean that people had a lot of great things to say about it, it
means they didn’t have much to say at all. After seeing this, I
remembered that this had been a particularly hard book to find reviews
for. I don’t think many people have read it, which in and of itself
should be telling. There’s a lesson in this though. Sometimes winning
isn’t about being good, it’s just about making sure other people attract
more attention than you do so that people notice their flaws more than
yours. That’s probably the moral of some fable, right?

Unfortunately, the best way to address this problem would be to try and
find more reviews for Dobry to get a better representation of how people
feel about the book. For the other books I was able to find anywhere
from eight to ten long reviews. For Dobry I could only find four or five
and they were shorter. Those four or five reviews just might have been
from the people who tended to like Dobry more than the average person.
It would be interesting to grab reviews from a website like goodreads
and try this again to get a better picture of how people feel about
these books.

<figure>

<img src="https://media.giphy.com/media/3ornk6UHtk276vLtkY/giphy.gif" alt="Caveat" style="width:50%" class = "center">

<figcaption>

Luke also couldn’t believe that Dobry ranked higher than Holes

</figcaption>

</figure>

<https://media.giphy.com/media/3ornk6UHtk276vLtkY/giphy.gif>

There are three final things to note. First, if you would like you can
see the scores for all of they are in a table at the end of the post.
Second, you’ll notice that the range of sentiment scores is pretty
small. When you are ranking things using some scoring method and the
scores are this close to each other, it means that the rank order isn’t
definitive. If I were to collect some more reviews and do this again
there’s a good chance that books with scores close to each other might
switch places. As I collect more reviews, the order would start to
solidify. However, this doesn’t mean that the ordering here isn’t
completely uninformative. You probably aren’t going to see The High King
and The Dark Frigate switch places, but you could very well see The High
King jump from 10th to 6th place or higher.

The last thing to note is that last place doesn’t mean the book is *bad*
it might just mean the reviews have a lot of negative words and phrases.
This is important to remember when you see books like The Slave Dancer
and Bridge to Terabithia in last place. They both deal with some pretty
heavy topics: slavery and kids dying. I think it would be hard to not
have some negative feelings about things like that.

# Conclusion

Hopefully this post has given you a better idea of how sentiment
analysis works and how it can be used to see how people feel about
Newbery award winning books. We can definitely see that people do have
more positive things to say about some books than they do others.
There’s a lot to unpack with these Newbery reviews data and I’ve
enjoyed what we’ve discovered so far\!

    ##                                                         Sentiment Number
    ## Dobry                                                 0.246273458      1
    ## Last Stop on Market Street                            0.203371579      2
    ## Joyful Noise: Poems for Two Voices                    0.199172945      3
    ## A Visit to William Blake's Inn                        0.195872588      4
    ## The Cat Who Went to Heaven                            0.192981693      5
    ## Invincible Louisa                                     0.162255468      6
    ## Good Masters! Sweet Ladies!                           0.161419282      7
    ## From the Mixed-Up Files of Mrs. Basil E. Frankweiler  0.153999000      8
    ## Waterless Mountain                                    0.151728500      9
    ## The High King                                         0.151203231     10
    ## Sarah, Plain and Tall                                 0.150299945     11
    ## Young Fu of the Upper Yangtze                         0.149717417     12
    ## Hitty, Her First Hundred Years                        0.148807324     13
    ## The Whipping Boy                                      0.147450756     14
    ## The One and Only Ivan                                 0.146479481     15
    ## Tales of Silver Lands                                 0.144360600     16
    ## Onion John                                            0.141831862     17
    ## A Single Shard                                        0.140472900     18
    ## The Story of Mankind                                  0.139287367     19
    ## The White Stag                                        0.134512667     20
    ## Thimble Summer                                        0.132368472     21
    ## It's Like This, Cat                                   0.132050136     22
    ## The Voyages of Doctor Dolittle                        0.131641527     23
    ## The Twenty-One Balloons                               0.128151307     24
    ## The Graveyard Book                                    0.125522092     25
    ## Caddie Woodlawn                                       0.124255192     26
    ## Criss Cross                                           0.120291491     27
    ## Flora and Ulysses                                     0.118490397     28
    ## The View from Saturday                                0.118117529     29
    ## I, Juan de Pareja                                     0.117789707     30
    ## Secret of the Andes                                   0.114244015     31
    ## The Door in the Wall                                  0.113815121     32
    ## A Gathering of Days                                   0.113738915     33
    ## Ginger Pye                                            0.112767299     34
    ## When You Reach Me                                     0.111811154     35
    ## Lincoln, a Photobiography                             0.110425680     36
    ## Merci Suarez Changes Gears                            0.108348754     37
    ## Julie of the Wolves                                   0.107222026     38
    ## A Wrinkle in Time                                     0.106780950     39
    ## Number the Stars                                      0.106389326     40
    ## Kira-Kira                                             0.105910163     41
    ## Shiloh                                                0.105585746     42
    ## King of the Wind                                      0.104076154     43
    ## Miracles on Maple Hill                                0.103265734     44
    ## The Tale of Despereaux                                0.101814233     45
    ## The Bronze Bow                                        0.100650820     46
    ## Amos Fortune Free Man                                 0.100499396     47
    ## Roller Skates                                         0.099181777     48
    ## Gay Neck, The Story of a Pigeon                       0.098747608     49
    ## Walk Two Moons                                        0.097706751     50
    ## Adam of the Road                                      0.095873622     51
    ## ...And Now Miguel                                     0.095112367     52
    ## Carry On, Mr. Bowditch                                0.094748992     53
    ## The Midwife's Apprentice                              0.093948129     54
    ## Moon Over Manifest                                    0.093387591     55
    ## The Hero and the Crown                                0.093075801     56
    ## Mrs. Frisby and the Rats of NIMH                      0.091424239     57
    ## Dicey's Song                                          0.090027055     58
    ## The Witch of Blackbird Pond                           0.088040574     59
    ## A Year Down Yonder                                    0.085820203     60
    ## The Westing Game                                      0.085485653     61
    ## The Higher Power of Lucky                             0.085255216     62
    ## Call It Courage                                       0.084785672     63
    ## Rabbit Hill                                           0.080790765     64
    ## Island of the Blue Dolphins                           0.080480606     65
    ## The Girl Who Drank the Moon                           0.078192628     66
    ## Hello, Universe                                       0.077302582     67
    ## Dear Mr. Henshaw                                      0.073104118     68
    ## The Crossover                                         0.064959250     69
    ## Sounder                                               0.062380639     70
    ## Holes                                                 0.061078772     71
    ## Smoky the Cowhorse                                    0.060607721     72
    ## Summer of the Swans                                   0.059994283     73
    ## Johnny Tremain                                        0.056165470     74
    ## Jacob Have I Loved                                    0.054642085     75
    ## The Trumpeter of Krakow                               0.053875758     76
    ## Shen of the Sea                                       0.053367240     77
    ## Shadow of a Bull                                      0.050009996     78
    ## The Giver                                             0.048642237     79
    ## M.C. Higgins the Great                                0.047778124     80
    ## The Grey King                                         0.047153151     81
    ## Out of the Dust                                       0.046507945     82
    ## Up a Road Slowly                                      0.045418086     83
    ## Maniac Magee                                          0.043728129     84
    ## The Wheel on the School                               0.043667840     85
    ## Bud, Not Buddy                                        0.037210708     86
    ## Rifles for Watie                                      0.023463589     87
    ## Daniel Boone                                          0.022247052     88
    ## The Matchlock Gun                                     0.021006750     89
    ## Dead End in Norvelt                                   0.020124810     90
    ## Crispin: The Cross of Lead                            0.016589576     91
    ## Missing May                                           0.013597596     92
    ## Strawberry Girl                                       0.007750052     93
    ## The Dark Frigate                                     -0.001584072     94
    ## Miss Hickory                                         -0.013359798     95
    ## Bridge to Terabithia                                 -0.013497337     96
    ## Roll of Thunder, Hear My Cry                         -0.025514887     97
    ## The Slave Dancer                                     -0.059366423     98
