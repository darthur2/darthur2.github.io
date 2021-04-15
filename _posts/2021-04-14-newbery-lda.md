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
interesting. I was curious to discover if there any common themes are
shared by Newbery winners.

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
living in 1930 would.

However, finding reviews like this was difficult; either because there
weren’t many or because they weren’t publicly available. For example, I
tried scraping some reviews from book review websites but some of them
didn’t like that so instead of trying harder I just let it go. Even if I
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
mixture of topics which in turn are made up of mixtures of words. The
goal of LDA is to try and discover which topics make up a document and
which words belong to a topic.

For example, let’s say we are examining tweets from the Avengers and we
treated all tweets from a given Avenger as a single document. If we
performed LDA with these tweets, assuming there were four major topics,
and then grabbed the top 3 words for each discovered topic, the output
of LDA might look like this:

From here we could try to infer what the topics are and give them names.
For example, we might say that the first topic is American Patriotism,
the next are Fighting, then Nerd Stuff, and finally Bad Guys. You’ll
notice that it’s possible for a word to be associated with more than one
topic. In this case this would just mean that no matter what they are
talking about, Avengers are all about justice.

It would also be possible to see which topics are the most popular for
different Avengers. For example, if you look at the topic distribution
for Steve Rogers’ tweets you might find them to be 85% Patriotism and
15% Fighting. Peter Parker’s tweets on the other hand might be 20%
Patriotism, 30% Fighting, 50% Nerd, and 10% Bad Guys.

With that example in mind, we can turn our attention to the analysis at
hand.

# Understanding the Newbery with LDA

Some pre-processing of the text was required before it could be used
with LDA. I performed some routine cleaning such as removing
punctuation, common stop words (e.g. I, me, them, etc.), and words that
are very specific to reviews of Newbery books in general (e.g. book,
chapter, newbery, etc.). Also, since I’m trying to identify general
themes in the reviews of Newbery books, I’m not going to focus on words
that either aren’t mentioned very often or aren’t mentioned in very many
books. I’m only going to select words that are mentioned at least 20
times and that appear for at least 10 of the books. After doing this,
the text for the reviews for the first three books now looked like this:

    ## [1] "winner medal classic loon's ability history fascinating tale adventure readers world history history written perspective author history reads written true chapters loon's death originally published times begins development actual history begins perspective continues time time treated style writing aspect author audience directly telling attitude lived begins ancient time families business forever running discuss writing style history written children grade level aloud parents references children"
    ## [2] "eccentric doctor favorite children earlier doctor dolittle.in doctor takes person account boy son village meets doctor takes hurt doctor treatment doctor quickly friends boy learns animals remarkable talking amazing creatures previous sequel mysterious friend doctor's called sets train strange events doctor exciting native american son living world.the quirky animal doctor plot approach world desire concern memorable adventure strong appeal compassion share south american discover giant sea wond"
    ## [3] "century england terrible accident forces fear life sea dark quiet philip's spirit knowledge sea win captain's forced fortune return england battles hero tale search adventure century england terrible accident forces fear life sea dark quiet philip's spirit knowledge sea win captain's forced fortune return england battles hero tale search adventure twenty difficult reads found difficult adult imagine crazy son 6y0m main boy named mother died father lost sea pages staying father's accident leads r"

With the text in the form, we are ready to perform LDA. However, one
problem is that before using LDA, we need to specify how many underlying
topics there are, which is something we don’t know. For now, we are just
going to choose a number of topics and see what we can discover. There
are different methods that have been proposed for finding the number of
topics, but that might be for another post. We will fit an LDA model
with 6 topics.

After fitting the LDA model with 6 topics, we can examine the top 15
words for each topic and see if we can identify any themes that help us
give names to the topics.

![](/images/unnamed-chunk-5-1.png)<!-- -->

One thing that is apparent is that there are some words that are related
to each topic. Most of these words are related to family, community, and
children. This would suggest that in each book, no matter what topics
the reviewer covers in their review, they are going to discuss families,
kids, and relationships. This makes sense for reviews of books that are
winning the Newbery award.

We can also group books together based on which topic is most strongly
represented in peoples’ reviews of that book. This would help us further
as we try and give labels to the topics. For example, the books for
which topic 1 is discussed the most in reviews are:

    ##  [1] "Young Fu of the Upper Yangtze"                       
    ##  [2] "Dobry"                                               
    ##  [3] "Secret of the Andes"                                 
    ##  [4] "From the Mixed-Up Files of Mrs. Basil E. Frankweiler"
    ##  [5] "The Westing Game"                                    
    ##  [6] "A Visit to William Blake's Inn"                      
    ##  [7] "The Whipping Boy"                                    
    ##  [8] "Joyful Noise: Poems for Two Voices"                  
    ##  [9] "The Midwife's Apprentice"                            
    ## [10] "Holes"                                               
    ## [11] "A Single Shard"                                      
    ## [12] "Crispin: The Cross of Lead"                          
    ## [13] "The Tale of Despereaux"                              
    ## [14] "Criss Cross"                                         
    ## [15] "Good Masters! Sweet Ladies!"                         
    ## [16] "When You Reach Me"                                   
    ## [17] "Dead End in Norvelt"                                 
    ## [18] "Last Stop on Market Street"

Notice that some of these words have been stemmed which means words like
village and villages will be converted to villag. These books are
adventure books that feature boys and are often set at various places
around the world. This makes sense since some of the top words for this
topic are “boy”, “world”, “village”, “learn”, and “setting”.

We can do the same thing for topic 2:

    ##  [1] "Tales of Silver Lands"       "The Witch of Blackbird Pond"
    ##  [3] "A Wrinkle in Time"           "Up a Road Slowly"           
    ##  [5] "Summer of the Swans"         "Bridge to Terabithia"       
    ##  [7] "The Hero and the Crown"      "Number the Stars"           
    ##  [9] "Missing May"                 "Walk Two Moons"             
    ## [11] "The Graveyard Book"          "Moon Over Manifest"         
    ## [13] "Flora and Ulysses"           "The Girl Who Drank the Moon"

These are all books that might be considered magical and many of them
have a female lead. This might be why some of the top words for this
topic are “girl”, “witch”, and “magic”.

Here are the groups of books for topics 3, 4, 5, and 6:

    ## [1] "Waterless Mountain"          "Call It Courage"            
    ## [3] "The Twenty-One Balloons"     "Island of the Blue Dolphins"
    ## [5] "Julie of the Wolves"         "The Giver"

    ##  [1] "Invincible Louisa"            "Caddie Woodlawn"             
    ##  [3] "Thimble Summer"               "Strawberry Girl"             
    ##  [5] "...And Now Miguel"            "The Wheel on the School"     
    ##  [7] "Onion John"                   "Sounder"                     
    ##  [9] "M.C. Higgins the Great"       "Roll of Thunder, Hear My Cry"
    ## [11] "A Gathering of Days"          "Jacob Have I Loved"          
    ## [13] "Dicey's Song"                 "Dear Mr. Henshaw"            
    ## [15] "Sarah, Plain and Tall"        "Maniac Magee"                
    ## [17] "The View from Saturday"       "Out of the Dust"             
    ## [19] "Bud, Not Buddy"               "A Year Down Yonder"          
    ## [21] "Kira-Kira"                    "The Higher Power of Lucky"   
    ## [23] "The Crossover"                "Hello, Universe"             
    ## [25] "Merci Suarez Changes Gears"

    ##  [1] "The Voyages of Doctor Dolittle"   "The Cat Who Went to Heaven"      
    ##  [3] "Roller Skates"                    "Adam of the Road"                
    ##  [5] "Rabbit Hill"                      "Miss Hickory"                    
    ##  [7] "The Door in the Wall"             "Ginger Pye"                      
    ##  [9] "Miracles on Maple Hill"           "It's Like This, Cat"             
    ## [11] "Mrs. Frisby and the Rats of NIMH" "Shiloh"                          
    ## [13] "The One and Only Ivan"

``` r
reviews$Book[max_doc_topics == 6]
```

    ##  [1] "The Story of Mankind"            "The Dark Frigate"               
    ##  [3] "Shen of the Sea"                 "Smoky the Cowhorse"             
    ##  [5] "Gay Neck, The Story of a Pigeon" "The Trumpeter of Krakow"        
    ##  [7] "Hitty, Her First Hundred Years"  "The White Stag"                 
    ##  [9] "Daniel Boone"                    "The Matchlock Gun"              
    ## [11] "Johnny Tremain"                  "King of the Wind"               
    ## [13] "Amos Fortune Free Man"           "Carry On, Mr. Bowditch"         
    ## [15] "Rifles for Watie"                "The Bronze Bow"                 
    ## [17] "Shadow of a Bull"                "I, Juan de Pareja"              
    ## [19] "The High King"                   "The Slave Dancer"               
    ## [21] "The Grey King"                   "Lincoln, a Photobiography"

We can do this with the other topics as well. I did this and these are
the titles that I came up for the topics discovered with LDA:

1.  Boy adventures around the world
2.  Girls and magic
3.  Relationships among different cultures
4.  Kids navigating family relationships
5.  Animals and people
6.  Historical and medieval fantasy

These are by no means the “correct” names for these topics. It’s also
good to remember that a book isn’t always represented completely by one
topic. For example, the Grey King was around 26% topic 2 and 65% topic 6
while the rest is split equally among the other topics. That makes
intuitive sense since it deals with medieval fantasy, girls, and magic.

It’s also good to remember that this is just my interpretation when
there are six topics. As I mentioned before, part of topic modeling is
trying to decide how many topics there are. There are different
techniques and methods that have been developed for this and perhaps I
will explore them in a different post.

# Conclusion

So they are you have it\! That’s just a little dive into Latent
Dirichlet Allocation and how it can be used for model topics discussed
in reviews of Newbery winners. There’s a lot more that could be done
with these data. Not only could we dive into methods for finding the
optimal number of topics as I mentioned previously, but we could look at
LDA with bi-grams (two words together instead of words by themselves),
sentiment analysis, word clouds, and more. Perhaps I will touch on those
in future posts, but that’s it for now.
