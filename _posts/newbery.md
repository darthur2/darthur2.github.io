---
layout: post
title: "What's in a Newbery?"
output:
  md_document:
    variant: markdown_github
    preserve_yaml: true
---

    ## Warning: package 'openxlsx' was built under R version 4.0.5

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.4     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.0

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## Warning: package 'wordcloud' was built under R version 4.0.5

    ## Loading required package: RColorBrewer

    ## Warning: package 'tidytext' was built under R version 4.0.4

    ## Warning: package 'topicmodels' was built under R version 4.0.5

    ## Warning: package 'ldatuning' was built under R version 4.0.5

    ## 
    ## Attaching package: 'gridExtra'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     combine

    ## Warning: package 'textdata' was built under R version 4.0.5

Cleaning Data
=============

``` r
allReviews <- gsub("'", "'", reviews$Reviews)
authorNames <- tokenize_words(reviews$Author)
bookTitles <- tokenize_words(reviews$Book)

myStopWords <- c("newbery", "award", "winner", "medal", "book", "chapter",
                 "story", "stories", "books", "awards", "winners",
                 "novel", "newberry", "page", 
                 stopwords("en"), letters, unlist(authorNames),
                 unlist(bookTitles))

cleanedReviews <- lapply(1:nrow(reviews), function(i)
  tokenize_words(allReviews[i], stopwords = myStopWords) %>% `[[`(1))

cleanedReviews <- sapply(1:nrow(reviews), function(i)
  paste(cleanedReviews[[i]], collapse = " "))

cleanedReviews <- gsub("'s", "", cleanedReviews)

cleanedWords <- sapply(1:nrow(reviews), function(i)
  tokenize_words(cleanedReviews[i], stopwords = myStopWords) %>% `[[`(1))
```

Document Term Matrix, TF-IDF, and Word Clouds
=============================================

``` r
uniqueWords <- unlist(cleanedWords) %>% unique %>% sort
docTermMat <- matrix(0, nrow(reviews), length(uniqueWords))

for (i in 1:nrow(reviews)){
  curDocTermRow <- table(cleanedWords[[i]])
  wordInd <- uniqueWords %in% names(curDocTermRow)
  docTermMat[i,wordInd] <- curDocTermRow
}

colnames(docTermMat) <- uniqueWords
rownames(docTermMat) <- reviews$Book

tf <- t(apply(0.5 + 0.5*docTermMat, 1, function(i) i/max(i)))
idf <- matrix(log(nrow(reviews)/apply(docTermMat, 2, function(i) sum(i != 0))), 
              nrow = nrow(reviews), ncol = length(uniqueWords), byrow = TRUE)
tfidf <- tf*idf

bottom <- apply(tfidf, 1, function(i) quantile(i, 0.0006))
top <- apply(tfidf, 1, function(i) quantile(i, 0.9994))

keepIndMat <- matrix(FALSE, nrow(reviews), ncol(tfidf))

for (i in 1:nrow(reviews)){
  keepIndMat[i,] <- tfidf[i,] >= bottom[i] & tfidf[i,] <= top[i]
}

keepIndVec <- colSums(keepIndMat) == nrow(reviews)

removeWords <- uniqueWords[!keepIndVec]

reduceWords <- sapply(1:nrow(reviews), function(i) 
  paste(cleanedWords[[i]], collapse = " ")) %>% 
  tokenize_words(stopwords = removeWords)

reduceTable <- table(unlist(reduceWords))
reduceTable <- reduceTable[order(reduceTable, decreasing = TRUE)]
reduceNames <- names(reduceTable)
reduceCounts <- unname(reduceTable)

reduceBigrams <- tokenize_ngrams(paste(unlist(reduceWords), collapse = " "),
                                 n = 2) %>% `[[`(1)
reduceBigramTable <- table(reduceBigrams)
reduceBigramTable <- reduceBigramTable[order(reduceBigramTable, 
                                             decreasing = TRUE)]
reduceBigramNames <- names(reduceBigramTable)
reduceBigramCounts <- unname(reduceBigramTable)

set.seed(150)

wordcloud(reduceNames[1:300], reduceCounts[1:300], 
          scale = c(3, 0.1), colors = brewer.pal(8, "Dark2"))
```

![](/images/unnamed-chunk-3-1.png)

``` r
set.seed(160)

wordcloud(reduceBigramNames[1:200], reduceBigramCounts[1:200], 
          scale = c(3, 0.1), colors = brewer.pal(8, "Dark2"))
```

![](/images/unnamed-chunk-3-2.png)

Topic Analysis
==============

``` r
uniqueWords2 <- unlist(reduceWords) %>% unique %>% sort
docTermMat2 <- matrix(0, nrow(reviews), length(uniqueWords2))

for (i in 1:nrow(reviews)){
  curDocTermRow <- table(reduceWords[[i]])
  wordInd <- uniqueWords2 %in% names(curDocTermRow)
  docTermMat2[i,wordInd] <- curDocTermRow
}

colnames(docTermMat2) <- uniqueWords2
rownames(docTermMat2) <- reviews$Book

freqDocWords <- (colSums(docTermMat2 > 1) >= nrow(reviews)/2) %>% unname
rareWords <- (colSums(docTermMat2 <= 2) == nrow(reviews)) %>% unname
keepIndVec2 <- (freqDocWords | rareWords)

removeWords2 <- uniqueWords2[keepIndVec2]

reduceWords2 <- sapply(1:nrow(reviews), function(i) 
  paste(reduceWords[[i]], collapse = " ")) %>% 
  tokenize_word_stems(stopwords = removeWords2)

reviewsDF <- data.frame(book = reviews$Book, 
                         review = sapply(1:nrow(reviews), function(i) 
                           paste(reduceWords2[[i]], collapse = " ")), 
                         stringsAsFactors = FALSE)

reviewsTDF <- unnest_tokens(reviewsDF, output = phrase, input = review)

reviewsCount <- reviewsTDF %>% count(book, phrase, sort = TRUE) %>% ungroup

reviewsDTM <- reviewsCount %>% cast_dtm(book, phrase, n)

numTopicCV <- FindTopicsNumber(
  reviewsDTM,
  topics = seq(from = 2, to = 15, by = 1),
  metrics = c("Griffiths2004", "CaoJuan2009", "Arun2010", "Deveaud2014"),
  method = "Gibbs",
  control = list(seed = 77),
  mc.cores = 4L,
  verbose = TRUE
)
```

    ## fit models... done.
    ## calculate metrics:
    ##   Griffiths2004... done.
    ##   CaoJuan2009... done.
    ##   Arun2010... done.
    ##   Deveaud2014... done.

``` r
FindTopicsNumber_plot(numTopicCV)
```

![](/images/unnamed-chunk-4-1.png)

``` r
reviewsLDA <- LDA(reviewsDTM, k = 9, control = list(seed = 1234))

reviewsTopics <- tidy(reviewsLDA, matrix = "beta")

topTerms <- reviewsTopics %>%
  group_by(topic) %>%
  top_n(10, beta) %>%
  ungroup() %>%
  arrange(topic, -beta)

topTerms %>% mutate(term = reorder_within(term, beta, topic)) %>%
  ggplot(aes(term, beta, fill = factor(topic))) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~ topic, scales = "free") +
  coord_flip() +
  scale_x_reordered()
```

![](/images/unnamed-chunk-4-2.png)

``` r
topicRanks <- t(get_topics(reviewsLDA, 2))

topicMat <- data.frame(document = rownames(topicRanks),
                       first = topicRanks[,1],
                       second = topicRanks[,2],
                       stringsAsFactors = FALSE)

docYearMat <- data.frame(document = reviews$Book,
                         year = 1922:(1922+nrow(reviews)-1),
                         stringsAsFactors = FALSE)

topicNames <- c("Growth, Learning, and Relationships",
                "Illustrations and Poetry",
                "Historical Fiction and Adventure",
                "Fantasy and Adventure",
                "Culture and Learning",
                "Culture, Survival, and Adventure",
                "Small Towns and Discovery",
                "Historical Fiction and Learning",
                "Growing Up and Small Towns")

topicYearMat <- left_join(topicMat, docYearMat) %>% arrange(year)
```

    ## Joining, by = "document"

``` r
topicData <- data.frame(Year = topicYearMat$year,
                        Topic = topicNames[topicYearMat$first],
                        Name = reviews$Book)

grid.arrange(
  tableGrob(topicData[1:25,], rows = NULL, 
            theme = ttheme_default(base_size = 10)),
  nrow = 1
)
```

![](/images/unnamed-chunk-4-3.png)

``` r
grid.arrange(
  tableGrob(topicData[26:50,], rows = NULL, 
            theme = ttheme_default(base_size = 10)),
  nrow = 1
)
```

![](/images/unnamed-chunk-4-4.png)

``` r
grid.arrange(
  tableGrob(topicData[51:75,], rows = NULL, 
            theme = ttheme_default(base_size = 10)),
  nrow = 1
)
```

![](/images/unnamed-chunk-4-5.png)

``` r
grid.arrange(
  tableGrob(topicData[76:98,], rows = NULL, 
            theme = ttheme_default(base_size = 10)),
  nrow = 1
)
```

![](/images/unnamed-chunk-4-6.png)

Sentiment Analysis
==================

``` r
sentRating <- rep(0, nrow(reviews))
sentDict <- get_sentiments("afinn")
posWords <- sentDict %>% filter(value > 0) %>% 
  select(word, value) %>% as.data.frame
negWords <- sentDict %>% filter(value < 0) %>%
  select(word, value) %>% as.data.frame

for (i in 1:nrow(reviews)){
  curWords <- reduceWords[[i]]
  curPosWords <- posWords[posWords$word %in% curWords,]
  curNegWords <- negWords[negWords$word %in% curWords,]
  posScore <- sum(curPosWords$value)
  negScore <- sum(curNegWords$value)
  sentRating[i] <- posScore + negScore
}

sentData <- data.frame(Sentiment = sentRating[order(sentRating)],
                       Number = 1:nrow(reviews))

rownames(sentData) <- reviews$Book[order(sentRating)]

ggplot(sentData, aes(x = Sentiment, y = Number)) +
  geom_point(alpha = 0) + 
  geom_text(label = rownames(sentData), check_overlap = TRUE, size = 4) +
  xlab("Sentiment Rating") + ylab("Book Number") + 
  xlim(-60, 160)
```

![](/images/unnamed-chunk-5-1.png)

``` r
sentByYear <- data.frame(Sentiment = sentRating,
                         Year = topicYearMat$year)

set.seed(1650)

ggplot(sentByYear, aes(x = Year, y = Sentiment)) + 
  geom_line() +
  ggtitle("Sentiment Over the Years") +
  theme(plot.title = element_text(hjust = 0.5)) +
  geom_text(aes(label = ifelse(Sentiment > 120 | Sentiment < -30,
                               topicYearMat$document, '')),
            hjust = 0.8, vjust = 0, 
            position = position_jitter(height = 6, width = 6))
```

![](/images/unnamed-chunk-5-2.png)

Sentiment-Topic Relationship
============================

``` r
sentMat <- data.frame(document = rownames(sentData),
                      rating = sentRating,
                      stringsAsFactors = FALSE)

topicData <- left_join(topicMat, sentMat)
```

    ## Joining, by = "document"

``` r
topicMeans <- sort(round(tapply(topicData$rating, 
                                topicData$first, mean), 2),
                   decreasing = TRUE)
names(topicMeans) <- topicNames
topicMeans <- data.frame("Mean Sentiment" = topicMeans)

grid.newpage()
grid.table(topicMeans)
```

![](/images/unnamed-chunk-6-1.png)
