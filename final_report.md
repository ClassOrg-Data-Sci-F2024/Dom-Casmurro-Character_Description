Final Report
================
LUIZA DIVINO (<lus120@pitt.edu>)
2024-12-17

# Introduction

## Summary

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
betrayal, memory, and the ambiguity of truth.With this project, I aimed
to track linguistic differences throughout the story, focusing on
*positive* and *negative* polarity words per chapter and also near the
word **Capitu**.

When I first had the idea of executing this project, the goal was to
identify lexical and structural patterns where the word **Capitu** was
used in order to verify if there were noticeable changes in the way
Bentinho described her from the beginning to the end of the story. My
initial hypothesis was that it would be possible to track changes in a
linear way from the beginning to the end of the book. Viana, V., Fausto,
F., & Zyngier, S. (2007) study, however, compared Machado de Assis’
style in *Dom Casmurro* with Dan Brown’s *The Da Vinci Code*. Their
research used Corpus Linguistics (CL) tools to identify frequent n-grams
that could describe both author’s styles. The results showed that *The
Da Vinci Code* contained more repeated n-grams than *Dom Casmurro*. This
study proved that Machado de Assis doesn’t use much formulaic language
in *Dom Casmurro*, which can explain way I was not able to find
linguistic patterns, so my first hypothesis was discarded. Kaufmann
(2020) used Multidimensional Analysis (MDA) to analyse linguistic
variation in Machado de Assis’s fictional prose, comparing the *Machado
de Assis Literary Corpus (CLIMA)*, containing 9 novels and 76 short
stories (859,521 words), and the *Comparable Literary Corpus (CLIC)*,
featuring works by 23 contemporary writers (3,386,037 words). In his
study, linguistic features and co-occurring words were examined to
identify functional and lexical dimensions of variation. One of the
stylistic dimensions found in Machado de Assis’ work was Sentimental
Oralized Narrative, which, according to the author, “injects more
orality and a contextual narrative into the text” and also “raises
questions of \[…\] and opposition” (Kaufmann, 2020, p. 211).

### Research questions

Based on Viana, V., Fausto, F., & Zyngier, S. (2007) and Kaufmann
(2020), I came up with research questions to base my analysis:

*(How)* Does the way Bentinho’s talk about Capitu evolve/change
throughout *Dom Casmurro*?

1.  What do the most frequent content words in the book suggest about
    its story?
2.  Are there more positive/negative words in specific parts of the
    story?
3.  Which grammatical categories co-ocurr more often with “Capitu” and
    what could it indicate?

### Hypothesis

- There are more positive words at the beginning than at the end.

- There are more negative words at the end than at the beginning.

# Steps

- For the purpose of this project, I needed to transform the data into a
  format that could be tagged. I first uploaded the book in PDF format,
  then I transformed it into a tibble dataframe divided by page. The
  last step before using the POS-tagger was to flatten the data so that
  the sentences wouldn’t be segmented in different pages. Then, I
  downloaded a Portuguese Language Model and annotated the data. There
  were some columns that wouldn’t be very useful for this project, so I
  decided to leave just the columns that would be useful for the
  analysis. Because the `udpipe` package is not meant to tag books
  specifically, I had to create a column to access each chapter.

- After transforming the data into the version I used for the analysis,
  I was left with 17 different tags, 148 chapters, and 1510 paragraphs.

- When trying to access content words, there are some grammatical
  categories like, for example, verbs, prepositions, and articles are
  not included, so after analyzing all the tags, I decided to select
  only nouns, proper names and adjectives.

- To find out if the there were more positive or negative polarity words
  in different parts of the story, the first step was to do a sentiment
  analysis. Then, I created two different dataframes: one containing
  words with positive polarities and one with words with negative
  polarities. Next, I grouped the words into chapters to track in which
  parts of the story there were more positive or negative polarity
  terms.

- I was also interested to see which words occurred close to “Capitu”
  and if those words were more positive or negative. I chose a range to
  keep only words within 5 positions before or after. I also divided the
  data into two different dataframes, one containing only negative
  polarity words and the other containing only positive polarity words.

- As a last part of the analysis, I decided to check which grammatical
  categories most occurred before and after “Capitu”. In this case, I
  left punctuation (`PUNCT`), articles (`DET`) and pronouns (`PRON`)
  out.

# Analysis

To analyse the data, I will answer questions a, b and c.

## Word count

**a. What do the most frequent content words in the book suggest about
its story?** The word that appears as the biggest one is “Capitu”. There
are also other proper names in the wordcloud (“Escobar”, “Bentinho”,
“José” + “Dias”, “Glória”, “Ezequiel”, and “Justina”), who are also
important characters in the story. There are also nouns (“Deus” = God,
“idéia” = idea, “seminário” = seminar, “dia” = day, “padre” = priest,
“rua” = road, “prima” = cousin, “anos” = years, “pé” = foot, “porta” =
door, “coisa” = thing, “verdade” = truth, “vida” = life, “homem” = man,
“cabeça” - head, “filho” = son, “palavras” = words, and “tempo” = time)

- The high frequency of Capitu’s name suggests she is frequently
  mentioned by Bentinho, emphasizing her role as the focus of his
  **attention**, **reflection**, and **obsession**, and it also reflects
  the narrative bias of the text, which is told from Bentinho’s point of
  view.

- Her constant mention might indicate Bentinho’s attempt to understand
  or justify his own insecurities and suspicions

<div style="text-align: center;">

<img src="data/wordcloud.png" style="width:60.0%;height:60.0%" />

</div>

## Sentiment Analysis

**b. Are there more positive/negative words in specific parts of the
story?**

Yes, there are. But they are not linear, as my first hypothesis
suggested. As we can see in the image below, it varies throughout the
story.The scattered sentiment might be reflecting the central theme of
doubt and uncertainty that runs through the novel.

<div style="text-align: center;">

<img src="data/positive_negative_plot.png"
style="width:60.0%;height:60.0%" />

</div>

Instead, I decided to take a closer look to the chapters with the most
positive polarity words and the most negative polarity words.

<div style="text-align: center;">

<img src="data/top_positive_negative_plot.png"
style="width:60.0%;height:60.0%" />

</div>

<div style="display: flex; justify-content: center;">

<img src="data/top_positive_chapters.png" width="20%" height="20%" style="margin-right: 10px;">
<img src="data/top_negative_chapters.png" width="20%" height="20%">

</div>

What really called my attention was that both positive and negative
polarity words were more frequent in chapter 18, titled “Um Plano (A
Plan), Bentinho experiences conflicting emotions as he grapples with the
promise to enter the seminary and his deepening love for Capitu. The
chapter takes on a dual emotional tone, combining hope and tension.
Bentinho and Capitu begin to discuss a plan to prevent him from going to
the seminary, creating a sense of optimism and complicity between them.
However, the conversation also introduces conflict: Capitu refers to
Bentinho’s mother, Dona Glória, as a”beata, carola, papa-missas!”
(roughly meaning “pious, churchy, mass-goer”), which offends Bentinho.
He feels contrariety and discomfort, as her words challenge his mother’s
religious devotion—a sacred part of his upbringing.

**Connection to Positive and Negative Polarity Words:**

- *Positive polarity:* The shared plan and the moments of closeness
  between Bentinho and Capitu evoke feelings of hope, unity, and
  affection, contributing to the chapter’s positive tone.

- *Negative polarity:* Capitu’s sharp words about Dona Glória and
  Bentinho’s resulting annoyance create tension. Additionally,
  Bentinho’s underlying anguish about his promise and the burden of his
  obligations add layers of emotional distress.

As for the words near Capitu, there were almost the same amount of
positive and negative polarity ones, which could symbolize Bentinho’s
fluctuating feelings of love, jealousy, suspicion, and obsession, making
Capitu a more enigmatic character.

<div style="text-align: center;">

<img src="data/count_polarity_capitu.png"
style="width:20.0%;height:20.0%" />

</div>

And we can see that they are also inconsistently distributed by chapter.

<div style="text-align: center;">

<img src="data/positive_negative_capitu_plot.png"
style="width:60.0%;height:60.0%" />

</div>

It is possible to say that Bentinho’s feelings toward Capitu are never
entirely clear, and this lack of a clear positive or negative trajectory
could mirror his conflicting emotions and the ambiguous nature of their
relationship. It possible that Bentinho is constructing Capitu’s image
over time. Rather than a linear build-up of emotions (positive to
negative), the feelings seem to be fragmented, showing how Bentinho’s
view of Capitu oscillates over time, based on his own emotional and
psychological state.

## Gramatical categories

**c. Which grammatical categories co-ocurr more often with “Capitu” and
what could it indicate?**

Before “Capitu” the most prominent category are the *Prepositions*, but
I will focus here on the words after “Capitu”. *Verbs* are the most
prominent category after “Capitu”, indicating that the focus of the
narrative is on the dynamic events, actions, and states associated with
Capitu. The narrative seems to shift slightly towards a less direct
description of her, where her role in the story is defined more by
**what she does** and the situations she finds herself in, rather than
by detailed descriptions of her appearance or qualities.

<div style="display: flex; justify-content: center;">

<img src="data/words_before_capitu.png" width="20%" height="20%" style="margin-right: 10px;">
<img src="data/words_after_capitu.png" width="20%" height="20%">

</div>

### Examples

- Então Capitu **abanava** a cabeça com uma grande expressão de
  desengano e melancolia, tanto mais de espantar quanto que tinha os
  cabelos realmente admiráveis; mas eu retorquia chamando-lhe maluca.
  (*Then Capitu waved her head with a great expression of
  disillusionment and melancholy, all the more surprising because she
  had really admirable hair; but I retorted by calling her crazy.*)

- Capitu **ria** alto, **falava** alto, como se me avisasse; eu
  continuava surdo, a sós comigo e o meu desprezo. (*Capitu laughed
  loudly, spoke loudly, as if to warn me; I remained deaf, alone with
  myself and my contempt.*)

- Capitu **tinha** os olhos no chão. (*Capitu had her eyes on the
  floor.*)

- Capitu **preferia** tudo ao seminário. (*Capitu preferred everything
  to the seminary.*)

- Capitu **fez** um gesto de impaciência. (*Capitu gestured
  impatiently.*)

- Não obstante, achei que Capitu **estava** um tanto impaciente por
  descer. (*Nevertheless, I thought Capitu was a little impatient to get
  downstairs.*)

- Capitu **refletia**, **refletia**, **refletia**… (*Capitu reflected,
  reflected, reflected…*)

# Conclusion and limitions

#### *(How)* Does the way Bentinho’s talk about Capitu evolve/change throughout *Dom Casmurro*?

The evolution of Bentinho’s description of Capitu does not follow a
clear, linear pattern. Instead, it fluctuates based on his perceptions,
emotions, and the narrative’s progression. The lack of a consistent
evolving pattern reflects the subjective nature of Bentinho’s
perspective. As the narrator of the story, his interpretations of
**Capitu** are shaped by his own biases, experiences, and mental state,
making it difficult to pinpoint a clear evolution in his descriptions.

# Limitations

The analysis focused on specific words (such as “Capitu”) might overlook
other words or aspects that could also be relevant to the study of the
text. Frequently occurring words may not necessarily be the most
significant for the interpretative analysis of the text. Frequency
analyses by categories (such as prepositions or verbs) may fail to
capture the stylistic and thematic depth of the narrative. Comparing Dom
Casmurro with other works by Machado could provide additional insights.

# References

Gonzaga, S. 2022. Package ‘lexiconPT’. *CRAN*. Retrieved from
<https://cran.r-project.org/web/packages/lexiconPT/lexiconPT.pdf>

Kauffmann, Carlos Henrique. *Linguística de corpus e estilo: análises
multidimensional e canônica na ficção de Machado de Assis*. 2020. PhD
diss., Programa de Estudos Pós-Graduados em Linguística Aplicada e
Estudos da Linguagem, Pontifícia Universidade Católica de São Paulo.

Schweinberger, Martin. 2023. *POS-Tagging and Syntactic Parsing with R*.
Brisbane: The University of Queensland. url:
<https://ladal.edu.au/pos.html> (Version 2023.05.31).

Viana, V., Fausto, F., & Zyngier, S. 2007. Corpus linguistics &
literature: A contrastive analysis of Dan Brown and Machado de Assis. In
S. Zyngier, V. Viana, & J. Jandre (Eds.), *Textos e leituras*: Estudos
empíricos de língua e literatura (pp. 233-256). Publit.

Wickham, H., & Grolemund, G. (n.d.). *R for Data Science: Data
visualization*. Retrieved from
<https://djvill.github.io/r4ds/data-visualisation.html>
