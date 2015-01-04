---
layout: Post
title: Taste of Open Access - Part 2 - How to count the words
categories: []
tags: []
published: True

---

At the very beginning, I wanted to count the word frequencies by building a new WordCount class. But soon enough (and luckily enough), I found there are plenty of python libs to do this kind of job. After spending some time looking for the most appropriate lib, I landed on the [CounterVectorizer](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html) module of the [scikit-learn](http://scikit-learn.org/stable/index.html) package. The Scikit-learn package contains a big set of very useful tools for machine learning (well, honestly, as a guy with microelectronics background, I don’t quite get the exact meaning of this new fancy buzz word of the year. Anyway, I would think of it as a very powerful method to analyze a huge number of data, and that thinking suits me quite well so far). The amount of text (downloaded from Nature) that I am trying to analyze here is not huge, but it won’t hurt just to try implementing this Scikit-learn module and see what it will bring out.

A [tutorial](http://scikit-learn.org/dev/modules/feature_extraction.html#common-vectorizer-usage) is provided for this CountVectorizer module. So I tried to combine the samples in the tutorial with the data I downloaded. The codes for analyzing the downloaded json files can be found in [this link](http://scikit-learn.org/dev/modules/feature_extraction.html#common-vectorizer-usage).

The CountVectorizer comes quite handy. It requires only three lines to count the words in an text array:

{% highlight python %}
cv = CountVectorizer(min_df=1)
counts = cv.fit_transform([all_text]).toarray().ravel()
words = np.array(cv.get_feature_names())
{% endhighlight %}

the first line creates a new CountVectorizer class. The second gets the number occurance of each feature word in the all_text array, and the third line generates a list of all the feature words. And the rest of the job is to generate a table in the below form by filling the necessary information from `counts` and `words`.

|      | word1 | word2 | word3 | word4 | ... | wordn |
|:----:|:-----:|:-----:|:-----:|:-----:|:---:|:-----:|
| 2006 |   44  |   49  |   22  |   27  | ... |   78  |
| 2007 |   32  |   34  |   23  |   43  |     |   45  |
| 2008 |   56  |   67  |   34  |   34  |     |   54  |
| 20xx |  ...  |  ...  |  ...  |  ...  | ... |  ...  |


