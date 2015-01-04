---
layout: Post
title: Taste of Open Access - Part 1 - How to download data from Nature
categories: []
tags: []
published: True
date: 2015-01-04 13:29:25
---

As a researcher who has always been interested in programming (as a hobbit besides the daily work in the labs), I found it particularly interesting that many big academic journal publishers are starting to provide APIs for their paper databases. This opens up a opportunity for everyone to do some analysis that might not be possible before.

So I pulled off a few days in the end of 2014 and composed the following little research.

First I looked into how I can get my hands on the data that are provided by Nature's API (I did not find similar APIs provided by Science, so I just started with Nature). With the help of search engines I quickly landed on the documentation of its [OpenSearch](http://www.nature.com/developers/documentation/api-references/opensearch-api/) API. The APIs are quite well documented, so after several hour's keystroking I managed to select and download the meta data of the articles that I am interested in (only the abstract and some author data. At the moment of this writing the full content is not available through the API).

You can find [here](https://gist.github.com/deene/3fbb0b02fbf7c3425dbf) the sample of the codes that I use to download the meta info of the articles published in Nature Nanotechnology from the mid of 2006 (the year when this journal was established) to Dec, 2014 (the date of this writing). After running this code in Python a few json files, named by year, will be generated. In the json file there contains an json object array of all the downloaded articles meta info. The python script, though looks quite verbose, has the only core statement below:

{% highlight python %}
url = 'http://api.nature.com/content/opensearch/request?&recordPacking=unpacked&queryType=cql&maximumRecords=100&httpAccept=application/json&query=' + queryWord

content = urllib2.urlopen(url).read()
{% endhighlight %}

The variable `url` is formulated according to Nature's API protocol. And the search result is retrieved by calling the `urllib2` package function `urlopen()`. The rest of the code snippet is just getting the information flow in the correct form.

In the output json file stores an array of all the entries found throughout one year. A loop sweeping the targeted search dates are constructed at the beginning of the snippet:

{% highlight python %}
for year in range(2008, 2009):
    fileName = 'nnano-' + str(year) + '.json'
    myfile = open(fileName, 'w+')
    yearWord = str(year)

    for month in range(1,2):
        if month < 10:
            monthWord =  '0' + str(month)
        else:
            monthWord = str(month)

        for day in range(1,12):
            if day<10:
                dayWord = '0' + str(day)
            else:
                dayWord = str(day)

            targetDate = yearWord + '-' + monthWord + '-' + dayWord
{% endhighlight %}

The response from the server (`content`) of each query looks like this:

{% highlight javascript %}
{
    "comment": "nature.com OpenSearch: urn:uuid:c9532f0e-1145-4f41-962a-8021b3c90484",
    "namespaces": {
        ...
    },
    "feed": {
        ...
    }
    ...
    "sru:numberOfRecords":11,
    ...
    "entry": [
    {
        "title" : "Computing power",
        ...
    }, {
        "title" : "Top down bottom up: Shared vision",
        ...
    }, ...]
    ...
}
{% endhighlight %}

This object has a very complex structure. Luckily, there are just a few keys that matters at this stage. The `sru:numberOfRecords` key tells us how many results are found in this search, and the `entry` key contains the array of all the returned result if there is any. By checking these two keys, the article meta info can there for be extracted:

{% highlight python %}
if data['feed']['opensearch:totalResults'] != 0:
                for entry in data['feed']["entry"]:
                    jsonObjArray.append(entry)
            else:
                print "No result found!"
{% endhighlight %}

So, at the end of this snippet, the `jsonObjArray` is dumped into a json file named by year, therefore make the data format-ready for further analysis.

In the next part, I will try to write about how to do simple word count on these downloaded data.

