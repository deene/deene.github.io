---
layout: post
title: Taste of Open Access - Part 2 - Top 10 used words in Nature Nanotechnology
categories: []
tags: []
published: True
date: 2015-01-05 15:17:46
---

After the first part of my adventure into Nature's Open Search, I have got some pretty formatted json files sitting in my disk waiting to be further processed. Though data analysis is not where my super-power lies, this kind of hobbit project seems to be a good start for newbies like me anyway, isn't it?

At the very beginning, I just wanted to "simply" count the word frequencies by building a new WordCount class. But soon enough (and luckily enough), I realized that this task was not really as "simple" as it looks. In order to properly count the words, I need to take into account so many scenarios and exceptions, such as character encoding, lower vs. upper case, and process speed, etc, that soon this task became too complicated for me as amateur. Fortunately, I just found in time that there are plenty of python libs to do this kind of jobs. After spending some time looking for the most appropriate lib, I landed on the [CounterVectorizer](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html) module of the [scikit-learn](http://scikit-learn.org/stable/index.html) package. The Scikit-learn package contains a big set of very useful tools for machine learning (well, honestly, as a guy with microelectronics background, I don't quite get the exact meaning of this new fancy buzz word. Anyway, I would think of it as a very powerful method to analyze a huge number of data, and that thinking suits me quite well so far). Though the amount of text (downloaded from Nature) that I am trying to analyze here is not huge, it won't hurt just to try implementing this Scikit-learn module and see what it will bring out.

A [tutorial](http://scikit-learn.org/dev/modules/feature_extraction.html#common-vectorizer-usage) is provided for this CountVectorizer module. So I tried to combine the samples in that tutorial with the data in hand. The codes for analyzing the downloaded json files can be found in [this gist link](https://gist.github.com/deene/43c15e92624798ab4734).

The CountVectorizer module comes quite handy. It requires only three lines to count the words in an text array:

{% highlight python %}
cv = CountVectorizer(min_df=1)
counts = cv.fit_transform([all_text]).toarray().ravel()
words = np.array(cv.get_feature_names())
{% endhighlight %}

The first line creates a new CountVectorizer class. The second gets the number (`counts`) of occurrence of each feature word in the `[all_text]` array, which was the sum of all the `[dc:descripton]` keys in the json object array loaded from the file, and the third line generates a list (`words`) of all the feature words. By extracting the necessary information from `counts` and `words`, a `summaryTable` [DataFrame](http://pandas.pydata.org/pandas-docs/stable/dsintro.html#dataframe) is generated in the below table, containing the counts of each word for each year from 2006 to 2014.

{% highlight python %}
rows = range(2006, 2015)
summaryTable = pd.DataFrame(index=rows, columns=allTerms)

for col in allTerms:
    for row in rows:
        if col in resultByYear[str(row)].keys():
            summaryTable.loc[row][col] = resultByYear[str(row)][col]
{% endhighlight %}

|      | word1 | word2 | word3 | word4 | ... | wordn |
|:----:|:-----:|:-----:|:-----:|:-----:|:---:|:-----:|
| 2006 |   44  |   49  |   22  |   27  | ... |   78  |
| 2007 |   32  |   34  |   23  |   43  |     |   45  |
| 2008 |   56  |   67  |   34  |   34  |     |   54  |
| 20xx |  ...  |  ...  |  ...  |  ...  | ... |  ...  |

I did a trick here during the counting by employing a `ignore_list` variable. This variable contains a list of many common English words that has very little scientific meaning. These words were removed from the `summaryTable` when encountered in the counting. This variable is a pure manual work by me looking at the words in the counting result. In the future I plan to automatically generate such a list by screening a lot of English texts. Yet in this project, such a manually generated list should be sufficient.

{% highlight python %}
ignore_list = ('of', 'the', 'a', 'to', 'and', 'in', 'be', 'as', 'that', 'this', 'can', 'for', 'have', 'is', 'with', 'by', 'are', 'used', 'such', 'on', 'it', 'at', 'from', 'high', 'has', 'an', 'which', '(', ')', ',', 'made', 'its', 'new', '2013', '2012', '.', 'two', '-', 'researchers', 'been', 'or', 'using', 'state', '2014', 'not', 'than', 'into', 'their', 'our', 'because', 'under', 'both', 'over', 'other', 'based', 'these', 'show' 'non', 'more', 'also', 'but', 'between', 'however', 'here', 'we', 'most', 'times', 'recent', 'small', 'long', 'usually', 'overall', 'leading', 'led', 'consistent', 'realize', 'existing', 'produced', 'relatively', 'current', 'role', 'one', 'could', 'make', 'will', 'may', 'now', 'use', 'how', 'due', 'all', 'up', 'was', 'were')
{% endhighlight %}

Now I have finished the counting work - the `summaryTable` is what I am looking for. It contains the frequency data of all the words that appears in the abstracts of the Nature Technology articles from 2006 to 2014.

After I have got the big `summaryTable` containing all the word counts, I started to do some analysis on this table, looking forward to some interesting information.

First I just printed out the top 10 most referenced words from 2006 and 2014.

![image1](/assets/images/top10WordTrendNatureNano2006-14.png)

![image2](/assets/images/top10WordCountTableNatureNano2006-14.png)

From the figure we can see that the word 'spin' is rising significantly since 2008, and 'nanotube', on the other hand, has been declining dramatically since its peak in 2007. To my surprise, 'graphene' does not make it to the top 10. Isn't it the hottest topic over the last decade? Hmmm...
