Getting familiar with python nltk 3.0

Watch out for the notes in the [book](http://www.nltk.org/book/ch01.html) because
it will tell you to do something that doesn't work in 3.0


### concordance - Find usages of the word and include some surrounding context in the results
### similar - Find words used similarly...different writers use a word differently so you will get different results on different texts
### common_contexts - Find contexts shared between words
###dispersion_plot - Get positional information about some words
###generate - Generate some text in the same style...**not working** on NLTK 3.0
###count - How many times does a particular word occur in the text
###FreqDist - Count the words appearing in the text


>Now, let's calculate a measure of the lexical richness of the text. The next example shows us that the number of distinct words is just 6% of the total number of words, or equivalently that each word is used 16 times on average (remember if you're using Python 2, to start with from __future__ import division).

```python
len(set(text3)) / len(text3)
```


