---
layout: post
title: Language models with Naive Bayes
---

The model described below is a toy project aimed at describing how a Naive Bayes model works. It was initially made as part of a short presentation to a non-technical audience.

### What is Naive Bayes

With a language model, the question that we are trying to answer is - given that we know the first part of a sentence, what is the likelihood attributed to each word being te next word. For example, as a human, the partial sentence "Iphone's are made by ...", we would give a high likelihood to words like "Apple" or "robots" (in factories), and low likelihood to "toothpick", "red" or "serepticiously"
Naive Bayes tries to answer this question by turning it on its head. Instead of trying to answer the question, what is the most likely word given I know the last two words? It asks the subtley different what next word would most likely result in the two words that I know?

The difference comes about by applying Bayes rule to the problem. The left hand side of the equation is the first question,
whilst the top of the right hand side is the second question. Here, 'PW' refers to the known 'prior words' and 'Word' to
the word that we know.
> P(Word|PW) = P(PW|Word)*P(Word)/P(PW)

The idea is to calculate the Word with the highest probability. This change of viewpoint simplifies the maths, especially when coupled with the 'Naive' assumption. The 
Naive assumption is that the features are not correlated. Explicitly, the assumption is that the following
equation can be rewritten as follow:
> P(PW1 & PW2)=P(PW1)xP(PW2)

Taking the following example we can walk through how we would compare each word that might follow the next sentence. 
> 'Margaret's hen'... 

Lets assume that we have the following 2 possibilities:

| Word | P(word) | P(pw1 = 'Margaret's' / word)   | P(pw2 = 'hen' / word)   | Probability   |
|------|---------|--------------------------------|-------------------------|---------------|
| the  | 0.1     | 0.01                           | 0.0000001               |  1 x10**-9    |
|clucked| 0.01   |  0.01                          | 0.01                    |  1 x10**-6    |

The word 'clucked' is more likely than the word 'the' - mostly because it is not likely to see the phrase 'hen the'
would be very uncommon.

A Naive Bayes model needs to be trained with these conditional and overall probabilities.

### Implementing NB language model in SK Learn
A Naive Bayes model is not the best method for creating this type of predictor - its just a simple one. A key problem for impementation is about the dimensions of typical text data. Before any manipulation there are literally millions of different words within a corpus of texts.
## Curse of Dimensionality
The number of words is enormous, and causes an issue with dimensionality. To fully parameterise these conditional probabilities above, we would need to create a table of 1,000,000 x 1,000,000 probabilities.
SKLearn toolkit naively trained on the model will fail. Thankfully many word combinations are exceedingly rare - or have never been seen. For example, "chair rhubarb", "sulphurous decadence" or "haste incandescence" do not appear in the data, or probably any data - apart from this here. 

### Sparse matrices

Sparse matrices are exactly what they say they are - matrices that have mostly zero values. Instead of creating a large, full matrix,
these can be used to more efficiently store the data that you need. 

SciPy has a number of sparse matrices options that can be used for this type of problem.  The one that I found most useful is called the COO matrix. 

This you provide some COOrdinates (e.g. a row and column) and the value. 
``` python
from scipy import sparse
coo_matrix = sparse.coo_matrix(value, (row, col))
```

Using these sparse matrices, it was just a matter of counting the number of times that a word pair occurred e.g the count of ('hen', 'clucked') and diving by the number of times each word is used e.g. 'clucked'. 
An estimate of the probability of 'hen clucked' given 'clucked' is one divided by the other.

It was an instructive task to implement a sparse Naive Bayes model from scratch. The results can be found in my github repo
 [here](https://github.com/JRThurman01/WhatTheDickens)

### How succesful was the model?

Poor. The model was trained on Charles Dickens' back catalogue. I have used it to generate random passages of text. The model chooses the
next word randomly, based on the language models probabilities, derived from the prior two words. 
> master david s son what s fate for a bad un in the window accidentally broke my mother fondling me davy speak replied sam weller beguiled the way of which had the general of warm ma am i am waited for some harmless from my parents got a chariot one having the appointed of me and adams within a week or limitation permitted by u s federal laws and made the first old fox is a pretty or you want to spend the rest goes that wintry afternoon still she was dressed much soiled skeleton suit of the river anio diverted from

There are some sentence-like structures, but mostly just gibberish. Let me know your thoughts!!