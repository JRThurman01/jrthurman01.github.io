---
layout: post
title: Naive Bayes for high dimension classifiers - and what I learnt about sparse matrices
---

## Creating a text prediction algorithm with Naive Bayes

I was asked to provide a four minute presentation to introduce what data science is to a group of non-technical students. 
Linear regression or A/B testing are definitely more common activities of a typical data scientist, but they lack the
excitement that other areas of data science can offer.

Image recognition, neural networks and reinforcement learning are far more enticing - but trying to provide a good 
introduction within 4 minutes is well beyond my capabilities as a communicator.

My idea instead was to use a Naive Bayes model to try and predict a message as it was being typed. The model would take 
the last two words and provide the most likely next word to be written. A balance between interesting and simple.

### What is Naive Bayes

Naive Bayes turns the question on its head. Instead of trying to answer the question:
> What is the most likely word given I know the last two words?

It asks a subtle different question:
> What next word would most likely result in the two words that I know?

The difference comes about by applying Bayes rule to the problem. The left hand side of the equation is the first question,
whilst the top of the right hand side is the second question. 
> P(Word|PriorWords) = P(PriorWords|Word)*P(Word)/P(Prior words)

This change of viewpoint simplifies the maths, especially when coupled with the 'Naive' assumption. The 
Naive assumption is that the features are not correlated. Explicitly, the assumption is that the following
equation can be rewritten as follow:
> P(PriorWord1 & PriorWord2)=P(PriorWord1)xP(PriorWord2)

Taking the following example we can walk through how we would compare each word that might follow the next sentence. 
> 'margaret's hen'... 

Lets assume that we have the following 2 possibilities:

* 'the'
* P('the') = 0.1
* P(Prior Word1 = 'Margaret' | Word= 'the') = 0.01
* P(Prior Word2 = 'hen' | Word='the') = 0.0000001
> P(PriorWords | Word) = 
> 0.1x0.01x0.0001 = 
> 0.0000001 (10 **-9)

* 'clucked'
* P('clucked') = 0.01
* P(Prior Word1 = 'Margaret' | Word= 'clucked') = 0.01
* P(Prior Word2 = 'hen' | Word='clucked') = 0.01
> P(PriorWords | Word) =
> 0.01x0.01x0.01 = 
> 0.000001 (10 **-6)

The word 'clucked' is more likely than the word 'the' - mostly because it is not likely to see the phrase 'hen the'
would be very uncommon.

A Naive Bayes model needs to be trained with these conditional and overall probabilities.

### Implementing in SK Learn
Word of warning - a Naive Bayes model is not the best method for creating this type of predictor - its just a simple one. 
I want to highlight a couple of issues:

* There are lots of different words - perhaps 1,000,000 in any 'corpus'. 
* It is Naive - it seems an unreasonable assumptions to assume that there is no 'interaction' between the two feature word

The number of words causes an issue with dimensionality. A common approach for encoding from a string to a number is called 'One hot encoding'.
This is where every element of a vector is set to zero, except for the one that applies to the word which is set to 1. We need a dimension
for every single word.

This dimensionality caused the SK Learn toolkit to crash when training the model. The SK Learn toolkit creates a matrix calculating every conditional
probability. E.g. For every 'prior word1' and 'Word' (now encoded to integers i, j) - the conditional probabilities are stored in a matrix.

It turns out that with 1,000,000 words, the amount of RAM needed to save this in memory would be measured in the Terabytes.
However, the vast majority of word combinations have zero probability. There is no evidence of the phrase 'chair hamster' or 
'swim beef' ever being uttered.

#### Note:
It is worth pointing out that there are a number of approaches that can be used to improve how words are transformed to numbers and vectors.
One approach is called 'Stemming' - taking just the first part of a word ('running' becomes 'run') to reduce the size of the vocab. Another is called 'word embedding' where
time has been spent to understand the semantics of the language, and can represent words in a much smaller vector space('Word2Vec' is a popular method)

## Sparse matrices

Sparse matrices are exactly what they say they are - matrices that have mostly zero values. Instead of creating a large, full matrix,
these can be used to more efficiently store the data that you need. 

SciPy has a number of sparse matrices options that can be used for this type of problem.  The one that I found most useful is called the COO matrix. 

This you provide some COOrdinates (e.g. a row and column) and the value. 
``` python
from scipy import sparse
coo_matrix = sparse.coo_matrix(value, (row, col))
```

It was an instructive task to implement a sparse Naive Bayes model from scratch. The results can be found in my github repo
 [here](https://github.com/JRThurman01/WhatTheDickens)

## How succesful was the model?

I do not think that it will be winning any competitions anytime soon. There were a number of very sensible suggestions and some others
that were a bit more curious. Better attention to cleaning the data could certainly have helped.

The model was trained on Charles Dickens' back catalogue. I have used it to generate random passages of text. It chooses the
next word probabilitly based on the prior two words. Let me know what you think!
> master david s son what s fate for a bad un in the window accidentally broke my mother fondling me davy speak replied sam weller beguiled the way of which had the general of warm ma am i am waited for some harmless from my parents got a chariot one having the appointed of me and adams within a week or limitation permitted by u s federal laws and made the first old fox is a pretty or you want to spend the rest goes that wintry afternoon still she was dressed much soiled skeleton suit of the river anio diverted from

It is unlikely to win the Booker prize but not wholly unsatisfying. Let me know your thoughts!!