Data Processing - Dom Casmurro
================
LUIZA DIVINO
2024-10-27

# Goal

Verify how Capitu and Escobar are described throughout the story, making
a comparison between how they were portrayed by Bentinho before and
after the apparent adultery

## Cleaning and reorganizing

- Load all the packages that will be needed for the analysis

- Import data: PDF (transform data to a df)

- Tokenize data: words

- Create columns: `text`, `word` and `page`

- Lowercase data

- Add stop wordlist without common words
  (<https://gist.github.com/alopes/5358189>) and create new dataset

### Load packages

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(tidytext)
library(pdftools)
```

    ## Using poppler version 23.04.0

``` r
library(tibble)
library (stringr)
library(dplyr)
# found about this package (could not use it)
library(lexiconPT)
library(tm)
```

    ## Loading required package: NLP
    ## 
    ## Attaching package: 'NLP'
    ## 
    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     annotate

### Import data and process data

The data is in PDF format, so first it is necessary to read the PDF file
using `pdf_text`.

``` r
dom_casmurro_pdf <- pdf_text("data/domCasmurro.pdf")

# use cat to have a preview of the first page
cat(dom_casmurro_pdf[1])
```

    ##                                   Dom Casmurro
    ## 
    ##                                                                              Texto de referência:
    ##                                                      Obras Completas de Machado de Assis, vol. I,
    ##                                                               Nova Aguilar, Rio de Janeiro, 1994.
    ## 
    ## 
    ##                                 Publicado originalmente pela Editora Garnier, Rio de Janeiro, 1899.
    ## 
    ## 
    ## 
    ## 
    ##                              CAPÍTULO PRIMEIRO
    ##                                  DO TÍTULO
    ## 
    ## Uma noite destas, vindo da cidade para o Engenho Novo, encontrei no trem da
    ## Central um rapaz aqui do bairro, que eu conheço de vista e de chapéu.
    ## Cumprimentou-me, sentou-se ao pé de mim, falou da Lua e dos ministros, e
    ## acabou recitando-me versos. A viagem era curta, e os versos pode ser que não
    ## fossem inteiramente maus. Sucedeu, porém, que, como eu estava cansado, fechei
    ## os olhos três ou quatro vezes; tanto bastou para que ele interrompesse a leitura e
    ## metesse os versos no bolso.
    ## 
    ## — Continue, disse eu acordando.
    ## 
    ## — Já acabei, murmurou ele.
    ## 
    ## — São muito bonitos.
    ## 
    ## Vi-lhe fazer um gesto para tirá-los outra vez do bolso, mas não passou do gesto;
    ## estava amuado. No dia seguinte entrou a dizer de mim nomes feios, e acabou
    ## alcunhando-me Dom Casmurro. Os vizinhos, que não gostam dos meus hábitos
    ## reclusos e calados, deram curso à alcunha, que afinal pegou. Nem por isso me
    ## zanguei. Contei a anedota aos amigos da cidade, e eles, por graça, chamam-me
    ## assim, alguns em bilhetes: "Dom Casmurro, domingo vou jantar com você”.—
    ## "Vou para Petrópolis, Dom Casmurro; a casa é a mesma da Renânia; vê se deixas
    ## essa caverna do Engenho Novo, e vai lá passar uns quinze dias comigo”.— "Meu
    ## caro Dom Casmurro, não cuide que o dispenso do teatro amanhã; venha e dormirá
    ## aqui na cidade; dou-lhe camarote, dou-lhe chá, dou-lhe cama; só não lhe dou
    ## moça”.
    ## 
    ## Não consultes dicionários. Casmurro não está aqui no sentido que eles lhe dão,
    ## mas no que lhe pôs o vulgo de homem calado e metido consigo. Dom veio por
    ## ironia, para atribuir-me fumos de fidalgo. Tudo por estar cochilando! Também não
    ## achei melhor título para a minha narração; se não tiver outro daqui até ao fim do
    ## livro, vai este mesmo. O meu poeta do trem ficará sabendo que não lhe guardo
    ## rancor. E com pequeno esforço, sendo o título seu, poderá cuidar que a obra é
    ## sua. Há livros que apenas terão isso dos seus autores; alguns nem tanto.
    ## 
    ## 
    ## 
    ##                                   CAPÍTULO II
    ##                                    DO LIVRO

Change format so that it is possible to access the data. Transforming
the pdf to tibble separates the whole content in each page as if they
were one single value. For it to be possible to analyse the data, it is
necessary to tokenize the corpus word by word, making it possible to
access in which page each word is located. This will leave us with two
variables: `page` and `word`.

Before going through this process, there were 128 observations (the book
has 128 pages). The tokenization leaves us with 669267 observations,
which represents the total number of words in the book, including
elements like “Chapter”, for example.

``` r
# data transformation
dom_casmurro_pages <- tibble(page = 1:length(dom_casmurro_pdf), text = dom_casmurro_pdf)

# data tokenization
dom_casmurro_words <- dom_casmurro_pages %>%
  unnest_tokens(word, text)

# naming the columns "page" and "word" for them to match the names of the columns on the stop wordlist

colnames(dom_casmurro_words) <- c("page", "word")
```

For future analysis, it is a good idea to lowercase the whole corpus.
For doing so, `str_to_lower_` will be used.

``` r
dom_casmurro_words <- dom_casmurro_words %>%
  mutate(word = str_to_lower(str_trim(word)))
```

Frequency list of words to have an overview of what have been used in
the book.

``` r
dom_casmurro_words %>% 
  count(word, sort = TRUE)
```

    ## # A tibble: 8,686 × 2
    ##    word      n
    ##    <chr> <int>
    ##  1 que    2684
    ##  2 a      2501
    ##  3 e      2186
    ##  4 de     1975
    ##  5 o      1695
    ##  6 não    1531
    ##  7 me     1035
    ##  8 se      858
    ##  9 um      774
    ## 10 é       718
    ## # ℹ 8,676 more rows

### Looking for context in which ““Capitu” and “Escobar”

#### Capitu

As a starting point, I have focused on “Capitu”, since her name is the
first content word that shows on the list with the most frequent words
(I had to look for it manually after using `count()`).

“Finding” Capitu `str_view()` gives the whole page. This is not
practical because it gives to much information, but it is a good
mechanism to look for more context when needed.

Context after Capitu (up to 10 words) I tested a lot of different
lengths and I might go with 5.

``` r
# str_extract_all gives access to all
# filter(is.na!) removes entries without capitu

dom_casmurro_pages %>%
  mutate(word_after_capitu = str_extract_all(text, "Capitu(\\s+\\w+){5}")) %>%
  unnest(word_after_capitu) %>% 
  filter(!is.na(word_after_capitu)) %>%
  select(word_after_capitu) %>%
  print()
```

    ## # A tibble: 79 × 1
    ##    word_after_capitu                                 
    ##    <chr>                                             
    ##  1 "Capitu fez quatorze\nà semana passada"           
    ##  2 "Capitu abanava a cabeça com uma"                 
    ##  3 "Capitu um dia notou a diferença"                 
    ##  4 "Capitu estava ao pé do\nmuro"                    
    ##  5 "Capitu tinha os olhos no chão"                   
    ##  6 "Capitu queria saber que notícia era"             
    ##  7 "Capitu não parecia crer nem\ndescrer"            
    ##  8 "Capitu gostava tanto de minha mãe"               
    ##  9 "Capitu estava agora particularmente nas lágrimas"
    ## 10 "Capitu quis que lhe repetisse as"                
    ## # ℹ 69 more rows

Context before Capitu (testing with 5)

``` r
dom_casmurro_pages %>%
  mutate(word_before_capitu = str_extract_all(text, "(\\b\\w+\\s+){5}Capitu")) %>%
  unnest(word_before_capitu) %>% 
  filter(!is.na(word_before_capitu)) %>%
  select(word_before_capitu) %>%
  print()
```

    ## # A tibble: 77 × 1
    ##    word_before_capitu                                                 
    ##    <chr>                                                              
    ##  1 "Com que então eu amava Capitu"                                    
    ##  2 "acordar com o\npensamento em Capitu"                              
    ##  3 "para legitimar a resposta de Capitu"                              
    ##  4 "desejo era ir atrás de Capitu"                                    
    ##  5 "se obedeciam às recomendações de Capitu"                          
    ##  6 "ter seguido o\nconselho de Capitu"                                
    ##  7 "Não confiaria esta idéia a Capitu"                                
    ##  8 "Iria contar\nestas esperanças a Capitu"                           
    ##  9 "XXXI\n                        AS CURIOSIDADES DE CAPITU\n\nCapitu"
    ## 10 "César acendia os olhos de Capitu"                                 
    ## # ℹ 67 more rows

Context before and after Capitu

``` r
dom_casmurro_pages %>%
  mutate(context_around_capitu = str_extract_all(text, "(\\b\\w+\\s+){5}Capitu(\\s+\\w+){5}")) %>%
  unnest(context_around_capitu) %>% 
  filter(!is.na(context_around_capitu)) %>%
  select(context_around_capitu) %>%
  print()
```

    ## # A tibble: 14 × 1
    ##    context_around_capitu                                                        
    ##    <chr>                                                                        
    ##  1 "os olhos de ressaca de Capitu deixassem de\ncrescer para mim"               
    ##  2 "com aquela\ngrande dissimulação de Capitu que não vi mais nada"             
    ##  3 "estada debaixo da janela de Capitu e a passagem de\num"                     
    ##  4 "perguntar o que havia entre Capitu e os\nperaltas do bairro"                
    ##  5 "à sala e disse a Capitu que a filha chamava por"                            
    ##  6 "Antes\nde examinar se efetivamente Capitu era parecida com o retrato"       
    ##  7 "XCVI\n                               UM SUBSTITUTO\n\nExpus a Capitu a idéi…
    ##  8 "o favor de perguntar se Capitu não daria uma boa\nesposa"                   
    ##  9 "a causa da impaciência de Capitu eram os sinais exteriores do"              
    ## 10 "padecer com a desatenção de Capitu e ter ciúmes do mar"                     
    ## 11 "que também foi passar com Capitu os primeiros dias e noites"                
    ## 12 "ou a necessidade de afligir Capitu com a minha grande demora"               
    ## 13 "não seria melhor esperar que Capitu e o filho\nsaíssem para"                
    ## 14 "resto é saber se a Capitu da Praia da Glória já"

### Use of stop wordlist

To make it easier to access target words (`Capitu` and `Escobar` at an
early stage of the analysis), a stop word list will be added in order to
eliminate common words. First, I tried to include an external stop word
list. For some reason, when I tried to get a new ds filtering only the
words that were not included in that list, I was getting weird results.
It did show that I had fewer observations, which was the expected, but
when I went to check the new ds, it still contained words that were on
the list. After trying different approaches and doing some research on
different packages, I discovered there is a Portuguese stop word list
included in the `tm` package which seemed to have all the same words I
wanted to add with that other stop word list, so I decided to use this
one.

``` r
# check stopwords in pt
stopwords("pt")
```

    ##   [1] "de"           "a"            "o"            "que"          "e"           
    ##   [6] "do"           "da"           "em"           "um"           "para"        
    ##  [11] "com"          "não"          "uma"          "os"           "no"          
    ##  [16] "se"           "na"           "por"          "mais"         "as"          
    ##  [21] "dos"          "como"         "mas"          "ao"           "ele"         
    ##  [26] "das"          "à"            "seu"          "sua"          "ou"          
    ##  [31] "quando"       "muito"        "nos"          "já"           "eu"          
    ##  [36] "também"       "só"           "pelo"         "pela"         "até"         
    ##  [41] "isso"         "ela"          "entre"        "depois"       "sem"         
    ##  [46] "mesmo"        "aos"          "seus"         "quem"         "nas"         
    ##  [51] "me"           "esse"         "eles"         "você"         "essa"        
    ##  [56] "num"          "nem"          "suas"         "meu"          "às"          
    ##  [61] "minha"        "numa"         "pelos"        "elas"         "qual"        
    ##  [66] "nós"          "lhe"          "deles"        "essas"        "esses"       
    ##  [71] "pelas"        "este"         "dele"         "tu"           "te"          
    ##  [76] "vocês"        "vos"          "lhes"         "meus"         "minhas"      
    ##  [81] "teu"          "tua"          "teus"         "tuas"         "nosso"       
    ##  [86] "nossa"        "nossos"       "nossas"       "dela"         "delas"       
    ##  [91] "esta"         "estes"        "estas"        "aquele"       "aquela"      
    ##  [96] "aqueles"      "aquelas"      "isto"         "aquilo"       "estou"       
    ## [101] "está"         "estamos"      "estão"        "estive"       "esteve"      
    ## [106] "estivemos"    "estiveram"    "estava"       "estávamos"    "estavam"     
    ## [111] "estivera"     "estivéramos"  "esteja"       "estejamos"    "estejam"     
    ## [116] "estivesse"    "estivéssemos" "estivessem"   "estiver"      "estivermos"  
    ## [121] "estiverem"    "hei"          "há"           "havemos"      "hão"         
    ## [126] "houve"        "houvemos"     "houveram"     "houvera"      "houvéramos"  
    ## [131] "haja"         "hajamos"      "hajam"        "houvesse"     "houvéssemos" 
    ## [136] "houvessem"    "houver"       "houvermos"    "houverem"     "houverei"    
    ## [141] "houverá"      "houveremos"   "houverão"     "houveria"     "houveríamos" 
    ## [146] "houveriam"    "sou"          "somos"        "são"          "era"         
    ## [151] "éramos"       "eram"         "fui"          "foi"          "fomos"       
    ## [156] "foram"        "fora"         "fôramos"      "seja"         "sejamos"     
    ## [161] "sejam"        "fosse"        "fôssemos"     "fossem"       "for"         
    ## [166] "formos"       "forem"        "serei"        "será"         "seremos"     
    ## [171] "serão"        "seria"        "seríamos"     "seriam"       "tenho"       
    ## [176] "tem"          "temos"        "tém"          "tinha"        "tínhamos"    
    ## [181] "tinham"       "tive"         "teve"         "tivemos"      "tiveram"     
    ## [186] "tivera"       "tivéramos"    "tenha"        "tenhamos"     "tenham"      
    ## [191] "tivesse"      "tivéssemos"   "tivessem"     "tiver"        "tivermos"    
    ## [196] "tiverem"      "terei"        "terá"         "teremos"      "terão"       
    ## [201] "teria"        "teríamos"     "teriam"

``` r
# trying another package (tm)
dom_casmurro_words_filtered <- dom_casmurro_words %>%
  filter(!word %in% stopwords("pt"))

# word count without common words
dom_casmurro_words_filtered %>% 
  count(word, sort = TRUE)
```

    ## # A tibble: 8,518 × 2
    ##    word         n
    ##    <chr>    <int>
    ##  1 é          718
    ##  2 capitu     341
    ##  3 mãe        229
    ##  4 dias       192
    ##  5 tudo       189
    ##  6 capítulo   188
    ##  7 disse      184
    ##  8 casa       170
    ##  9 ser        168
    ## 10 olhos      164
    ## # ℹ 8,508 more rows

By using these stopwords, I could see more how `Capitu` is the most none
common word used throughout the book.

## Saving data

``` r
write_csv(dom_casmurro_pages, "data_pages.csv")

write_csv(dom_casmurro_words, "data_words.csv")
```

## Experiment with POS tagger

### Download packages

``` r
#install.packages("udpipe")
#install.packages("flextable")
#install.packages("here")
# install klippy for copy-to-clipboard button in code chunks
#install.packages("remotes")
#remotes::install_github("rlesur/klippy")
```

### Load packages

``` r
#library(udpipe)
#library(flextable)
# activate klippy for copy-to-clipboard button
#klippy::klippy()
```

### Downloading the pt-model

For some reason, it says on the website that there is a model for pt-br,
but when I try to download it, it says it does not exist. Since this is
and older text, perhaps it might also works with pt-pt since there are
some small differences.

``` r
#m_port   <- udpipe_download_model(language = "portuguese-gsd")
```

### Load models

``` r
#m_port <- udpipe_load_model(file = here::here("udpipemodels", "portuguese-gsd-ud-2.5-191206.udpipe"))
```
