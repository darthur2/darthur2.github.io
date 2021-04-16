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
blogs. In the post, I will do the following:

1.  Explain a little about sentiment analysis.
2.  Perform a sentiment analysis on the book reviews for Newbery
    winners.

I won’t be explaining much about where the data came from in this post
but you can go to my [first
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

We can read this and have a pretty good idea whether the sentiment is
negative or positive, but how would a computer do it? The first step
would be to assign words a polarity. The polarity of a word is a numeric
value that represents where that word falls on a scale of “very
negative” to “very positive”. For now we will say our scale goes from
-1 to 1. Most words, like “okay”, “I”, and “long”, would be neutral and
therefore have a polarity of 0. Other words like “interesting” or
“like”, by themselves, would be positive and might have a polarity
of 0.75 and 0.5 respectively. If we would at the polarity of each word
by itself, we would say that these sentences have a lot of positive
words. However, we know that saying the sentiment here is positive would
be a mistake, so polarity by itself isn’t enough to judge sentiment.

This is where the other two concepts of valence shifting and
amplification come into play. Amplifiers are words that enhance the
polarity of nearby words. In our sentences, the words “really” and
“very” are amplifiers and they enhance the words “like” and
“interesting”. Valence shifters are words that reverse the polarity of
a surrounding word. In our sentences, words like “didn’t” and “weren’t”
would be considered valence shifters. By adding the word “really” to
“like” the sentiment might increase from 0.5 to 0.65 and then by
augmenting the phrase “really like” with “didn’t” we would flip the
polarity to -0.65. By doing this with our text, a computer would begin
to recognize that the sentiment here is negative, not positive.

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

![](/images/lda2-unnamed-chunk-4-1.png)<!-- -->

From the plot of
