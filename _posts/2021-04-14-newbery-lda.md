---
layout: post
title: "Understanding the Newbery with Latent Dirichlet Allocation?"
output:
  md_document:
    variant: markdown_github
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

Let’s dive in!

The Newbery Award
=================

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

This is part of the reason that I thought that this analysis would be
interesting. I was curious to discover if there any common themes share
by Newbery winners.

The Data
========

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
didn’t like that so I instead of trying harder I just let it go. That
probably wouldn’t have worked out very well because many of these
reviews were the same across different websites and weren’t very
detailed and I wanted a lot of text for each book for this analysis.

<figure>
<img src="https://media.giphy.com/media/ac7MA7r5IMYda/giphy.gif" alt="Get Out" style="width:50%">
<figcaption>
This was pretty much how Amazon responded to me the second I started
scraping book descriptions
</figcaption>
</figure>

In the end I spent a little time each day searching online reviews
