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
