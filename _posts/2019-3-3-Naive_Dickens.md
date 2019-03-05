---
layout: post
title: Naive Bayes for high dimension classifiers - and what I learnt about Sparse matrices
---

# How many words are there?

I was asked to provide a four minute presentation to a non-technical audience about some of the things that data scientists might do. I had an idea that I wanted to demonstate how android could predict the next word for a text that is being typed by using a simple classifier. My object for my presentation was the following: 
* Solve a simple problem - get the audience to understand that machine learning isn't just robots and self driving cars
* Simple solution - Be able to explain the premise of how it works so that it is not a black box
* Highlight some areas the solution might not be perfect - I didn't want the audience to think data science meant following a process without thinking!

# Naive Bayes
I decided to implement a Naive Bayes model to determine the likelihood of the next word in a sentence given the prior 2. For example, if I was writing a text and have written 'I am' - what is the next word that I am likely to write?
I thought it was a nice example project because it fitted my objectives that I had for my project.

In particular, I liked that a Naive Bayes model would make some errors compared to Humans. In my presentation I asked the audience what they thought likely words might be if we knew just the word 2 before the word 'apple'. The responses were as expected - 'pie', 'sauce', 'fruit', 'orange'. After a while I went to the example of predicting the word 1 word after 'shares'. Again, the answers were as expected = 'his', 'pie', 'food', 'experiences'.
When I asked the word after the two words 'apple shares', people had a very different mindset - this was about apple share prices. The two words had a very different connotation when put together than seperatly. A key weakness of the naive model is its inability to distinguish interactions between the two input words.

# Implementing in SK Learn
The error that I made was thinking that this was going to be easy. It was a four minute presentation - I didnt want to spend hours writing a presentation for this. Therefore a simple model simply put together using SK Learn's Naive Bayes classifier would be the work of a minute
I was wrong. I was wrong because I failed to understand how SK learn and many other python libraries implement Naive Bayes. They create matrices of the conditional probabilities as a median step to calculating the most likely next words. Unfortunately I had over 400,000 unique words and so a quick bit of maths told me that 160,000,000,000 double floats would be required. At 8 bytes per element this would be just over 1TB or RAM required. No dice.

# Sparse matrices
Here, the vast majority of our matrix is 0 with only a few numbers available. Sparse matrices can be used to record only the non-zero elements and therefore save huge amounts of memory.
SciPy has a number of sparse matrices options that can be used for this type of problem.  The one that I found most useful is called the COO matrix. This you provide some COOrdinates (e.g. a row and column) and the value. I implemented my own Naive Bayes classifier from scratch to train my classifcaition for the nest

The code for the model can be found in here:

# Results
How succesful was the model?

I did not complete any validation on the model I made - It was only for a 4 minute presentation and was only for some fun, but overall it did do a nice job. Once my model was trained it provided some sensible suggestions. For example:
* After 'master david' - the model suggested 'copperfield' (Did I mention that I trained my model on the works of Charles Dickens?)

I also used this model for a Monte-Carlo markov chain. In doing so, it produced a random 100 word extract that looks like a Dickensian "Lorem Ipsum' generator. Judge for yourselves:

master david s son what s fate for a bad un in the window accidentally broke my mother fondling me davy speak replied sam weller beguiled the way of which had the general of warm ma am i am waited for some harmless from my parents got a chariot one having the appointed of me and adams within a week or limitation permitted by u s federal laws and made the first old fox is a pretty or you want to spend the rest goes that wintry afternoon still she was dressed much soiled skeleton suit of the river anio diverted from

It is unlikely to win the Booker prize but not wholly unsatisfying. Let me know your thoughts!!