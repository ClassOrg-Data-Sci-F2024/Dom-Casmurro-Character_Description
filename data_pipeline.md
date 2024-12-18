Data Pipeline
================
LUIZA DIVINO
2024-12-16

- [Project details](#project-details)
- [Raw data overview](#raw-data-overview)
- [Uploading data and using a
  POS-tagger](#uploading-data-and-using-a-pos-tagger)
- [Transformed data overview](#transformed-data-overview)
- [Selecting which grammatical categories will be included for the
  content word
  count](#selecting-which-grammatical-categories-will-be-included-for-the-content-word-count)
- [Content word analysis](#content-word-analysis)
- [Sentiment Analysis](#sentiment-analysis)
- [Words near Capitu](#words-near-capitu)
- [Words right after and right before
  “Capitu”](#words-right-after-and-right-before-capitu)

# Project details

This project is on the book *Dom Casmurro* by Machado de Assis
(1839-1908), a Brazilian author. It is narrated by Bento Santiago, or
**Bentinho**, an older man who reflects on his life, particularly his
relationship with **Capitu**, a childhood friend who becomes his wife.
Bentinho recounts his deep love for Capitu, but the story takes a darker
turn as he becomes consumed by jealousy and suspicion that Capitu had an
affair with his best friend, **Escobar**. He believes Escobar to be the
father of his son, **Ezekiel**, due to the boy’s physical resemblance to
Escobar. However, the narrative is marked by Bentinho’s unreliable
perspective, leaving readers to question whether his suspicions are true
or products of his paranoia. The novel explores themes of love,
betrayal, memory, and the ambiguity of truth.

This project aims to track linguistic differences throughout the story,
focusing on *positive* and *negative* polarity words per chapter and
also near the word **Capitu**.

# Raw data overview

*Dom Casmurro* was published in 1899, which means it is a public domain
content. I was able to download it in PDF format through the [Brazilian
Ministry of Education
page](http://www.dominiopublico.gov.br/pesquisa/DetalheObraForm.do?select_action&co_obra=1888).
The book has **128 chapters**.

# Uploading data and using a POS-tagger

For the purpose of this project, I needed to transform the data into a
format that could be tagged. I first uploaded the book in PDF format,
then I transformed it into a tibble dataframe divided by page. The last
step before using the POS-tagger was to flatten the data so that the
sentences wouldn’t be segmented in different pages. Then, I downloaded a
Portuguese Language Model and annotated the data. There were some
columns that wouldn’t be very useful for this project, so I decided to
leave just the columns that would be useful for the analysis. Because
the `udpipe` package is not meant to tag books specifically, I had to
create a column to access each chapter.

``` r
# uploading the pdf format
dom_casmurro_pdf <- pdf_text("data/domCasmurro.pdf")

# tibble data into page
dom_casmurro_pages <- tibble(page = 1:length(dom_casmurro_pdf), text = dom_casmurro_pdf)

# flatten data into plain text
dom_casmurro_text <- str_flatten(dom_casmurro_pages$text)

# downloading model
port_model <- udpipe_download_model(language = "portuguese-bosque")
```

    ## Downloading udpipe model from https://raw.githubusercontent.com/jwijffels/udpipe.models.ud.2.5/master/inst/udpipe-ud-2.5-191206/portuguese-bosque-ud-2.5-191206.udpipe to /Users/luizadivino/Documents/Pitt Fall 2024/Data Science/Dom-Casmurro-Character_Description/portuguese-bosque-ud-2.5-191206.udpipe

    ##  - This model has been trained on version 2.5 of data from https://universaldependencies.org

    ##  - The model is distributed under the CC-BY-SA-NC license: https://creativecommons.org/licenses/by-nc-sa/4.0

    ##  - Visit https://github.com/jwijffels/udpipe.models.ud.2.5 for model license details.

    ##  - For a list of all models and their licenses (most models you can download with this package have either a CC-BY-SA or a CC-BY-SA-NC license) read the documentation at ?udpipe_download_model. For building your own models: visit the documentation by typing vignette('udpipe-train', package = 'udpipe')

    ## Downloading finished, model stored at '/Users/luizadivino/Documents/Pitt Fall 2024/Data Science/Dom-Casmurro-Character_Description/portuguese-bosque-ud-2.5-191206.udpipe'

``` r
# loading model
port <- udpipe_load_model(file = port_model$file_model)

# using the POS-tagger
dom_casmurro_pos <- udpipe::udpipe_annotate(port, x = dom_casmurro_text) %>% 
  as.data.frame()

# selecting columns that will be used
dom_casmurro_pos <- select(
  dom_casmurro_pos, 
  "paragraph_id",
  "sentence_id",
  "sentence",
  "token_id",
  "token",
  "lemma",
  "upos")
  
# creating variable chapter_id: "capítulo" means "chapter" in Portuguese
# I knew the chapter had 148 chapters and when using "count" it showed only 147, so I had to manually find out where the problem was
# I found that one of the chapters (in Portuguese, "Chapter" is "Capítulo")
dom_casmurro_pos <- dom_casmurro_pos %>%
  mutate(
    is_new_chapter = str_detect(sentence, "^CAPÍTULO") | str_detect(sentence, "CAPÍTULO XXXVI"),
    chapter_id = cumsum(is_new_chapter & sentence != lag(sentence, default = ""))
  )
```

# Transformed data overview

After transforming the data into the version I used for the analysis, I
was left with 17 different tags, 148 chapters, and 1510 paragraphs.

``` r
# different tags
unique(dom_casmurro_pos$upos)
```

    ##  [1] "ADP"   "NOUN"  "PUNCT" "PROPN" "NUM"   "VERB"  "ADV"   NA      "DET"  
    ## [10] "PRON"  "CCONJ" "AUX"   "ADJ"   "SCONJ" "INTJ"  "PART"  "X"     "SYM"

``` r
# number of chapters
unique(dom_casmurro_pos$chapter_id)
```

    ##   [1]   0   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17
    ##  [19]  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35
    ##  [37]  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53
    ##  [55]  54  55  56  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71
    ##  [73]  72  73  74  75  76  77  78  79  80  81  82  83  84  85  86  87  88  89
    ##  [91]  90  91  92  93  94  95  96  97  98  99 100 101 102 103 104 105 106 107
    ## [109] 108 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125
    ## [127] 126 127 128 129 130 131 132 133 134 135 136 137 138 139 140 141 142 143
    ## [145] 144 145 146 147 148

``` r
# number of paragraphs
n_distinct(dom_casmurro_pos$paragraph_id)
```

    ## [1] 1510

# Selecting which grammatical categories will be included for the content word count

When trying to access content words, grammatical categories like, for
example, verbs, prepositions, and articles are not included, so after
analyzing all the tags, I decided to select only nouns, proper names and
adjectives.

``` r
# selecting grammatical categories that are useful for content word count
dom_casmurro_pos_content_words <- dom_casmurro_pos %>%
  filter(upos == "PROPN" | upos == "NOUN" | upos == "ADJ") %>% 
  select(token, upos) %>% 
  mutate(token = str_to_lower(token)) 
```

# Content word analysis

First of all, I wanted to check what the most frequent content words
were.

``` r
# List with most frequent words, regardless of the grammatical category
dom_casmurro_pos_content_words %>% 
  count(token, sort = TRUE) %>% 
  head(10)
```

    ##       token   n
    ## 1    capitu 306
    ## 2       mãe 228
    ## 3      dias 192
    ## 4  capítulo 188
    ## 5      casa 170
    ## 6     olhos 164
    ## 7      josé 160
    ## 8       vez 141
    ## 9     tempo 120
    ## 10    padre 110

``` r
# List most frequent nouns
dom_casmurro_pos_content_nouns <- dom_casmurro_pos_content_words%>% 
  filter(upos == "NOUN") %>% 
  select(token) 

dom_casmurro_pos_content_nouns %>% 
  count(token, sort = TRUE) %>% 
  head(10)
```

    ##        token   n
    ## 1        mãe 224
    ## 2       casa 168
    ## 3      olhos 163
    ## 4        vez 141
    ## 5      tempo 119
    ## 6        dia 107
    ## 7      coisa  97
    ## 8  seminário  85
    ## 9       vida  84
    ## 10     padre  82

``` r
# List most frequent proper names
dom_casmurro_content_pos_propn <- dom_casmurro_pos_content_words %>% 
  filter(upos == "PROPN") %>% 
  select(token)

dom_casmurro_content_pos_propn %>% 
  count(token, sort = TRUE) %>% 
  head(10)
```

    ##       token   n
    ## 1    capitu 306
    ## 2      josé 160
    ## 3      dias 152
    ## 4  capítulo 146
    ## 5   escobar  84
    ## 6  bentinho  55
    ## 7   justina  55
    ## 8    glória  52
    ## 9      deus  49
    ## 10 ezequiel  47

``` r
# List most frequent proper names
dom_casmurro_content_pos_adj <- dom_casmurro_pos_content_words %>% 
  filter(upos == "ADJ") %>% 
  select(token)

dom_casmurro_content_pos_adj %>% 
  count(token, sort = TRUE) %>% 
  head(10)
```

    ##       token  n
    ## 1    grande 64
    ## 2     mesmo 51
    ## 3     mesma 50
    ## 4    melhor 49
    ## 5  primeira 39
    ## 6  primeiro 38
    ## 7   preciso 35
    ## 8     prima 35
    ## 9       bom 33
    ## 10    velho 32

``` r
# Wordcloud including content words that occurred more than 50 times, regardless of the grammatical category
dom_casmurro_pos_content_words %>% 
  count(token, sort = TRUE) %>% 
  filter (n > 50) %>% 
  ggplot(aes(label = token, size = n)) +
  geom_text_wordcloud() +
  scale_size_area(max_size = 10) + 
  theme_linedraw()
```

![](data_pipeline_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

# Sentiment Analysis

To find out if the there were more positive or negative polarity words
in different parts of the story, the first step was to do a sentiment
analysis. Then, I created two different dataframes: one containing words
with positive polarities and one with words with negative polarities.
Next, I grouped the words into chapters to track in which parts of the
story there were more positive or negative polarity terms.

``` r
# running the Sentiment Analysis
dom_casmurro_pos_sentiment_oplexicon_v3.0 <- dom_casmurro_pos %>% 
  inner_join(oplexicon_v3.0, by = c("token" = "term"),
             relationship = "many-to-many")

# finding out which grammatical categories remained
dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  count(upos, sort = TRUE) 
```

    ##     upos    n
    ## 1   VERB 2674
    ## 2    ADJ 2203
    ## 3   NOUN 1529
    ## 4  CCONJ  508
    ## 5    ADP  447
    ## 6    AUX  254
    ## 7    ADV  165
    ## 8   PRON  164
    ## 9  SCONJ   52
    ## 10   DET   32
    ## 11  <NA>   20
    ## 12   NUM    5
    ## 13 PROPN    4
    ## 14  INTJ    1

``` r
# counting words by polarity
dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  count(polarity) 
```

    ##   polarity    n
    ## 1       -1 2095
    ## 2        0 3484
    ## 3        1 2479

``` r
# selecting important columns
dom_casmurro_pos_sentiment_oplexicon_v3.0 <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  select(paragraph_id, sentence_id, chapter_id, sentence, token, upos, type, polarity)

# printing a small part of the dataframe
pander(head(dom_casmurro_pos_sentiment_oplexicon_v3.0, 10), caption = "dom_casmurro_pos_sentiment_oplexicon_v3.0")
```

| paragraph_id | sentence_id | chapter_id | sentence |
|:--:|:--:|:--:|:--:|
| 3 | 4 | 0 | Publicado originalmente pela Editora Garnier, Rio de Janeiro, 1899. |
| 5 | 6 | 1 | Uma noite destas, vindo da cidade para o Engenho Novo, encontrei no trem da Central um rapaz aqui do bairro, que eu conheço de vista e de chapéu. |
| 5 | 6 | 1 | Uma noite destas, vindo da cidade para o Engenho Novo, encontrei no trem da Central um rapaz aqui do bairro, que eu conheço de vista e de chapéu. |
| 5 | 6 | 1 | Uma noite destas, vindo da cidade para o Engenho Novo, encontrei no trem da Central um rapaz aqui do bairro, que eu conheço de vista e de chapéu. |
| 5 | 8 | 1 | A viagem era curta, e os versos pode ser que não fossem inteiramente maus. |
| 5 | 8 | 1 | A viagem era curta, e os versos pode ser que não fossem inteiramente maus. |
| 5 | 8 | 1 | A viagem era curta, e os versos pode ser que não fossem inteiramente maus. |
| 5 | 9 | 1 | Sucedeu, porém, que, como eu estava cansado, fechei os olhos três ou quatro vezes; tanto bastou para que ele interrompesse a leitura e metesse os versos no bolso. |
| 8 | 12 | 1 | — São muito bonitos. |
| 9 | 13 | 1 | Vi-lhe fazer um gesto para tirá-los outra vez do bolso, mas não passou do gesto; estava amuado. |

dom_casmurro_pos_sentiment_oplexicon_v3.0 (continued below)

|  token  | upos | type | polarity |
|:-------:|:----:|:----:|:--------:|
|   por   | ADP  |  vb  |    0     |
|  vindo  | VERB | adj  |    -1    |
|  rapaz  | NOUN | adj  |    0     |
|  vista  | NOUN | adj  |    1     |
|  curta  | ADJ  | adj  |    0     |
|   ser   | VERB |  vb  |    1     |
|  maus   | ADJ  | adj  |    -1    |
| cansado | ADJ  | adj  |    -1    |
| bonitos | ADJ  | adj  |    1     |
|  fazer  | VERB |  vb  |    0     |

``` r
# positive words
dom_casmurro_pos_sentiment_oplexicon_v3.0_positive <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  filter(polarity == "1")

dom_casmurro_pos_sentiment_oplexicon_v3.0_positive %>% 
  count(token, sort = TRUE) %>% 
  head(n = 10)
```

    ##      token   n
    ## 1      ser 167
    ## 2   melhor  82
    ## 3      ver  80
    ## 4       vi  50
    ## 5    amigo  42
    ## 6    mundo  37
    ## 7     alma  35
    ## 8  preciso  35
    ## 9      bom  33
    ## 10   certo  31

``` r
# grouping positive words by chapter
dom_casmurro_pos_sentiment_oplexicon_v3.0_positive_chapters <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>%
  filter(polarity == "1") %>%
  group_by(chapter_id) %>%
  count(polarity) %>%
  ungroup()

# negative words
dom_casmurro_pos_sentiment_oplexicon_v3.0_negative <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  filter(polarity == "-1")

dom_casmurro_pos_sentiment_oplexicon_v3.0_negative %>% 
  count(token, sort = TRUE) %>% 
  head(n = 10)
```

    ##      token   n
    ## 1      mas 507
    ## 2    pouco  70
    ## 3      dar  46
    ## 4    prima  39
    ## 5     cara  34
    ## 6   entrar  32
    ## 7     sair  32
    ## 8    longe  31
    ## 9   contar  24
    ## 10 sentido  20

``` r
# grouping negative words by chapter
dom_casmurro_pos_sentiment_oplexicon_v3.0_negative_chapters <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>%
  filter(polarity == "-1") %>%
  group_by(chapter_id) %>%
  count(polarity) %>%
  ungroup() 

# combining both positive and negative dataframes to create a plot with both
dom_casmurro_pos_sentiment <- bind_rows(
  mutate(dom_casmurro_pos_sentiment_oplexicon_v3.0_positive_chapters, Sentiment = "Positive"),
  mutate(dom_casmurro_pos_sentiment_oplexicon_v3.0_negative_chapters, Sentiment = "Negative")
)

# chapters with more positive words
dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Positive") %>%   
  group_by(chapter_id) %>%            
  summarise(total_positive_words = sum(n)) %>%  
  arrange(desc(total_positive_words)) 
```

    ## # A tibble: 147 × 2
    ##    chapter_id total_positive_words
    ##         <int>                <int>
    ##  1         18                   70
    ##  2        100                   57
    ##  3         25                   44
    ##  4         61                   43
    ##  5         41                   42
    ##  6         55                   39
    ##  7          9                   38
    ##  8         44                   38
    ##  9         32                   36
    ## 10         78                   36
    ## # ℹ 137 more rows

``` r
# chapters with more negative words
dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Negative") %>%   
  group_by(chapter_id) %>%               
  summarise(total_negative_words = sum(n)) %>%  
  arrange(desc(total_negative_words)) 
```

    ## # A tibble: 147 × 2
    ##    chapter_id total_negative_words
    ##         <int>                <int>
    ##  1         18                   60
    ##  2         67                   44
    ##  3         90                   38
    ##  4         25                   37
    ##  5         55                   36
    ##  6         44                   34
    ##  7        110                   33
    ##  8         41                   32
    ##  9         30                   30
    ## 10         56                   30
    ## # ℹ 137 more rows

``` r
# creating a plot with both positive and negative words in each chapter
ggplot(dom_casmurro_pos_sentiment, aes(x = chapter_id, y = n, fill = Sentiment)) +
  geom_bar(stat = "identity", position = "stack") +  
  labs(title = "Positive and Negative Words Distribution",
       x = "Chapters",
       y = "Number of Positive and Negative Words") +
  scale_fill_manual(values = c("Positive" = "#70c13f", "Negative" = "red")) +  
  theme_gray()
```

![](data_pipeline_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# top 5 chapters with the biggest amount of positive polarity words
top_positive_chapters <- dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Positive") %>%
  group_by(chapter_id) %>%
  summarise(total_positive_words = sum(n)) %>%
  arrange(desc(total_positive_words)) %>%
  slice_head(n = 5) %>%
  pull(chapter_id)

# top 5 chapters with the biggest amount of positive polarity words
top_negative_chapters <- dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Negative") %>%
  group_by(chapter_id) %>%
  summarise(total_negative_words = sum(n)) %>%
  arrange(desc(total_negative_words)) %>%
  slice_head(n = 5) %>%
  pull(chapter_id)

# combining both dataframes
top_sentiment_chapters <- dom_casmurro_pos_sentiment %>%
  filter(chapter_id %in% c(top_positive_chapters, top_negative_chapters))

# plot with both top 5 positive and negative
ggplot(top_sentiment_chapters, aes(x = factor(chapter_id), y = n, fill = Sentiment)) +
  geom_bar(stat = "identity", position = "stack") +
  labs(title = "Chapters with the biggest amount of positive polarity words X 
       Chapters with the biggest amount of negative polarity words",
       x = "Chapters",
       y = "Number of Positive and Negative Words") +
  scale_fill_manual(values = c("Positive" = "#70c13f", "Negative" = "red")) +
  theme_grey()
```

![](data_pipeline_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

# Words near Capitu

I was also interested to see which words occurred close to “Capitu” and
if those words were more positive or negative. I chose a range to keep
only words within 5 positions before or after. I also divided the data
into two different dataframes, one containing only negative polarity
words and the other containing only positive polarity words.

``` r
# selecting words within 5 positions before and after "Capitu"
dom_casmurro_pos <- dom_casmurro_pos %>%
  mutate(token_id_total = row_number())

context_capitu <- dom_casmurro_pos %>%
  filter(token == "Capitu") %>%
  select(token_id_capitu = token_id_total, chapter_id) %>%
  inner_join(dom_casmurro_pos, by = "chapter_id", relationship = "many-to-many") %>%
  filter(token_id_total >= token_id_capitu - 5 & token_id_total <= token_id_capitu + 5) %>%
  filter(token != "Capitu") %>%
  select(paragraph_id, chapter_id, token, token_id, token_id_total, token_id_capitu, upos)

# printing a small part of the dataframe
pander(head(context_capitu, 10), caption = "capitu_context")
```

| paragraph_id | chapter_id |  token   | token_id | token_id_total |
|:------------:|:----------:|:--------:|:--------:|:--------------:|
|      27      |     3      |   mal    |    6     |      1893      |
|      27      |     3      |   tem    |    7     |      1894      |
|      27      |     3      |  quinze  |    8     |      1895      |
|      27      |     3      |   anos   |    9     |      1896      |
|      27      |     3      |    .     |    10    |      1897      |
|      27      |     3      |   fez    |    2     |      1899      |
|      27      |     3      | quatorze |    3     |      1900      |
|      27      |     3      |    à     |   4-5    |      1901      |
|      27      |     3      |    a     |    4     |      1902      |
|      27      |     3      |    a     |    5     |      1903      |

capitu_context (continued below)

| token_id_capitu | upos  |
|:---------------:|:-----:|
|      1898       |  ADV  |
|      1898       | VERB  |
|      1898       |  NUM  |
|      1898       | NOUN  |
|      1898       | PUNCT |
|      1898       | VERB  |
|      1898       |  ADV  |
|      1898       |  NA   |
|      1898       |  ADP  |
|      1898       |  DET  |

``` r
# sentiment analysis with words near "Capitu"
sentiment_words_near_capitu <- context_capitu %>% 
  inner_join(lexiconPT::oplexicon_v3.0, by = c ("token" = "term"))

sentiment_words_near_capitu %>% 
  count(polarity)
```

    ##   polarity   n
    ## 1       -1  79
    ## 2        0 116
    ## 3        1  88

``` r
# positive words
sentiment_words_near_capitu_positive <- sentiment_words_near_capitu %>%
  filter(polarity == "1") %>%
  group_by(chapter_id) %>%
  count(polarity) %>%
  ungroup()

# negative words
sentiment_words_near_capitu_negative <- sentiment_words_near_capitu %>%
  filter(polarity == "-1") %>%
  group_by(chapter_id) %>%
  count(polarity) %>%
  ungroup()

# combining both positive and negative dataframes to create a plot with both
capitu_sentiment <- bind_rows(
  mutate(sentiment_words_near_capitu_positive, Sentiment = "Positive"),
  mutate(sentiment_words_near_capitu_negative, Sentiment = "Negative")
)

# creating a plot with both positive and negative words in each chapter
ggplot(capitu_sentiment, aes(x = chapter_id, y = n, fill = Sentiment)) +
  geom_bar(stat = "identity", position = "stack") +  
  labs(title = "Positive and Negative Words Distribution (Capitu)",
       x = "Chapters",
       y = "Number of Positive and Negative Words") +
  scale_fill_manual(values = c("Positive" = "#70c13f", "Negative" = "red")) +  
  theme_gray()
```

![](data_pipeline_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
# top 5 positive
capitu_top_positive_chapters <- capitu_sentiment %>%
  filter(Sentiment == "Positive") %>%
  group_by(chapter_id) %>%
  summarise(total_negative_words = sum(n)) %>%
  arrange(desc(total_negative_words)) %>%
  slice_head(n = 5) %>%
  pull(chapter_id)

# top 5 negative
capitu_top_negative_chapters <- capitu_sentiment %>%
  filter(Sentiment == "Negative") %>%
  group_by(chapter_id) %>%
  summarise(total_negative_words = sum(n)) %>%
  arrange(desc(total_negative_words)) %>%
  slice_head(n = 5) %>%
  pull(chapter_id)

#combining both
top_sentiment_capitu_chapters <- capitu_sentiment %>%
  filter(chapter_id %in% c(capitu_top_positive_chapters, capitu_top_negative_chapters))

# plot with both top 5 positive and negative
ggplot(top_sentiment_capitu_chapters, aes(x = factor(chapter_id), y = n, fill = Sentiment)) +
  geom_bar(stat = "identity", position = "stack") +
  labs(title = "Chapters with the biggest amount of positive polarity words near Capitu X 
       Chapters with the biggest amount of negative polarity words near Capitu",
       x = "Chapters",
       y = "Number of Positive and Negative Words") +
  scale_fill_manual(values = c("Positive" = "#70c13f", "Negative" = "red")) +
  theme_grey()
```

![](data_pipeline_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

# Words right after and right before “Capitu”

As a last part of the analysis, I decided to check which grammatical
categories most occurred before and after “Capitu”. In this case, I left
punctuation (`PUNCT`), articles (`DET`) and pronouns (`PRON`) out.

``` r
# words before "Capitu"
words_before_capitu <- dom_casmurro_pos %>%
  mutate(word_before = lag(token), pos_before = lag(upos), lemma_before = lag(lemma)) %>%
  filter((!pos_before == "PUNCT" & !pos_before == "DET" & !pos_before == "PRON") & token == "Capitu")  %>%
  select(paragraph_id, chapter_id, sentence, word_before, pos_before, lemma_before) %>% 
  mutate(word_before = str_to_lower(word_before))

words_before_capitu %>% 
  count(pos_before, sort = TRUE) 
```

    ##   pos_before  n
    ## 1        ADP 98
    ## 2       VERB 21
    ## 3      CCONJ 20
    ## 4      SCONJ 19
    ## 5        AUX  9
    ## 6        ADV  5
    ## 7      PROPN  3
    ## 8       NOUN  1

``` r
# words after "Capitu"
words_after_capitu <- dom_casmurro_pos %>% 
  mutate(word_after = lead(token), pos_after = lead(upos), lemma_after = lead(lemma)) %>% 
  filter(token == "Capitu" & (!pos_after == "PUNCT" & !pos_after == "DET" & !pos_after == "PRON")) %>% 
  select(paragraph_id, chapter_id, sentence, word_after, pos_after, lemma_after) %>% 
  mutate(word_after = str_to_lower(word_after))

words_after_capitu %>% 
  count(pos_after, sort = TRUE) 
```

    ##    pos_after  n
    ## 1       VERB 71
    ## 2        AUX 24
    ## 3      CCONJ 19
    ## 4        ADV 16
    ## 5        ADP  6
    ## 6      SCONJ  5
    ## 7       NOUN  4
    ## 8        ADJ  2
    ## 9        NUM  1
    ## 10     PROPN  1

``` r
# SAVING TABLES AS PNG FOR THE REPORT
library(flextable)
```

    ## 
    ## Attaching package: 'flextable'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     compose

``` r
library(webshot)

# polarity count
polarity_count <- dom_casmurro_pos_sentiment_oplexicon_v3.0 %>% 
  count(polarity)

polarity_table <- flextable(polarity_count)

save_as_image(polarity_table, path = "data/polarity_count.png")
```

    ## [1] "data/polarity_count.png"

``` r
# most positive words throughout the text
top_positive_chapters <- dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Positive") %>%   
  group_by(chapter_id) %>%            
  summarise(total_positive_words = sum(n)) %>%  
  arrange(desc(total_positive_words)) %>% 
  slice_head(n = 5)

polarity_table <- flextable(top_positive_chapters)

save_as_image(polarity_table, path = "data/top_positive_chapters.png")
```

    ## [1] "data/top_positive_chapters.png"

``` r
# most negative words throughout the text 
top_negative_chapters <- dom_casmurro_pos_sentiment %>%
  filter(Sentiment == "Negative") %>%
  group_by(chapter_id) %>%
  summarise(total_negative_words = sum(n)) %>%
  arrange(desc(total_negative_words)) %>%
  slice_head(n = 5)

polarity_table <- flextable(top_negative_chapters)

save_as_image(polarity_table, path = "data/top_negative_chapters.png")
```

    ## [1] "data/top_negative_chapters.png"

``` r
# polarity count capitu
count_polarity_capitu <-sentiment_words_near_capitu %>% 
  count(polarity)

count_polarity_capitu <- flextable(count_polarity_capitu)

save_as_image(count_polarity_capitu, path = "data/count_polarity_capitu.png")
```

    ## [1] "data/count_polarity_capitu.png"

``` r
# words before and after capitu
words_before_capitu_print <- words_before_capitu %>% 
  count(pos_before, sort = TRUE) 
words_before_capitu_table <- flextable(words_before_capitu_print)
save_as_image(words_before_capitu_table, path = "data/words_before_capitu.png")
```

    ## [1] "data/words_before_capitu.png"

``` r
words_after_capitu_print <- words_after_capitu %>% 
  count(pos_after, sort = TRUE)
words_after_capitu_table <- flextable(words_after_capitu_print)
save_as_image(words_after_capitu_table, path = "data/words_after_capitu.png")
```

    ## [1] "data/words_after_capitu.png"

``` r
# words near capitu (positive and negative)
capitu_top_positive_chapters <- capitu_sentiment %>%
  filter(Sentiment == "Positive") %>%
  group_by(chapter_id) %>%
  summarise(total_positive_words = sum(n)) %>%
  arrange(desc(total_positive_words)) %>%
  slice_head(n = 5) 

capitu_top_positive_chapters <- flextable(capitu_top_positive_chapters)

save_as_image(capitu_top_positive_chapters, path = "data/capitu_top_positive_chapters.png")
```

    ## [1] "data/capitu_top_positive_chapters.png"

``` r
# top 5 negative
capitu_top_negative_chapters <- capitu_sentiment %>%
  filter(Sentiment == "Negative") %>%
  group_by(chapter_id) %>%
  summarise(total_negative_words = sum(n)) %>%
  arrange(desc(total_negative_words)) %>%
  slice_head(n = 5)

capitu_top_negative_chapters <- flextable(capitu_top_negative_chapters)

save_as_image(capitu_top_negative_chapters, path = "data/capitu_top_negative_chapters.png")
```

    ## [1] "data/capitu_top_negative_chapters.png"

``` r
sessionInfo()
```

    ## R version 4.4.1 (2024-06-14)
    ## Platform: aarch64-apple-darwin20
    ## Running under: macOS 15.0
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/4.4-arm64/Resources/lib/libRblas.0.dylib 
    ## LAPACK: /Library/Frameworks/R.framework/Versions/4.4-arm64/Resources/lib/libRlapack.dylib;  LAPACK version 3.12.0
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## time zone: America/New_York
    ## tzcode source: internal
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] webshot_0.5.5     flextable_0.9.7   pander_0.6.5      patchwork_1.3.0  
    ##  [5] lexiconPT_0.1.0   ggwordcloud_0.6.2 udpipe_0.8.11     lubridate_1.9.3  
    ##  [9] forcats_1.0.0     stringr_1.5.1     dplyr_1.1.4       purrr_1.0.2      
    ## [13] readr_2.1.5       tidyr_1.3.1       tibble_3.2.1      ggplot2_3.5.1    
    ## [17] tidyverse_2.0.0   pdftools_3.4.1   
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] gtable_0.3.5            xfun_0.49               lattice_0.22-6         
    ##  [4] tzdb_0.4.0              vctrs_0.6.5             tools_4.4.1            
    ##  [7] generics_0.1.3          fansi_1.0.6             highr_0.11             
    ## [10] pkgconfig_2.0.3         Matrix_1.7-0            data.table_1.16.0      
    ## [13] uuid_1.2-1              lifecycle_1.0.4         compiler_4.4.1         
    ## [16] farver_2.1.2            textshaping_0.4.0       munsell_0.5.1          
    ## [19] fontquiver_0.2.1        fontLiberation_0.1.0    htmltools_0.5.8.1      
    ## [22] yaml_2.3.10             pillar_1.9.0            openssl_2.2.1          
    ## [25] fontBitstreamVera_0.1.1 commonmark_1.9.2        zip_2.3.1              
    ## [28] tidyselect_1.2.1        digest_0.6.37           stringi_1.8.4          
    ## [31] labeling_0.4.3          fastmap_1.2.0           grid_4.4.1             
    ## [34] colorspace_2.1-1        cli_3.6.3               magrittr_2.0.3         
    ## [37] utf8_1.2.4              withr_3.0.1             gdtools_0.4.1          
    ## [40] scales_1.3.0            timechange_0.3.0        rmarkdown_2.28         
    ## [43] officer_0.6.7           qpdf_1.3.4              askpass_1.2.0          
    ## [46] ragg_1.3.2              png_0.1-8               hms_1.1.3              
    ## [49] evaluate_0.24.0         knitr_1.48              markdown_1.13          
    ## [52] rlang_1.1.4             gridtext_0.1.5          Rcpp_1.0.13            
    ## [55] glue_1.8.0              xml2_1.3.6              rstudioapi_0.16.0      
    ## [58] R6_2.5.1                systemfonts_1.1.0
