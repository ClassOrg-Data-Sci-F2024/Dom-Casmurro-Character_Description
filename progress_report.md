Progress report
================
LUIZA DIVINO
2024-10-28

# 1st Progress Report

### October 28th

Packages used so far: `tidyverse` `tidytext` `pdftools` `tibble`
`stringr` `dplyr` `tm`

I have managed to import the dataset as a PDF and transformed the data
into a data frame. I have 3 datasets: `dom_casmurro_pages_`, with 128
observations, each of giving access to the amount of text in each of the
128 pages; `dom_casmurro_words`, with 66,927 observations, where each
observation is a different word, divided by page number; and
`dom_casmurro_words_filtered`, with 35,884, after “adding” a stop word
list, which gave me easy access to content words.

I started by using `count()` to see what were the most common words, the
decided it would be valuable to have a list that did not include common
words. Initially, I was trying to add a stop word list found on
<https://gist.github.com/alopes/5358189>. I then discovered that the
package `tm` has its own stopwords also for Portuguese, which makes the
coding process a bit easier.

I started working firstly with `capitu`, trying to figure out how long
the ngrams could be. I tried with different lengths, from 1 to 10 words
and I might work with 5. I had 3 different approaches: only right
context, only left context, and both right and left contexts together. I
noticed that, the bigger the length, the smaller the amount of
observations.

I found out about the package `lexiconPT`, designed to make Sentiment
Analysis with Brazilian Portuguese. The problem is there is not much
information about how to use it and the only way I found did not seem
very “automatic”

### Next steps

- Figure how to categorize the ngrams into different groups (try to find
  a pattern)

- Extract and analyse ngrams including the name Escobar

- The word `mãe` (=mom) also had a lot of occurrences: see if there is
  anything interesting with this term

- Discover how to use `lexiconPT` for Sentiment Analysis or find a
  different package to work with Portuguese

# 2nd Progress Report

### November 11th

At this stage, I did not progress much on what I had previously done
because I see the next step as the tagging process. I have been reading
about POS-Tagging and I discovered about `udpipe` (Wijffels 2021)
(<https://ladal.edu.au/tagging.html>) which, in theory, has a Brazilian
Portuguese as one of the options for language models to download. It
turns out it had only [European
Portuguese](https://github.com/ClassOrg-Data-Sci-F2024/Dom-Casmurro-Character_Description/blob/main/portuguese-gsd-ud-2.5-191206.udpipe)
and even though I was able to download it, I kept on getting error
messages when trying to use it. My goal was to use an R package to tag
the corpus but, if none of them work, I will download the
[TreeTagger](https://www.cis.uni-muenchen.de/~schmid/tools/TreeTagger/).
Even though the data is not yet tagged, I added csv files for both the
[pages
file](https://github.com/ClassOrg-Data-Sci-F2024/Dom-Casmurro-Character_Description/blob/main/data/data_pages.csv)
and also the [words
file](https://github.com/ClassOrg-Data-Sci-F2024/Dom-Casmurro-Character_Description/blob/main/data/data_words.csv).

#### License

Since this is a text that belongs to the public domain and I have no
restrictions on sharing the results I will obtain with this research, I
chose the MIT License that only requires preservation of copyright and
license notices. If this is helpful to anyone, I just want to be
acknowledged for it.

# 3rd Progress Report

I decide to separate my progress into [PART
1](https://github.com/luizasdivino/Dom-Casmurro-Character_Description/blob/main/PART%201.Rmd)
and [PART
2](https://github.com/luizasdivino/Dom-Casmurro-Character_Description/blob/main/PART%202.Rmd)
because I wanted to organize my progress. What I did differently in PART
2 was that I organized it into *Clean and tag corpus* and *Data
analysis*. In the *Clean and tag corpus* part I managed to put
everything in an order that made more sence: first flatten the pdf file
using `str_flatten()` and then tokenizing the tibble version of the data
using `unnest_tokens()`.

Differently from what I was doing before, I decided to use the package
`stopwords` because it seemed better for this context than the stop word
list available in the `tm` package. `stopwords` gave me three source
options. After testing all three, `nlkt` was the one which showed the
best result, so I decided to use it. After checking the word frequency
list, I added some more words to the list because I noticed some verbs
still remained there. I checked the list up to 50 occurrences because it
would affect the word cloud. I decided not to take “capítulo” (chapter)
out because the narrator uses it also inside the text, not only to name
each chapter.

For the corpus to be tagged, I chose the `portuguese-bosque` language
model because it is a Portuguese treebank that includes both Brazilian
and European Portuguese. To use this treebank, I had to install the
package called `udpipe`, where I downloaded the model I wanted by using
`udpipe_download_model()` and then `udpipe_annotate()` to tag the
corpus.

After doing so, in the section *Data analysis*, I started by testing the
stopword using `count()` to see if only content words had remained. I
then decided to make a wordcloud with `geom_text_wordcloud()`, available
in the package called `ggwordcloud` because I think it gives an
interesting visual description of what is in the dataset, which helped
me have an idea of one aspect I want to focus on the analysis. I want to
focus on changing the color of words in the wordcloud to make the
visuals more interesting.

One aspect that is very well known in this book is how the narrator
talks about Capitu’s eyes, and the word “olhos” (which means “eyes” in
English) seems to be really frequent comparing to other content words,
so I decided to check how this word co-occurs with Capitu and had some
interesting results. After taking some time to take a careful look on
the list of n-grams with “Capitu” and “olhos”, I decided to find out
which verbs come right after Capitu and now I think I finally found
where the changes during the story might be.

A sentiment analysis might help me to organize the results I had so far,
so my next step will probably be this one.

## Problems to solve

For some reason, I could not knit the rmd as with github output, it only
allowed me to knit it as an html. I have to understand why this happened
in order to fix the problem.
