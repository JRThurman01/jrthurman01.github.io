---
layout: post
title: Spell Checkers with Edit Distance
---

Text data can be messy even at the best of times. At the worst of times, it can become monstrous - and especially where the corpus is riddled with spelling errors.
There are a number of spell checkers and spelling correctors that are available in python. For this project, nothing was available that would work with my infrastructure limitations, and so 
I needed to write a simple spell checker myself

### Edit Distance

The concept of edit distance is simple - Its assigning some value to the distances between two strings. The closer the strings are together, the smaller the distance
For example, the strings 'flowers' and 'flwers' are almost identical - and it should be expected that they have a small edit distance. Conversely, 'flowers' and 'crayon' intuitively feel much further away.

This is where the concept of edit distance comes in. A famous example of edit distance is the Levenshtein distance. This is the smallest number of edits required
to convert one string to another string - where an edit is one of the following
* insert letter
* remove letter
* substitute letter

For example, to get from 'flowers' to 'flwers' (or vice versa) takes only one insert/remove letter.
Whilst the distance of 'flowers' and 'crayon' may not be obvious initially there are efficient algorithms available to calculate it (in this case the answer is 6)

So we have solved Spell Checking - If the word is not in the dictionary, then return the closest word...

Or not. To compare every error against millions of words would be very computationally expensive. Also, there will be times where a word is equal distance from two words. The error 'quise' is an edit distance of one from two words - 'quite' and 'quire' - one very popular, and the other almost unheard of. We should use this when selecting the most likely correct word.

### Spell checkers
There - I said it, most likely correct word. Its not the closest word we want, but the one that most likely resulted in that error.
Spell checkers effectively work in 2 stages. Firstly, a list of candidate words is generated that will be looked at more closely. Secondly, The likelihood of these candidate words is assessed against each other and, the most likely values chosen

**Candidate words**
Candidate words might not be quite an accurate term - but candidate collection of letter. For me, I generated all words that were edit distance upto 1 from the unknown word. I would take every letter and insert and replace each space and letter with it, aswell as removing each letter of the word.
For example, if my unknown word is flwers, I would start generating the list of words I need by INSERTING (aflwers, bflwers, ...), REPLACING (alwers, blwers, ...) and DELETING (lwers, fwers, ...)

Exactly how you generate these words is up to you, there are likely much more sophisticated methods available.

**Estimating Likelihood**
The first thing to do is remove all words in the candidates list that aren't in your dictionary.

If you have data on spelling corrections or a good quality language model you may be able to asses the likelihood of different words based on the spelling error, or the previous parts of the sentence. However, I did not have a trained model for my subject area. Instead I had a dictionary of words, and a frequency of each word. Not having a model specific situation is probably as common issue.
As a heuristic to an actual likelihood, determining a quasi-likelihood based on the frequency of each word in the dictionary is a possible simple method. The value with the maximum frequency that returned. In the case where I was looking at both edit distance of 1 and 2 from my word, estimating the likelihood of making 1 mistake vs. 2 mistakes and also using this in the estimated likelihoods would be able to cope with this.

### How succesful was the model?
Surprisingly good. One thing that I needed to do was to ignore anything shorter than 3 letters long, as there were some issues here. There were definately errors that got through, but overall the dataset was improved immensely. My data was based on addresses, and the relatively small number of unique streetnames, towns and cities probably helped. Let me know your thoughts!!