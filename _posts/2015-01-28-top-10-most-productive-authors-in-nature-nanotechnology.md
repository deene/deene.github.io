---
layout: post
title: Top 10 most productive authors in Nature Nanotechnology
categories: []
tags: [Open Access, Python, Data Analysis]
published: True
date: 2015-01-28 18:00:41
---

In the [previous post]({% post_url 2015-01-05-taste-of-open-access--part-2--top-10-used-words-in-nature-nanotechnology %}), I have the downloaded all the meta information from Nature's [Open Search API](http://www.nature.com/developers/documentation/api-references/opensearch-api/) and saved them as json object arrays. And I have also counted the top 10 most used key words from these info. Now I would like to find out who are the most productive authors in Nature Nanotechnology.

As I have already got the meta data in hand, the mission is quite easy. I just load all the json files and search for the `dc:creators` keys where stores all the author names, and make sure that the `["prism:genre"]` is set as "Research" (Editors usually don't publish in this genre so this criteria should screen out most editorial articles). There is a bug in Nature's API that sometimes there are two spaces instead of one between authors' first and last names. So I did a quick fix `author.replace('[space][space]', '[space]')` to change two spaces to one before logging the name.

After iterating through all the json files using similar method as I used in my [previous post]({% post_url 2015-01-05-taste-of-open-access--part-2--top-10-used-words-in-nature-nanotechnology %}), I've got a dictionary `summaryTable` holding all the author names and their corresponding number of mentions throughout Nature Nanotechnology's publication from 2006 to 2014, like this:

{% highlight html %}
________Yi Cui  Charles M. Lieber  Hongjie Dai  Phaedon Avouris  Xiang Zhang  
2006       0                  1            1                0            0   
2007       1                  2            1                0            0   
2008       1                  0            3                1            1   
2009       0                  1            1                3            0   
2010       0                  0            1                1            1   
2011       1                  3            1                1            1   
2012       2                  0            1                1            0   
2013       1                  3            0                0            0   
2014       5                  0            0                0            3
...
{% endhighlight %}

Thus, the final top 10 is this:

{% highlight python %}
Yi Cui               11
Charles M. Lieber    10
Hongjie Dai           9
Phaedon Avouris       7
Xiang Zhang           6
Hagan Bayley          6
Michael S. Strano     6
Edward H. Sargent     5
Sumio Iijima          5
Peidong Yang          5
{% endhighlight %}

Figures can make things look beautiful, this is the count of total number of articles per author:

![top10 authors](/assets/images/top10productiveAuthorsNNano.png)

And this is the article number distribution by author and years:

![top10 authors](/assets/images/top10productiveAuthorsNNanoBarStack.png)

I recognize some of the big names in my field, such as Yi Cui, Charlse Lieber and Smio Ijjima. So, this analysis seems to have done a quite ok job. 

The complete (and ugly) code for generating the summaryTable can be found [here](https://gist.github.com/deene/bcdc9ad9915aababfedd).


