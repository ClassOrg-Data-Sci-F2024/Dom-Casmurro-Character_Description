Progress report
================
LUIZA DIVINO
2024-10-28

# 1st Progress Report

Packages used so far: `tidyverse` `tidytext` `pdftools` `tibble`
`stringr` `dplyr` `tm`

I have managed to import the dataset as a PDF and transformed the data
into a data frame. I have 3 datasets: `dom_casmurro_pages_`, with 128
observations, each of giving access to the amount of text in each of the
128 pages; `dom_casmurro_words`, where each observation is a different
word, divided by page number; and `dom_casmurro_words_filtered`, where I
added a stop wordlist, which gives me easy access to content words.

I started by using `count()` to see what were the most common words, the
decided it would be valueble to have a list that did not include comon
words. Initially, I was trying to add a stop word list found on
<https://gist.github.com/alopes/5358189>. I then discovered that the
package `tm` has its own stopwords also for Portuguese, which makes the
coding process a bit easier.

I started working firstly with `Capitu`, trying to figure out how long
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
