---
layout: post
title: Taste of Open Access - Part 3 - Top 10 Most productive authors in Nature Nanotechnology
categories: []
tags: []
published: True
date: 2015-01-28 18:00:41
---

In the previous post, I have the downloaded all the meta information from Nature's Open Search API and saved them as json object arrays. And I have also counted the top 10 most used key words from these info. Now I would like to find out who are the most productive authors in Nature Nanotechnology.

As I have already got the meta data in hand, the mission is quite easy. I just loaded all the json files and search for the `dc:creators` keys where stores all the author names. There is a bug in Nature's API that sometimes there are two spaces instead of one between authors' first and last names. So I did a quick fix `author.replace('[space][space]', '[space]')` to change two spaces to one before logging the name.

{% highlight python %}
for year in range(2006, 2015):
    fileName = 'nnano-' + str(year)
    json_data=open(fileName + '.json')
    data = json.load(json_data)

    authorList = {}
    
    for entry in data:
        if not entry['dc:creator']:
            continue
        if entry["prism:genre"] != 'Research':
            continue
            
        for author in entry['dc:creator']:
            author = author.replace('  ', ' ')
            if author in editors:
                continue
            if not author in authorList.keys():
                authorList[author] = 1
            else:
                authorList[author] = authorList[author] + 1
{% endhighlight %}

After iterating through all the json files, I've got a dictionary `authorList` holding all the author names and their corresponding number of mentions throughout Nature Nanotechnology's publication from 2006 to 2014. I used the same Pandas functions, which I used to find the top 10 key words in my previous post, to find the top 10 most productive authors from this `authorList`. And of course, it is imaginable that some of the top names of the articles are Nature editors, not authors. So I just manually take away the editors names (`editors`). Maybe I get some of the names wrong, as some names are not listed on Nature Nanotechnology's website. Anyway, the not-so-precise final top 10 is this:

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

