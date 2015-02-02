---
layout: post
title: Taste of Open Access - Part 3 - Top 10 Most productive authors in Nature Nanotechnology
categories: []
tags: []
published: True
date: 2015-01-28 18:00:41
---

In the previous post, I have the downloaded all the meta information from Nature's Open Search API and saved them as json object arrays. And I have also counted the top 10 most used key words from these info. Now I would like to find out who are the most productive authors in Nature Nanotechnology.

As I have already got the meta data in hand, the mission is quite easy. I just loaded all the json files and search for the `dc:creators` keys where stores all the author names. There is some bug in Nature's API that sometimes there are two spaces instead of one between authors' first and last names. So I did a quick fix `author.replace('[space][space]', '[space]')` to change two spaces to one before logging the name.

{% highlight python %}
authorList = {}
for year in range(2006, 2014):
    fileName = 'nnano-' + str(year)
    json_data=open(fileName + '.json')
    data = json.load(json_data)
    
    for entry in data:
        if not entry['dc:creator']:
            continue
            
        for author in entry['dc:creator']:
            author = author.replace('  ', ' ')
            if not author in authorList.keys():
                authorList[author] = 1
            else:
                authorList[author] = authorList[author] + 1
{% endhighlight %}

After iterating through all the json files, I've got a dictionary `authorList` holding all the author names and their corresponding number of mentions throughout Nature Nanotechnology's publication from 2006 to 2014. Then I just sorted it out in a descending order and got a array of tuples `sortedAuthorList`.

{% highlight python %}
import operator
sortedAuthorList = sorted(authorList.items(), key=operator.itemgetter(1), reverse=True)
{% endhighlight %}

And of course, it is imaginable that some of the top names of the articles are Nature editors, not authors. So I just manually take away the editors' names ("Adarsh Sandhu", "Ai Lin Chun", "Tim Reid", "Jessica Thomas", "Peter Rodgers", "Owain Vaughan", "Stuart Cantrill", "Michael Segal", "Alberto Moscatelli", "Elisa De Ranieri", "Fabio Pulizzi", maybe I get some of the names wrong, as some names are not listed on Nature Nanotechnology's website), and the final author productivity list is like this:

{% highlight python %}
Phaedon Avouris       8
Felix Cheung          9
Charles M. Lieber    10
Michael S. Strano    10
Hongjie Dai          10
Richard Jones        16
Gemma Moxham         17
Samia Mantoura       18
Ros Portman          26
Chris Toumey         31
{% endhighlight %}

And a bar chart can make things look beautiful:

![top10 authors](/assets/images/top10productiveAuthorsNNano.png)

![top10 authors](/assets/images/top10productiveAuthorsNNanoBarStack.png)


