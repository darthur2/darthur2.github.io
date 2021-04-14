---
layout: post
title: "Understanding the Newbery with Latent Dirichlet Allocation"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
categories: analysis
---

Not too long ago my brother and sister-in-law asked an interesting
question: What makes a Newbery award winner? There are obviously a lot
of ways to try and answer this question, but I thought it would be
interesting to look into online reviews of Newbery books and see if
there are any interesting themes that continue to pop up in these books.
I thought a great way to do this would be with Latent Dirichlet
Allocation (LDA) which is something I had heard of, but had never used
before.

In this blog post, I will walk through the following:

1.  Explain a little about the Newbery award.
2.  Talk about where my data come from.
3.  Give a little background on LDA.
4.  Use LDA to understand important themes in Newbery award winners.

Let’s dive in\!

# The Newbery Award

According to Wikipedia, The [Newbery
Award](https://en.wikipedia.org/wiki/Newbery_Medal) is a literary award
given to an author for their “…distinguished contribution to American
literature for children”. The award was first given in 1922 and has been
given out every year since then. It’s not completely clear to me what
makes a book a Newbery award winner. One would think that a book would
win the award because children absolutely love it, but if you look at
the list I think you’re bound to find some books that even the most
ambitious young readers would hate. With other books it seems that
perhaps it was selected because adults like what it teaches children,
but after you sift through enough reviews it becomes clear that not even
adults like some of these books.

<figure>

<img src="https://media.giphy.com/media/KSKvdT1YGCpUIonvSq/giphy.gif" alt="I Tried" style="width:50%" class = "center">

<figcaption>

When you ask someone if they liked Smoky the Cow Horse

</figcaption>

</figure>

This is part of the reason that I thought that this analysis would be
interesting. I was curious to discover if there any common themes share
by Newbery winners.

# The Data

Because I didn’t have access to the text of each book, I went with the
next best thing I could think of and searched for reviews of the books.
Ideally I would have liked to get a large collection of detailed
professional reviews for each book and it would have been nice if these
reviews had been written in the same time period that the book was
written. This information would have provided insights into why the book
was chosen at the time it was chosen. Tastes change over time and
because the award has been around for so long, it seems obvious that
reviewers today might not understand a book in the same way that someone
living in 1930 would

However, finding reviews like this was difficult either because there
weren’t many or because they weren’t publicly available. For example, I
tried scraping some reviews from book review websites but some of them
didn’t like that so I instead of trying harder I just let it go. Even I
had kept trying, it probably wouldn’t have helped much because many of
these reviews were the same across different websites and weren’t very
detailed which was a problem because I wanted a lot of text for each
book for this analysis.

<figure>

<img src="https://media.giphy.com/media/ac7MA7r5IMYda/giphy.gif" alt="Get Out" style="width:50%">

<figcaption>

This was pretty much how Amazon responded to me the second I started
scraping book descriptions

</figcaption>

</figure>

In the end I spent a little time each day searching online reviews for
each book and copying the reviews over into an Excel document. In the
future, I would like to come up with a more robust data collection
method, but this worked for what I wanted to do with this analysis. Here
is what the review data look like for the first three Newbery winners:

    ## [1] "Winner of the first John Newbery Medal, this renowned classic is now updated for the millennium. Hendrik Willem van Loon's ability to convey history as a fascinating tale of adventure has endeared this book to countless readers. \"The Story of Mankind seems to be used as a core or spine world history by an increasing number of homeschoolers, particularly those looking for a history written from something other than a Protestant perspective. While the author was himself a Protestant, this history "      
    ## [2] "The delightfully eccentric Doctor Dolittle, rendered immortal on screen by the gifted Rex Harrison, has remained a firm favorite with generations of children ever since he made his debut in an earlier novel, The Story of Doctor Dolittle.In his second outing titledThe Voyages of Doctor Dolittle, the maverick physician takes on a new assistant, Tommy Stubbins. The story is structured as a first person account given by Tommy, who is now a very old man. The boy who was the son of the village cobbler"       
    ## [3] "In seventeenth century England, a terrible accident forces orphaned Philip Marsham to flee London in fear for his life. Bred to the sea, he signs on with the \"Rose of Devon,\" a dark frigate bound for the quiet shores of Newfoundland.\nPhilip's bold spirit and knowledge of the sea soon win him his captain's regard. But when the \"Rose of Devon\" is seized in midocean by a devious group of men plucked from a floating wreck, Philip is forced to accompany these \"gentlemen of fortune\" on their murderous"

# Latent Dirichlet Allocation (LDA)

Before jumping into the analysis using the data that’s been collected,
it would be helpful to give a little overview of how Latent Dirichlet
Allocation (LDA) works.

There are three important terms used in LDA: Documents, topics, and
words. In this case, a single document consists of all of the reviews
collected for a given book. So in this case there are 98 documents (I
only collected reviews for 1922 to 2019). Each document is made up of a
collection of topics which in turn are made up of collections of words.
The goal of LDA is to try and discover which topics make up a document
and which words belong to a topic.

For example, let’s say we are examining tweets from the Avengers and we
treated all tweets from a given Avenger as a single document. If we
performed LDA with these tweets assuming their were four major topics,
and then grabbed the top 3 words for each discovered topic, the output
of LDA might look like this:

From here we could try to infer what the topics are and give them names.
For example, we might say that the first topic is American Patriotism,
the next is Fighting, then Nerd, and finally Bad Guys. You’ll notice
that it’s possible for a word to be associated with more than one topic.
In this case this would just mean that no matter what they are talking
about, Avengers are all about justice.

It would also be possible to see which topics are the most popular for
different Avengers. For example, if you look at the topic distribution
for Steve Rogers’ tweets you might find them to be 85% Patriotism and
15% Fighting. Peter Parker’s tweets on the other hand might be 20%
Patriotism, 30% Fighting, 50% Nerd, and 10% Bad Guys.

With that example in mind, we can turn our attention to the analysis at
hand:

# Understanding the Newbery with LDA

Some pre-processing of the text was required before it could be used
with LDA. I performed some routine cleaning such as removing
punctuation, common stop words (e.g. I, me, them, etc.), and words that
are very specific to reviews of Newbery books in general (e.g. book,
chapter, newbery, etc.). Also, since I’m trying to identify general
themes in the reviews of Newbery books, I’m not going to focus on words
that aren’t mentioned very often or aren’t mentioned in very many books.
I’m only going to select words that are mentioned at least 20 times and
that appear in at least 10 of the books. After doing this, the text for
the reviews for the first three books now looked like this:

One problem is that before using LDA, we need to specify how many
underlying topics there are, which is something we don’t know. For now,
we are just going to choose a number of topics and see what we can
discover. There are different methods that have been proposed for
finding the number of topics, but that might be for another post. We
will fit an LDA model with 6 topics.

After fitting the LDA model with 6 topics, we can examine the top 15
words for each topic and see if we can identify any themes that help us
give names to the topics.

One thing that is apparent is that there are some words that are common
in each topic. Most of these words are related to families, community,
and children. This would suggest that in each book, no matter what
topics the book covers, families, kids, and relationships are going to
play an integral part in the book. This makes sense for reviews of books
that are winning the Newbery award.

We can also look at the individual documents (books) and see which
topics are most prevalent in those books. This would help us further as
we try and give labels to the topics. For example, the books for which
topic 1 is most prevalent are:

These books are adventure books that feature boys and are often set at
various places around the world. This makes sense since some of the top
words for this topic were boy, world, village, learn, and setting.

We can do the same thing for topic 2:

These are all books that might be considered magical and many of them
have a female lead. This might be why some of the top words for this
topic are girl, witch, and magic.

Here are the groups of books for topics 3, 4, 5, and 6:

We can do this with the other topics as well. I did this and these are
the titles that I came up for the topics:

1.  Boy adventures around the world
2.  Girls and magic
3.  Relationships among different cultures
4.  Kids navigating family relationships
5.  Animals and people
6.  Historical and medieval fantasy

These are by no means the “correct” names for these topics. It’s also
good to remember that a book isn’t always represented completely by one
topic. For example, the Grey King was around 26% topic 2, 65% topic 6
and the rest is split equally among the other topics. That makes
intuitive sense since it deals with medieval fantasy, girls, and magic.

It’s also good to remember that this is just our interpretation when
there are six topics. As I mentioned before, part of topic modeling is
trying to decide how many topics there are. There are different
techniques and methods that have been developed for this and perhaps I
will explore them in a different post.
