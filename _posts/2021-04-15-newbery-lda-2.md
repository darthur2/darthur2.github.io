---
layout: post
title: "Understanding the Newbery with Latent Dirichlet Allocation (Part 2)"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
categories: analysis
tags: lda nlp
---

In my [previous post](%7B%%20post_url%202021-04-14-newbery-lda%20%%7D) I
explored reviews of books that have won the Newbery award using Latent
Dirichlet Allocation (LDA). In that post I only looked at one word
chunks (1-grams). In this post I am going to perform the same type of
analysis, but this time I am going to look at bigrams (these are two
word chunks). For this analysis I will be doing the following:

1.  Explain a little about bigrams.
2.  Use bigrams to get some more insights into topics covered in reviews
    of Newbery books using LDA.

For more context about where the data came from and what they look like,
you can look at my previous post about LDA. There you will also find a
quick overview of what LDA is and how it works.

# Bigrams

As I mentioned, in my last analysis I performed LDA using 1-grams. This
means that a sentence like “I have an annoying dog” would be broken up
into two words, “annoying” and “dog”, after removing the stop words “I”,
“have”, and “an”. However, sometimes one can gain more insight into what
is being said by looking at words grouped together as n-grams. For
example, the single bigram (2-gram) of the previous sentence would be,
“annoying dog”. Using 1-grams tells us that the person talks about
their dog and that something is annoying, but the bigram tells us that
it’s the dog that is annoying.

There are some caveats about using bigrams, the first of which simply
applies to text analysis and natural language processing in general. The
first caveat is that by removing stop words, it’s possible to lose some
context about what is being said. For example, the sentence “I don’t
have an annoying dog” would lead to the same 1-grams and the same bigram
after removing the stop words. This is something to keep in mind when
interpreting topics in LDA. We don’t know whether the dog likes or
dislikes their dog, however it seems unlikely that we would find the
phrase “annoying dog” a lot if everyone was saying how much they loved
their dog, but it’s important to remember that it could happen.

As a quick aside, I’ll say that one could try to avoid the ambiguity in
meaning by leaving in the stop words, but this creates other problems.
Using 1-grams you would find it harder to identify words that have more
meaning since the most popular words would now be those stop words
(which is why they removed in the first place). Using bigrams wouldn’t
really fix the problem for our example because then you would just have
the bigrams “I don’t”, “don’t have”, “have an”, “an annoying”, and
“annoying dog”. In this case since the word “don’t” is separated from
“annoying dog” we still wouldn’t be able to quickly infer what the
speaker means to say. We could try solving this by using larger chunks
of words, but this leads to the next problem I was going to mention.

Using higher order n-grams results in more sparsity. By this I just mean
that once you start grouping words together, you are less likely to find
lots of occurrences of those phrases which makes it difficult to find
out what is important and what isn’t. In our example, we are more likely
to find occurrences of the words “annoying” and “dog” separately than we
are to find them together. We are even less likely to find many
occurrences of the phrase “don’t have an annoying dog” than we are to
find occurrences of the bigram “annoying dog”. In general, the larger
the phrases, the more unique they become and the harder it becomes to
find meaningful topics in the text.

In most cases, people will probably look at 1-grams and bigrams. They
might look at trigrams (3-grams) if they have a lot of data. I am just
going to look at the bigrams. Using bigrams doesn’t really change
anything else about LDA. As far as LDA is concerned, “annoying dog” is
just another word.

# Discovering Topics in Newbery Reviews Using LDA with Bigrams

The first thing we are going to do now is remove the stop words and stem
the words as we did before, but this time we are going to create bigrams
instead of 1-grams. After creating the bigrams, I remove “unimportant”
bigrams. By this I mean I’m going to remove bigrams that don’t occur
very often. I’m choosing to only select the top 700 or so bigrams.
Finally, I fit an LDA with 6 topics again and select the top 10 bigrams
for each topic.

![](/images/lda2-unnamed-chunk-3-1.png)<!-- -->

As we can see, using bigrams gives us some insight into what is being
discussed in these reviews that we didn’t have when we were using
1-grams. Some bigrams are found in all topics. For example, most of the
topics include the bigram for historical fiction which for me makes
sense. We can also see that the idea of children growing up is a common
theme throughout books. The coming of age bigram is important for many
topics.

We can again look at which topics are the most popular for each book to
help us gain some additional insight into what these discovered topics
might be.

    ##  [1] "Smoky the Cowhorse"                 "Young Fu of the Upper Yangtze"     
    ##  [3] "Invincible Louisa"                  "The White Stag"                    
    ##  [5] "Johnny Tremain"                     "King of the Wind"                  
    ##  [7] "The Bronze Bow"                     "Shadow of a Bull"                  
    ##  [9] "The Hero and the Crown"             "Joyful Noise: Poems for Two Voices"
    ## [11] "Out of the Dust"                    "Hello, Universe"

By look at the books in topic 1 and reviewing the bigrams, I might say
that topic 1 should maybe just be labeled historical fiction.

    ##  [1] "The Voyages of Doctor Dolittle" "Waterless Mountain"            
    ##  [3] "Thimble Summer"                 "Amos Fortune Free Man"         
    ##  [5] "Secret of the Andes"            "Rifles for Watie"              
    ##  [7] "M.C. Higgins the Great"         "Dicey's Song"                  
    ##  [9] "Lincoln, a Photobiography"      "The Giver"                     
    ## [11] "The View from Saturday"         "The Higher Power of Lucky"     
    ## [13] "The One and Only Ivan"          "Flora and Ulysses"             
    ## [15] "Last Stop on Market Street"     "Merci Suarez Changes Gears"

I might label this topic fictional coming of age stories.

Here are the books for topic 3:

    ##  [1] "The Dark Frigate"                 "The Trumpeter of Krakow"         
    ##  [3] "The Cat Who Went to Heaven"       "Adam of the Road"                
    ##  [5] "Strawberry Girl"                  "Ginger Pye"                      
    ##  [7] "Carry On, Mr. Bowditch"           "A Wrinkle in Time"               
    ##  [9] "I, Juan de Pareja"                "Sounder"                         
    ## [11] "Mrs. Frisby and the Rats of NIMH" "Jacob Have I Loved"              
    ## [13] "A Visit to William Blake's Inn"   "The Midwife's Apprentice"        
    ## [15] "A Single Shard"                   "Kira-Kira"

Topic 4:

    ##  [1] "The Story of Mankind"                                
    ##  [2] "Tales of Silver Lands"                               
    ##  [3] "Shen of the Sea"                                     
    ##  [4] "Gay Neck, The Story of a Pigeon"                     
    ##  [5] "Caddie Woodlawn"                                     
    ##  [6] "Roller Skates"                                       
    ##  [7] "Daniel Boone"                                        
    ##  [8] "The Matchlock Gun"                                   
    ##  [9] "Rabbit Hill"                                         
    ## [10] "Island of the Blue Dolphins"                         
    ## [11] "It's Like This, Cat"                                 
    ## [12] "From the Mixed-Up Files of Mrs. Basil E. Frankweiler"
    ## [13] "Julie of the Wolves"                                 
    ## [14] "The Slave Dancer"                                    
    ## [15] "Sarah, Plain and Tall"                               
    ## [16] "The Whipping Boy"                                    
    ## [17] "Number the Stars"                                    
    ## [18] "Walk Two Moons"                                      
    ## [19] "Moon Over Manifest"

Topic 5:

    ##  [1] "Dobry"                       "Call It Courage"            
    ##  [3] "The Twenty-One Balloons"     "The Door in the Wall"       
    ##  [5] "The Wheel on the School"     "The Witch of Blackbird Pond"
    ##  [7] "Onion John"                  "Up a Road Slowly"           
    ##  [9] "The High King"               "Summer of the Swans"        
    ## [11] "A Gathering of Days"         "Maniac Magee"               
    ## [13] "Missing May"                 "Holes"                      
    ## [15] "Bud, Not Buddy"              "A Year Down Yonder"         
    ## [17] "Crispin: The Cross of Lead"  "The Tale of Despereaux"     
    ## [19] "Criss Cross"                 "Good Masters! Sweet Ladies!"
    ## [21] "The Crossover"               "The Girl Who Drank the Moon"

Topic 6:

    ##  [1] "Hitty, Her First Hundred Years" "Miss Hickory"                  
    ##  [3] "...And Now Miguel"              "Miracles on Maple Hill"        
    ##  [5] "The Grey King"                  "Roll of Thunder, Hear My Cry"  
    ##  [7] "Bridge to Terabithia"           "The Westing Game"              
    ##  [9] "Dear Mr. Henshaw"               "Shiloh"                        
    ## [11] "The Graveyard Book"             "When You Reach Me"             
    ## [13] "Dead End in Norvelt"

Here are the labels that I chose for the topics:

1.  Historical fiction
2.  Junior high coming of age
3.  Medieval and 19th century adventure
4.  Wars (Civil, Revolutionary, II) 5: Fictional family stories 6: Young
    teenager adventures

Again, these labels aren’t perfect and the caveat that this might not be
the optimal number of labels still applies. However, this does help us
to see how LDA with bigrams can give us some information that wasn’t
available before when we were using 1-grams.

# Conclusion

I hope I was able to show how LDA can be used with bigrams to model
topics discussed by reviewers of Newbery award winning books. It really
is the same as LDA, but the only difference is that words are grouped
together in groups of two and treated as single quantites in LDA.

From this and my previous post, it seems that Newbery winners share some
common ingredients. They deal with families and relationships, many
feature animals, many are historical fiction, many of the historical
fiction books are set during periods of war, and almost all of them deal
with kids or youth growing up (coming of age). I didn’t mention this
before but you’ll notice that the bigram “mother dies” is found in
several of the topics so death is apparently another common theme
discusses in these books.

Hope you enjoyed the post\! I look forward to doing a couple more posts
about this data set.
